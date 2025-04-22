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
      behind the scenes and avoid treating it as a black box. I am certain there are things not correct or accurate, but I hope this helps.  
 

.. _marker_shift:

Shift To Marker Transform
-------------------------

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

.. note::
   An alternative approach to handling the misalignment between LD_Search (S) and LD_View (V) magnifications is to apply an Image Shift. In SerialEM, it’s      
   possible to define LD_Search with a specific Image Shift offset, so that every shot taken with LD_Search includes this predefined shift. When properly      
   configured, this method can make the image features captured at S and V magnifications appear aligned.

   This technique avoids the need to shift coordinates in the Navigator, since the actual beam/image position is adjusted instead. It provides a more           
   seamless alignment across magnifications without altering Navigator item positions.

   I believe this is the method used in EPU, where image shift alignment between search and view modes is handled. 

.. _Realign_Reloaded_Grid_transform:

Realign Reloaded Grid Transform
-------------------------------

After a grid is reloaded onto the stage, Multigrid computes both a rotational and translational shift between the previous 
and current stage positions. This results in a two-dimensional transformation matrix and offset, which are saved to the 
corresponding LMM map as one of its Navigator properties.

Below is an example of such an entry from the .nav file:

.. code-block:: python
   :caption: GridMapXform entry for LMM map

   ...
   GridMapXform = 0.997785 0.0665214 -0.0665214 0.997785 -11.1591 -15.8475
   ...

The program determines this transformation by identifying and comparing five of the best-matching locations from the 
LMM map before and after reloading. Using these points, it calculates the optimal transform and applies it to all 
Navigator items previously defined in the LMM map.

Although the LMM map overview image remains visually unchanged, all coordinate values are updated accordingly. This 
means the previously defined points will still work without needing to acquire a new LMM map. This process ensures 
continuity and accuracy in navigation despite grid reloads.

This procedure is known as **Realign to Reloaded Grid**, and it is triggered automatically after a reload during a Multigrid 
operation. If needed, you can also invoke it manually using the ``Realign to Map`` button found in the Multiple Grid 
Operations dialog window.

Each time the grid is reloaded, a new GridMapXform matrix is computed and updated in the nav file. All coordinate values in the Navigator 
file evolve with each transformation, adapting to the current physical position of the grid on the stage—even though the 
LMM map image itself appears unchanged.

As this transformation is foundational to the Multigrid workflow, it relies on the presence of an LMM map. Skipping the 
LMM step and jumping directly to MMM is not possible.

.. _Multishot_in_multigrid:

Multishot in Multigrid 
----------------------

One of the key features of the multishot procedure in SerialEM is its ability to derive final Image Shift vectors using 
a combination of hole vectors and an adjustment transform. This significantly simplifies alignment during automated data 
acquisition.

As discussed in other SerialEM notes, when the hole finder routine is executed on a View (V) image, a set of hole vectors 
becomes available. By pressing the Use ``Last Hole Vectors`` button, SerialEM can generate a set of *rough* Image Shift vectors 
based on the transformation of these hole vectors. 

If the hole finding routine is instead performed on a MMM map overview, the process works similarly: the resulting *rough* 
Image Shift vectors are computed and stored. Importantly, this information is saved as part of the Navigator item properties 
for the MMM map.

Below is an example of such an entry in the .nav file:

.. code-block:: python
   :caption: rough IS vectors stored with MMM map

   HoleISXspacing = -1.40096 2.16152 0
   HoleISYspacing = -2.17058 -1.41177 0

Thus, every MMM map can include this hole vector-derived Image Shift information, which is stored in the .nav file.

When a user performs the “StepTo & Adjust” operation, SerialEM not only determines the final, accurate Image Shift vectors 
for high-magnification data acquisition, but also calculates an adjustment transform. This transform is saved in the user’s 
settings file, typically in a format like the following:

.. code-block:: python
   :caption: Adjustment Transform for final IS vectors

   HoleAdjustXform -37 0 0 18 35 0.918684 0.015073 0.000718 0.926858

This transform describes the relationship between the View and Record beams and is generally stable—it does not vary between grids.

During Multigrid operation, SerialEM retrieves the “rough” Image Shift vectors that are stored with each MMM map and dynamically 
combines them with the HoleAdjustXform. This results in the final Image Shift vectors used for precise and automated data acquisition. 
The process happens seamlessly during multigrid operation, ensuring accurate targeting without manual intervention.

.. _hole_vectors_transform:

Hole Vectors Are Also Transformed Upon Reloading
------------------------------------------------

When hole finding is performed on all MMM maps, we obtain both the positions of "good" holes and a set of hole vectors. These 
vectors define the relative layout of the holes and are critical for generating accurate multishot Image Shift patterns.

However, during final data acquisition, the grid is reloaded again. While we know that the hole positions on MMM map remain valid due to 
the applied **GridMapXform**, the natural question is: Are the hole vectors also still valid after reloading?

The answer is yes.

SerialEM automatically updates the hole vector information to reflect the new grid orientation. Specifically, the following lines 
in the .nav file are updated:

.. code-block:: python
   :caption: IS vectors for MMM map

   HoleISXspacing = ...
   HoleISYspacing = ...

You can observe the effect of this by displaying the multishot pattern that was initially generated from the MMM map before 
reloading. On the old MMM map image, the pattern will now appear misaligned. However, if you take a fresh LD_View image and 
display the current multishot pattern on that, it aligns correctly—indicating that the hole vectors were properly transformed 
to match the new grid positioning. Even you delete these two lines and regenerate them by running hole finding on "old" MMM
maps, the new lines will be reflecting current new grid positioning. The new **GridMapXform** is in control!

SerialEM includes several mechanisms to ensure this information is accurately tracked and maintained across reloads. These 
include special .nav entries such as "OrigReg" and "Regis", which help maintain the integrity of coordinate systems and 
transformations at each stage.
