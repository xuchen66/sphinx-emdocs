.. _cm120_instruction:

CM120 Operation Instruction
===========================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2016-3-17

.. glossary::

   Abstract
      This document tries to list steps and procedures for a typical daily operation on CM120. 
      You can use it as guidebook to help you when you are sitting with CM120, especially if 
      you are a new user. I here assume you are already familiar with scope interface, what 
      the knobs and buttons do etc.. You might use the section titles as quick bullet p ints, 
      but the explanation inside each sections are supposed to be useful and informative. 
      
      If you have suggestion how to improve this document to make it more useful, please 
      feel free to let me know. Thank you!

.. note::
      all the turning knobs on the panel and push buttons are marked like ``button``.

.. _check-logbook:

Check log book to see if there have been any problems
-----------------------------------------------------

It is always a good idea to check the LOG book. You can find useful information listed there, such as:

- If there have been any recent problems with the scope.
- The conditions that were used last, especially the filament saturation.
- Any special note that last user wants you to know.

 .. _check-vacuum-status:

Check vacuum status
-------------------

The status must be Ready before you can operate the scope. If not, you should ask for help and 
report it to the manager. Usually, if the vacuum is not ready, it is due to one of the following reasons:

- The scope is malfunctioning.
- The air pressure is not within a good range(the building compressed air may be down?).
- The cooling water is off.

Vacuum being Ready means:

- Top line of vacuum page shows **read**.
- P3 < 50, IGP < 26 (normally, they are shown 0, 5).
- LEDs for UVAC and HiVAC are lit.

.. _cool-down-scope:

Add Liquid Nitrogen to the BIG anti-contaminator dewar
------------------------------------------------------

LN2 in the cold trap dewar is necessary for fast vacuum recovery. It is very useful, 
especially when you need to change grids and/or do a cryo session. However, if you are 
working with a negative stain or plastic section - a dry grid, the scope can still run 
without LN2 cold trap. Unless you are running automatic, long overnight session, you 
should always use it.

.. Tip::

   Generally, when you first put LN2 dewar to its stand, you want to be SLOW, or the 
   strong evaporation will make LN2 spilling.

.. _apply-HT:

Apply High Tension
------------------

Turn High Tension (H.T.) on if it is off by pressing the ``H.T.`` button on the panel. 
From ``Parameter`` page, set it to 120kV or the voltage you want.

.. _turn-on-filament:

Turn filament on
----------------

It is recommended to turn the filament on while on the ``configuration`` page where 
the actual filament current number is shown and the ``limit`` can be checked(highlighted).

.. _gun-tilt-saturatation:

Check saturation and gun tilt, and then saturate the filament
-------------------------------------------------------------

The procedure is as following:

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
#. Rotate stage back and forth by pressing ``CompuStage`` - `` A-wobbler``.
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

.. _low-dose

Typical Low-Dose setup parameters
---------------------------------

- Here are some typical setup for Low-Dose condition.

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

- Align an identified area under ``Exposure`` and ``Search``

This step is to insure that what you see under low mag. (Search mode) will be the same area you get under imaging mag. Here is how I do it:

#. At Exposure mode, MECHANICALLY drive an identified spot to the centber of the screen.
#. At Search mode(and usually in Diffraction mode also), using the ``Multi-function knobs`` to backtrack the identified spot at the center of the screen (electronically). This uses Image Shift or Diffaction Shift (when Search mode is set up in Diff mode) to "shift" image without actually moving the stage position.

.. Note::

   You can use a corner of a mesh as the identified spot for a negative stain specimet or to use an ice burn mark in the cryo case.

.. _finish

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


