
.. _singleparticle_serialem:

Single Particle Data Collection Using SerialEM
==============================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-created: 2016-10-18
:Last-updated: 2021-04-05
.. glossary::

   Abstract
      This document is to list step-by-step operations to perform single partile data collection using SerialEM. 
      I often receive requests to provide script/macro for single particle data collection using SerialEM as control program. 
      It is not very easy to explain that script/macro itself is only the small portion of whole operation steps. 
      I realized that a brief but detailed protocol for whole process is perhaps more useful, specially for novice cryoEM users. 
      It should be useful for more experience users as well as a quick checklist in case some step is forgotten. 
      I wrote something similiar at Brandeis EM webpage, but here I rewrite this to reflect newer hardware of microscope and camera, 
      and with updated SerialEM scripts/macros.
      
      A Krios with K2 Summit camera and FEI Ceta camera is the base hardware setup for this protocol. 

.. note::
      This doc is a working progress. If you have comment and suggestion, please let me know. Thank you!

.. _scope_tuning:

Check Scope Condition and Perform Tuning 
----------------------------------------

Before you commit large dataset time, it is always a good idea to check scope condition to make sure everything is good. Calm down and be patient! Here are a few things I usually check. 

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

   ## when to perform CenterBeam and AutoFocus, and defocus range
   groupOption = 1                 # 1 = at group head, 0 = at every item 
   defLow = -1.0 
   defHigh = -2.5
   step = 0.1  
   
   ## Drift control
   driftControl = 1                # 1 = yes, 0 = no
   limit = 3.0                     # Angstroms
   
   ## X,Y positioning
   templateOption = 1              # 1 = to use a fixed ref, 0 = use dynamic one
   refBuffer = P                   # reference buffer for template image
   
   ########## no edit below ##########
   RealignToNavItem 0
   ResetImageShift 2
   If $templateOption == 1
       Echo  --- assuming you have a template image in buffer $refBuffer ---
   Else
       Copy A $refBuffer           # use dynamic ref (whole image itself)
   Endif 
   AcquireToMatchBuffer $refBuffer
   AlignTo $refBuffer 0 1

   ## turn ON drift protection if it's off so Autofocus can report drift
   ReportUserSetting DriftProtection DP 
   If $DP == 0
       SetUserSetting DriftProtection 1
   Endif     

   ## center beam & focus
   ReportGroupStatus gs            # 1 = group head, 0 = inividual, 2 = group member
   If $groupOption == 0
       #AutoCenterBeam             
       CallFunction CycleTargetDefocus $defLow $defHigh $step
       AutoFocus
   Else
       If $gs == 1 OR $gs == 0     
           #AutoCenterBeam         
           CallFunction CycleTargetDefocus $defLow $defHigh $step
           AutoFocus
       Else
           Echo    group member, skip focusing...
       Endif 
   Endif

   ## drift                        # if reported drift is high, call drift control
   If $driftControl == 1
      ReportFocusDrift FD 
      If $FD > 0.09                # 0.09 reported here is close to real 2.0A/s.   
        CallFunction Drift $limit
      Endif 
   Endif

   ## shot
   AdjustBeamTiltforIS             # needed for single shot, so leave it here regardless
   MultipleRecords
   # Record

   ## post-exposure
   RefineZLP 30                    # refine ZLP every 30 minutes

   ####### Functions
   Function CycleTargetDefocus 3 0 defLow defHigh step
   Echo ===> Running CycleTargetDefocus ...
   Echo   --- Range and Step (um)  => [ $defLow, $defHigh ], [ $step ] ---

   delta = -1 * $step
   SuppressReports
   ReportTargetDefocus tarFocus
   If $tarFocus > $defLow OR $tarFocus < $defHigh
      SetTargetDefocus $defLow
   Else 
      IncTargetDefocus $delta
      ChangeFocus $delta
   Endif

   ReportTargetDefocus 
   EndFunction

   ######
   Function Drift 1 0 crit 
   # A function to measure drift rate, if good, skip to the end of loop. 
   # Otherwise, exit execution -- i.e. skip the point. 
   Echo ===> Running Drift $crit (A)...

   shot = F
   interval = 4
   times = 10

   period = $interval + 1
   $shot
   Delay $interval
   Loop $times index
      $shot
      AlignTo B
      ReportAlignShift
      ClearAlignment
      dx = $repVal3
      dy = $repVal4
      dist = sqrt $dx * $dx + $dy * $dy
      rate = $dist / $period * 10	
      echo Rate = $rate A/sec
      echo ----------------

      If $rate < $crit
         echo Drift is low enough after shot $index      
         break
      Elseif  $index < $times
         Delay $interval
      Else
        echo Drift never got below $crit: Skipping ...
        exit   
      Endif
   EndLoop
   EndFunction


