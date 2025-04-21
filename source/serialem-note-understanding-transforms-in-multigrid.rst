.. _Understanding_Transforms_in_Multigrid:

SerialEM Note: Understanding Transforms in Multigrid Operation
==============================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: Apr 21, 2025
:Last_Updated: Apr 21, 2025

.. glossary::

   Abstract
      The current design of Multigrid in SerialEM is not fully automated yet, but it follows a structured three-step process: LMM (Low-Magnification Mapping), 
      MMM (Medium-Magnification Mapping), and final data acquisition. Each grid is reloaded at least twice during this process.
        
      Typically, LMM maps are collected for all grids in a single automated run. The system loads each grid and acquires its LMM map. At this stage, "good"        
      mesh areas are selected using Navigator point items or polygons on each grid’s LMM map.
        
      Following that, MMM maps are collected—again, automatically—for all grids. Holes are then defined on each MMM map, and the program is instructed to          
      collect all the corresponding images automatically.
        
      While this workflow sounds straightforward, complications arise due to physical changes that occur when grids are reloaded. Reloading a grid
      inevitably introduces some degree of rotation and translation, which means previously defined Navigator items may no longer align correctly. It's   
      critical to ensure these items remain usable without having to acquire new maps. This is a key aspect of maintaining efficiency and consistency in the       
      workflow.
        
      Things become even more complex when using multi-hole acquisition with Multigrid. Each grid may differ significantly in geometry, requiring accurate 
      Image Shift vectors to be determined automatically for each grid — and sometimes even for each map — without manual intervention.
        
      How is all of this achieved? In this note, I want to share my understanding of the process, so that new users can better grasp what's happening 
      behind the scenes and avoid treating it as a black box.  
 

.. _marker_shift:


Understanding **Shift To Marker** in SerialEM
---------------------------------------------

The **Shift To Marker** operation is not unique to Multigrid workflows. It becomes relevant whenever there’s a mismatch between different magnifications—such 
as when LMM is collected using the LD_Search (S) beam at 110X and MMM is collected using the LD_View (V) beam at 2000X. In such cases, the same image feature 
typically won’t align between S and V magnifications. This discrepancy is often due to misalignment between these two magnification settings in the microscope.

**Shift To Marker** addresses this issue by adjusting the coordinates of all items in the LMM map at S magnification so that a selected feature is centered at 
V magnification. After applying the shift, you’ll notice that the X and Y coordinates of the LMM map itself are no longer at (0, 0). This adjustment is a simple 
coordinate transformation and does not involve creating registration points.

It’s important to note that this is purely a coordinate shift—it does not affect the microscope’s image shift, nor does it introduce any rotation.

A common mistake when using **Shift To Marker** is failing to select the correct Navigator item. Only the intended item on the LMM map should be highlighted 
in the Navigator window. If another unrelated item is selected, the shift will be applied incorrectly, resulting in misaligned coordinates.

Multigrid workflows also make use of this transformation. As such, the shift value should be determined in advance and verified for accuracy. Fortunately, 
SerialEM tracks whether a map has had the Marker Shift applied, helping to avoid confusion and ensuring consistent results across the process.

.. _note::

    An alternative approach to handling the misalignment between LD_Search (S) and LD_View (V) magnifications is to apply an Image Shift. In SerialEM, it’s      
    possible to define LD_Search with a specific Image Shift offset, so that every shot taken with LD_Search includes this predefined shift. When properly      
    configured, this method can make the image features captured at S and V magnifications appear aligned.

    This technique avoids the need to shift coordinates in the Navigator, since the actual beam/image position is adjusted instead. It provides a more           
    seamless alignment across magnifications without altering Navigator item positions.

    I believe this is the method used in EPU, where image shift alignment between search and view modes is handled.

.. _Realign_Reloaded_Grid_transform:

Realign Reloaded Grid Transform
-------------------------------

After a grid is realoded on the stage, **Multigrid** will compute rotation and translational shift bewteen the old and new stage positions. A 
two-dimensional transformation matrix and offset are obtained and written to LMM map as one of its navigator properties. Below is such entry 
in nav file for this LMM map.

.. code-block:: python
   :caption: GridMapXform entry for LMM map

   ...
   GridMapXform = 0.997785 0.0665214 -0.0665214 0.997785 -11.1591 -15.8475
   ...

The porgram does this using 5 best locations from LMM map and comparing them one-by-one for before and after the grid reloading. And it applies 
this tranform to all the nav items previously defined in LMM map. The LMM map overview image and all the items on it look the same, but all the 
coodinates are updated and they are all GOOD for current grid position on the stage. No new LMM is required. 

This procedure is called **Realign to Reloaded Grid**. It happens automatically after reloading (via multigtid operation) is done. If you want 
to realign it again manually, there is also a button ``Realign to Map`` in **Multiple Grid Opeations** dialog window for you to do so. After 
each reloading, it will obtain and update the transformation martix of "GridMapXform" compared to the last round. All the coordinate values in 
the nav file are updated - they have been evolving to cope with new grid stage position, although the initial LMM map overview image still 
looks the same. 

As you can see, this transform is essential to multigrid operation. It relys on LMM map. One cannot skip first step - LMM map and directly 
jump into MMM step. 

.. _Multishot_in_multigrid:

Multishot in Multigrid 
----------------------

One of the key features for multishot procedure is the fact that final the Image Shift vectors can be obtained from the hole vectors and an 
ajustment transform. As mentioned in other SerialEM note, if hole finder routine is performed on a V image, the hole vectors become availble. 
One can simply presse the button to Use ``Last Hole Vectors``, a set of "rough" Image Shift vectors are obtained from the transformation of 
hole vectors. If we perform hole finding routine on a MMM map overview, this "rough" Image Shift vectors are obtained and the information is 
also recorded as one of the properties for the MMM map nav item. See below:

.. code-block:: python
   :caption: IS vectors for MMM map

   HoleISXspacing = -1.40096 2.16152 0
   HoleISYspacing = -2.17058 -1.41177 0

Thus, every MMM maps can contain such information in nav file. 

If one performs ``StepTo & Adjust``, not only the final accurate IS vectors for high mag is availbe, but also the adjustment transform! This 
adjustment transform is kept in user's setting file like below. 

.. code-block:: python
   :caption: Adjustment Transform IS vectors

   HoleAdjustXform -37 0 0 18 35 0.918684 0.015073 0.000718 0.926858

This is for between View and Record beams, it is stable and doesn't change with grid. 

Multgrid procedure will get the "rough" Image Shift vectors stored for each MMM map, and combines that with "HoleAdjustXform" to have final 
Image Shift vectors for data acquisition. This is done dynamically during multigrid operation. 

.. _hole_vectors_transform:

Hole Vectors are Also Tranformed with Reloading
-----------------------------------------------

When we do hole finding on all MMM maps, we got the good holes postions, AND we got hole vectors. However, when we do final data acquisition, 
the grid will be reloaded it again. We already know the positions will be fine due to "GridMapXform", but what about hole vectors, are they 
still be good? The answer is YES. The "HoleISXspacing" and "HoleISYspacing" lines in nav file will get updated too. 

If you display the multishot pattern which was obtained based on MMM map image before reloading, NOW the pattern looks off on the old MMM map. 
And if you take a fresh LD_View shot and display current pattern on that, it fits nicely. This means even the hole vectors was determined over 
old MMM map, they actually got updated when grid is reloaded. 

SerialEM has various measures to ensure the information at each reloading well bookkept. It includes nav entries for "OrigReg" and "Regis" etc..




















