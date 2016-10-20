
.. _singleparticle_serialem:

Single Particle Data Collection Using SerialEM
==============================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2016-10-18

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

- Desaturate the filament about 2-3 clicks.
- Press ``Align`` button, highlight ``Gun Tilt``.
- Adjust the ``multi-function X,Y`` to get the best shape for the tip and maximum intensity as well.
- Bring the filament current to saturation.

.. _c2-aperture-stigma:

Check C2 aperture mechanical position and C2 stigmatism
-------------------------------------------------------

Steps to adjust the C2 aperture mechanical position are:

- Make sure the C2 aperture is in.
- At around 5kX, cross the beam first and center beam using track ball on the left panel, then spread the beam until its diameter is close to 5cm ring on the screen. Adjust mechanica ``screws X,Y`` on C2 aperture to center the beam.
- Repeat this process 2-3 times.
- At high mag ~30kX or higher, change the ``Intensity`` of beam through the crossover and adjust the ``screws X,Y`` to make the beam symmetric when spreading out.

There are two ways to check and adjust the C2 stigmatism:

- When the filament is desaturated, press ``Stig`` button and select ``Cond``, then adjust ``Multi-function X,Y`` so that the details in the filament shadowing image can be clearest and sharpest.
- When changing the beam intensity, use the ``Multi-function X,Y`` to adjust the beam into symmetric and *round* shape, i.e. not elliptical.

.. Note::

   For this purpose, don't pay attention to the beam shape when beam is exactly at cross point, as that more 
   reflects the shape of the crystal tip, rather than stigmatism. You want to make beam *round* when spreat out.

.. _specimen-rod:

Check specimen holder & load grid
---------------------------------

.. Important::

   This is important. If you see any problem with any of the holders, report it to the manager immediately. 
   Otherwise, you could be the one held accountable for the damage. 
   
Several details about the holder must be checked carefully before use:

#. Overall shape is good, and there is not obvious damage.
#. Make sure there is no crack or any other damage on the O-ring. If you do see a damage, like a cut etc., 
   ask manager to replace it for you. Check if there any dirt or fibril on the O-ring. You might want to 
   clean it gently with alcohol and slightly re-grease it. Do not over-grease. The main function of the 
   grease is to lubricate.
#. Gently secure the specimen grid on its position. Use the tool pin to open and close the clamping device.

.. _insert-specimen-rod:

Insert specimen holder into column
----------------------------------

.. Warning::

   Be careful! Only at this stage, you might damage the scope or specimen holder mechanically. Be sure that 
   you understand what you are doing. Should you feel any confusion about this procedure, please stop and ask 
   for help.

.. Note::

   For the sake of filament crystal, it is REQUIRED to turn down the filament to 10 before inserting the specimen rod. 
   That way, in case IGP shoots high, there will be no subtancial damage to the LaB6 tip crystal. In general, filament 
   should be kept at 10 or completely off until IGP recovers to below 26.

The procedure to insert a room temperature specimen rod is below:

#. define airlock pumping time as 60 seconds, from Vacuum - Cryo page.
#. Reset stage tilt angle to 0 if it is not.
#. Insert rod in, with the Pin at 3'oclock position.
#. As soon as it reach the end, rotate rod CLOCKWISE with some pushing force so that the pin slides into the locking 
   groove at 5'oclock position. You should feel the rod goes "in" about 8mm.
#. Wait until the red LED on the stage disappears. Dismiss the "non-standard" flushing message on the screen by pressing
   ``Reset`` button at lower left corner of the screen display.
#. Turn rod Count-Clockwise until pin is at 12'oclock position, while watching IGP reading. You should adjust your 
   rotating speed to keep IGP < 40.

.. _eucentricity

Adjust the specimen height to the eucentric height
--------------------------------------------------

Eucentricity is a fixed reference point in a scope. It is the intercross point of stage axis and column axis. We want to observe our specimen grid at this height level so that the actual magnification doesn't differ much from day to day. And scope is designed to perform better when specimen is at such height. It is good to have the habbit to always adjust specimen to eucentric height after rod insertion. On CM120, the procedure is as following:

#. Have beam seen on large screen, at ~3000X, and find a feature on the grid.
#. Rotate stage back and forth by pressing ``CompuStage`` - ``A-wobbler``.
#. Adjust joystick Z to minimize the shifting of the feature.

.. _beam-titl-pp:

Check Beam Tilt Pivot Point X, Y
--------------------------------

.. Note::

   The prerequisites for Pivot-Point is specimen being at eucentric height and objective being preceisly at focus level. **The order is important here**.