This script calls two functions - ``CycleTargetDefocus`` and ``Drift``. This is a standalone script. Some other functions can found `here on github.com <https://github.com/xuchen66/SerialEM-scripts/blob/new-features/MyFuncs.txt/>`_.

If running with python support, the code looks something like this:

.. code-block:: python

   #!Python
   #ScriptName LD-Group-Python
   import serialem
   from math import sqrt

   ### Functions
   def CycleTargetDefocus(defLow, defHigh, step):
       print(' ---> running CycleTargetDefocus ...')
       print(' --- defLow, defHigh, step = ', defLow, defHigh, step, '---')
       serialem.SuppressReports()
       tarFocus = serialem.ReportTargetDefocus()   # float
       if tarFocus > defLow or tarFocus < defHigh:
           serialem.SetTargetDefocus()
       else:
           serialem.IncTargetDefocus(-step)
           serialem.ChangeFocus(-step)
       serialem.ReportTargetDefocus()

   def Drift(crit):
       print(' ---> Running Drift ', crit, 'A ...')
       interval = 4
       times = 10
       period = interval + 1
       #
       serialem.Focus()
       serialem.Delay(interval)
       for index in range(1, times+1):
           serialem.Focus()
           serialem.AlignTo('B', 0, 1)
           aliShift = serialem.ReportAlignShift()
           dx, dy = aliShift[2], aliShift[3]
           rate = sqrt(dx*dx + dy*dy)/period*10
           print(' Rate =', rate, 'A/sec')
           if rate < crit:
               print('Drift is low enough after shot ', index)
               break
           elif index < times:
               serialem.Delay(interval)
           else:
               print('Drift never got below ', crit, 'skipping...')
               serialem.Exit()

   ### main
   # when to do focus and center beam, set range
   groupOption = 1         # 1 = only group head, 0 = every item
   defLow = -1.0
   defHigh = -2.5
   step = 1.0

   # drift control, limit 
   driftControl = 1        # 1 = yes, 0 = no 
   limit = 3.0

   # for X,Y position
   templateOption = 1      # 1 = use fixed ref, 0 = dynamic one (whole image)
   refBuffer = 'P'

   #### no editing below ####
   serialem.RealignToNavItem(0)
   serialem.ResetImageShift(2)
   if templateOption == 1:
       print(' --- assuming a template image in buffer', refBuffer, '---')
   elif templateOption == 0:
       serialem.Copy('A', refBuffer)
   else:
       print('templateOption needs to be 0 or 1, please fix it and continue')
       serialem.exit()
   serialem.AcquireToMatchBuffer(refBuffer)
   serialem.AlignTo(refBuffer, 0, 1)

   # turn on Autofocus drift protection so it reports drift rate
   DP = serialem.ReportUserSetting('DriftProtection')    # float
   if DP == 0.:
       serialem.SetUserSetting('DriftProtection', 1)

   # center beam and defosuc
   gs = serialem.ReportGroupStatus()      # tuple
   gs = gs[0]                             # now a float
   if groupOption == 0:
       serialem.AutoCenterBeam()
       CycleTargetDefocus(defLow, defHigh, step)
       serialem.Autofocus()
   else:
       if gs == 1. or gs == 0.:
           serialem.AutoCenterBeam()
           CycleTargetDefocus(defLow, defHigh, step)
           serialem.AutoCenterBeam()
       else:
           print('   group member, skip focusing ...')

   # drift 
   if driftControl == 1:
       FD = serialem.ReportFocusDrift()
       if FD > 0.09:
           Drift(limit)

   # shot
   serialem.AdjustBeamTiltforIS()      # keep this line 
   serialem.MultipleRecords()
   #serialem.Record()

   # post-expose
   serialem.RefineZLP(30)              # if GIF exists 

This is a good time to test running this script on one of the point items in navigator window, to make sure it runs fine. 

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

