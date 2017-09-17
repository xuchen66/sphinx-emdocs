
.. _SerialEM_note_more_about_Z_height:

SerialEM Note: More About Z Height
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-09-16

.. glossary::

   Abstract
      This is the first of a series of SerialEM notes I have wanted to write for a while. An application of using SerialEM, 
      even a simple one, could be very useful and "handy" for a SerialEM user. I try to give more explanantion for what 
      I did, rather than to just present plain lines of codes (yes, SerialEM scripting code) so that it can be helpful for a 
      SerialEM user, especially a new comer to understand better how SerialEM works. 
      
      Quickly and accurately moving specimen to eucentric height is a frequently needed task. Everything is going to be easier 
      if speciment is at eucentric hieght and objective lens at eucentric focus. I wrote a litte document before how to use tilted-beam
      method to do this using SerialEM `"SerialEM HowTo: Positioning Z" <http://www.bio.brandeis.edu/KeckWeb/emdoc/en_US.ISO8859-1/articles/SerialEM-howto:positioningZ/>`_. In this note, I give you an improved version and hopefully it is easier to use and 
      more robust too. 
      
.. _background_info:

Background Information 
----------------------

SerialEM has built-in task function to do eucentricity using stage-tilt method. It is rubust, but slower than beam-tilt method. Beam-tilt method is opposite to autofoccus funtion:

- it sets scope objective lens to eucentric focus value 
- and measures the defocus value for current specimen height using tilted-beam image pair,
- it then changes stage position to that reported value but in oppsite direction, 
- and it iterates until the reported defocus value is close enough to zero.  

The beam-tilt method works very nicely most of time and it is pretty quick. However, there are couples of things making it less perfect. First, the signal becomes very weak when stage is already close eucentricity. We all know the contrast is the lowest when focus matches z height. We can use focus offset to increase the contrast, but non-linearty property casues some inaccuracy. The calibrated standard focus value could also change a litte with time and scope condition. All these together makes it less robust. 

When we use SerialEM Low-Dose mode, we often give large focus offset such as -200 microns to View area (I call it View beam) to make the View image good contrast. If we can use this large defocused View beam to obtain tilt-beam pairs for measuring defocus value accurately, that would be ideal. 

.. _Z_byV2_funtion:

Z_byV2 Function
---------------

The function code is below. 

.. code-block:: ruby

   Function Z_byV2 1 0 offset
   Echo ===> Running Z_byV2 ...
   #====================================
   # for defocus offset of V in Low Dose, save it
   # ===================================
   GoToLowDoseArea V
   SaveFocus

   #==================
   # set object lens 
   #==================
   SetEucentricFocus
   ChangeFocus $offset                         # for -300um offset 

   #===========
   # Adjust Z
   #===========
   Loop 2
   Autofocus -1 2
   ReportAutofocus 
   Z = -1 * $reportedValue1
   MoveStage 0 0 $Z
   echo Z has moved --> $Z micron 
   EndLoop

   #=========================================
   # restore the defocus set in V originally
   # ========================================
   RestoreFocus
   EndFunction

The real difference between between this and previous version Z_byV is an additional line added after SetEucentricFocus:

.. code-block:: ruby

   ChangeFocus $offset
   
This is to use large defocus offset for good contrast. This function should be called in script like this way:

.. code-block:: ruby

   CallFunction Z_byV2 -288.32

Obviously, the -288.32 is to pass to variable $offset in the function. 

Now question is how to determine this offset value for accurate Z height under current scope condition. 

.. _how_to_find_the_offset:

How to Find the Offset Value
----------------------------

- Check Gun Lens, Extracting Voltage, High Tension are set at correct values.
- Stare for a few seconds at the focused beam at the highest SA mag, to see if the beam has good shape and there is no shaking or jumping.  
- From Direct Alignment, do gun tilt, beam tilt PP, Coma-Free alignment if needed. 
- Check Thon Ring at roughly the same condition (mag, dose) as your image condition. Make sure there is no obvious frequency cutoff, and Thon Ring reaches the resolution as in good condition. 

 .. _prepare_camera:

Prepare Cameras 
---------------

For K2 camera, perform full procedure to prepare backgrounds from DM interface. This include software and hardware backgrounds. The hardware background file is for processor to use, while the software gain reference files sit in K2 computer for final software image correction. I was told the software gain reference was more stable than hardware background, but not sure this is still the case. Any way, just perform the full procedure follwing DM steps. 

- After preparing camera, take a single shot with proper dose rate (~5-10 e/pix/s) for 1 second with no specimen and do an FFT. The FFT should show clean background without strong center cross or lines. 
- For Ceta camera, do the same from FEI user interface. 

.. _LMM:

Make Low Mag Montage (LMM) Map or Grid Atlas
--------------------------------------------

It saves time with large area detector. Therefore, Ceta camera is probably better for this step. 