#. Make sure the specimen is at eucentric height.
#. Take out Obj. aperture.
#. Press button ``Align`` - ``Direct Alignment`` - ``Beam Tilt Pivot X``.
#. Merge image feature by adjusting ``Focus`` knob. This is to precisely focus the image.
#. Merge beam using ``Multi-fcuntion knobs``.
#. Repeat the last two steps for Beam Tilt Pivot Y.
#. Press ``Align`` button again to exit.

.. _HT-Rot_center:

Check voltage and current rotation centers
------------------------------------------

This step is to align the beam to make it parallel to the axis of the column. The purpose of this step is to make beam to hit specimen perpendicularly. A coma is not a good thing, as it generates some phase error to the data.

The procedure is below.

#. Press ``Align`` button and select ``Rotation Center``.
#. Select ``Voltage`` or ``Current`` from the same page.
#. Adjust the ``Multi-function Knobs`` to let the wobbling be symmetrical around the center of the beam. The feature at very center of the large screen has minimum shift.

If you perform this with Obj aperture in, then re-check the certering of objective aperture.

.. Note::

The step size button on ``Focus`` is used to control the amplitude of the beam wobbling.

The steps used here only give "roughly" parallel beam to the axis. If you need very acurate "0" tilt beam, a different alignment procedure - Coma-Free is needed.

.. _obj-aperture:

Put in objective aperture and center it
---------------------------------------

It is important to know what size of the obj. aperture you are using. You don't want to use too small size to actual cut off useful high resolution signal. Meantime, you don't want to use the aperture size too large, as the non-usable high resoltion beam becomes noise to your image. This reduces signal to noise ration unneccesarily.

The position of the aperture could affect the obj. lens stigmatism. Therefore, you want to do this step before you finally check Obj lens stigmatism.

Here are the steps to insert and center Objective lens aperture:

#. Make sure the large screen is down, to prevent CCD from damage.
#. Switch to diffraction mode by pressing the diffraction ``D`` button.
#. Adjust camera length to ~1m using magnification knob.
#. Adjust the ``Intensity`` and ``Defocus`` knobs to see the shape edge of the obj. aperture.
#. Adjust the related mechanical screws on aperture holder to choose the proper size of the aperture and center it to the central beam on diffraction pattern.
#. Switch back to image mode by pressing ``D`` button again.

.. _obj-stigma:

Check Objective Lens stigmatism
-------------------------------

The obj. stigmatism should be corrected as much as possible, and it should be checked for every netative stain low-dose image that you are taking, as staining material might change field in local area. This is a bit hard by hand. Even with lastest version of SerialEW, this can be done by software, it is still not easy and time efficient. However, slight stigmated image is not critically bad, as it can be corrected as part of CTF correction computationally.

Here are steps to correct Obj lens stigmatism, manually:

#. Go to a relatively high mag., such as 100,000X, and focus the image.
#. If possible, acquire continous CCD image with live FFT so Thon rings can be seen.
#. Press the ``Stig`` button, highlight ``Obj``, and select proper stepsize.
#. Adjust the stigmatism using ``Multi-function X,Y`` until it becomes minimum at all defocus levels. (It shows up more at close to focus.)

.. _low-dose:

Low-Dose Setup
--------------

Magnification setup for three modes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Here are some typical magnificaton setup for Low-Dose condition.

+--------+----------+
| Mode   | Mag. (X) |
+========+==========+
| Search |   3000   |
+--------+----------+
| Focus  |  60,000  |
+--------+----------+
| Record |  60,000  |
+--------+----------+

One might use diffraction mode to ``Search``, which uasally gives better contrast but the "image" might be distorted. The final magnification depends on target pixelsize on image. If possible, use the same magnificagion for both ``Focus`` and ``Record`` to eliminate the dofocus offset between the two magnifications. The off-axis distanse is usually about 1.5 - 2.0 microns. 

Align an identified area under ``Exposure`` and ``Search``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This step is to insure that what you see under low mag. (Search mode) will be the same area you get under imaging mag. Here is how I do it:

#. At Exposure mode, MECHANICALLY drive an identified spot to the centber of the screen.
#. At Search mode(and usually in Diffraction mode also), using the ``Multi-function knobs`` to backtrack the identified spot at the center of the screen (electronically). This uses Image Shift or Diffaction Shift (when Search mode is set up in Diff mode) to "shift" image without actually moving the stage position.

.. Tip::

   You can use a corner of a mesh as the identified spot for a negative stain specimet or to use an ice burn mark in the cryo case.

.. _finish:

Finishing Up
------------

When you are done with your session, perform finishing up procedure.

- Specimen rod out.
- Reset Stage Position, X, Y, Z and A.
- Filament 0.
- H.T. OFF.
- Cryo-cycle, normally for 2-3 hours.
- Data display OFF.
- Display OFF.
- Log your session on logbook.