- Select Ceta from Camera Control Setup
- Insert/load your cryo grids
- Set mag at ~87X, retract Obj Aperture
- Spread beam to cover whole Ceta camera area
- Start SerialEM if not yet
- Select Ceta from SerialEM camera control setup *and* FEI Camera ocx. 
- Setup camera condition from SerialEM: Record (e.g. bin=4, exposure=0.4); a Record image gives proper counts (~2000)
- Navigator menu -> Open
- Navigator menu -> Montaging & Grids -> Setup Full Montage; define montage file to open
- Montage Control Panel -> Start
- Click "Yes" to make final overview of montage into a map
- Close the montage file

.. Tip::

   If you are not happy with the aligning of the pieces, you may check and uncheck boxes like
   "Treat sloopy..." and reload the map.

.. _setup_LD:

Setup Low Dose Condition
------------------------

You should have known how to setup Low Dose condition already. Here are some tips.

- Turn on Low Dose Mode from SerialEM Low Dose control panel
- Setup R beam first so that dose on detector and on specimen are all good.
- Defocus offset 100um for View is usually a good start. 
- Always cycle "area" (low dose mode) in one directional looped fashion, i.e., V-F-T-R-V...
- Using the same spotszise for all the areas (low dose modes) is a good idea. 

.. _MMM:

Make Medium Mag Montage Maps 
----------------------------

- select K2 camera from Camera Control Setup (from now on)
- add a polygon (a mesh) in LMM map
- add points for good meshes at center
- add one landmarker such as a dirt point in LMM map 
- take the landmarker into View image (you may use FlowCam to move that feature into middle first.) 
- while landmarker point being current (highlighted), left click on the landmarker in View image, a green cross will appear
- Navigator menu -> Shift to Marker -> Yes (this will change all the coordinates for all the navigator items)
- highlighting polygon item on navigator window, so it is currently selected 
- Navigator menu -> Montaging & Grids -> Setup Polygon Montage -> Check "using View ..." in the dialog window -> define montage filename. 
- Add flag "A" to all the interested mesh point items
- Navigator menu -> Acquire At Points ... -> Check "Eucentric Rough" in Pre-action and "Acquire Montage Map" in main action
- When finished, the MMM maps should be added to Navigator windows. You perhaps can close the montage file now. 

.. _draw_grid:

Draw Grids Points for Each Mesh
-------------------------------

For each of the MMM map, do the following steps to add group points.

- add a polygon item to exclude bad area
- add 5 point items to define grid geometry 
- make any of the 5 items in the group is currently selected
- Navigator menu -> Montaging & Grids -> Set Group Size (10um is a good start)
- Navigator menu -> Montaging & Grids -> Check "Devide point into Groups"
- Navigator menu -> Montaging & Grids -> Add Grid Points -> give polygon item number -> Flag "A" for all

.. _Script:

Test Main Script to Run
-----------------------

Lets load the script "LD-Group" to script editor and try to run it. 

.. code-block:: ruby

   ScriptName LD-Group
   # macro to skip points except the very first in the group.
   # assume LD is setup.

   # X,Y position 
   RealignToNavItem 1
   Copy A P                            # copy last image from Realign to buffer P
   CallFunction MyFuncs::AlignToBuffer 2 P      # this clears out any ImageShift

   # preparation for first item in group
   ReportGroupStatus 
   If $repVal1 == 1 OR $repVal1 == 0   # 1 for group head and 0 for non-group item
      #Call Z_byV
      #UpdateGroup Z
      AutoCenterBeam                   # autocenter policy must be setup 
      CallFunction MyFuncs::CycleTargetDefocus -1.2 -2.0 0.2
      G
   Else 
      echo Directly shot!
   Endif

   # For K2, uncomment next line
   EarlyReturnNextShot 0               # K2 frame, return to SEM
   R

   echo .


This script calls two functions - ``AlignToBuffer`` and ``CycleTargetDefocus``. The script that contains all the functions "MyFuncs" must be also loaded in one of the script buffers/editors. You can download the latest "MyFuncs.txt" `here on github.com <https://github.com/xuchen66/SerialEM-scripts/blob/master/MyFuncs.txt/>`_.

This is a good time to test run this script on one of the point items in navigator windows, to make sure it runs fine. 

.. _final_check:
   
Final Checking
--------------

Now we should check to make sure all the conditions are good for batch data collections for hours and days. 

- Low Dose beams lined up for all the modes (area is the term SerialEM uses)
- Record beam has proper intensity
- Objective aperture is inserted and centered
- Objective Stigmation is good
- Thon ring with R beam on carbon area shows good scope condition
- Total exposure time, frame time, total frame number, binning, output file options, frame saving folder etc. are all good.

.. _aquire_at_points:

Run it! 
-------

Navigator -> Acquire at Points... -> Run Script "LD-Group" in Main action -> OK.


