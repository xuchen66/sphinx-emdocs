.. _LD_offsets_touchup:

SerialEM Note: Touch Up Low-Dose Offsets for IS and Defocus 
===========================================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: May 19, 2026
:Last_Updated: May 19, 2026

.. glossary::

   Abstract
      There are still a couple of issues in the Low Dose setup that
      occasionally require attention or correction. For example, in our
      experience, after running final data collection for some time (e.g.,
      around 20 minutes), the Image Shift offset for View area can drift
      slightly. As a result, the target position may become off by about 100
      nm or so. This is not a major issue, but it can be annoying when very
      accurate targeting is required. We have observed this behavior not
      only on Talos and Glacios, but also on Krios. I suspect this is
      related to a hysteresis effect. It would be useful to have a way to
      correct this quickly, or even automatically, during data collection.

      Another issue can occur after an unexpected exit, such as a program
      crash.  After restarting the SerialEM program and entering Low Dose
      mode again, there may be a so-called “defocus doubling” problem in
      View or Search mode.  For example, if View mode was originally set
      with a -200 µm defocus offset, it may become -400 µm after restarting.
      It would be helpful to have a simple way to fix this issue as well.

      In this note, I will share how I handle these problems.


.. _fix_IS_offset:

To Fix IS Offset
----------------

There is a script command available to obtain a new, hopefully good Image
Shift offset for View and Search. Here we are focused on View. 


.. code-block:: ruby

  FindLowDoseShiftOffset #S [#M] [#S] [#A]

This works equivalently as [Auto] button in Low Dose Control panel. 
It is important to make sure there is a strong feature in both Record and
View mags so the above command can work reliably. Lets say there is a black
ice chunk somewhere we can use. Here are the steps to prepare:

1. Take a Preview image contain this black chunk or edge of the chunk. Make
it into a map. Assume it has index # 3. 

2. Take an anchor map at View mag. This is to help to realign accurately to the
Preview map, specially when Preview mag is high. 

And I can create a little script as below"

.. code-block:: ruby

  ScriptName Fix-IS-Offset

  RealignToOtherItem 3 0           # index = 3
  FindLowDoseShiftOffset 0         # 0 is for View Offset

You can run this little script whenever you like to. And you can also run
this periodically in your collection workflow pipeline. 

.. _fix_defocus_doubling:

To Fix Defocus Doubling Issue
-----------------------------

When using the latest Testing executable, we sometimes encounter a “defocus doubling” issue after a crash. Specifically, after restarting the SerialEM program and turning on Low Dose mode, the microscope defocus may be at -400 µm, even though the Low Dose defocus offset for View mode is set to -200 µm.

The root cause of this problem is that View mode uses the uP probe mode, while F, T, and R typically use the nP probe mode. If SerialEM crashes while in View mode, the microscope may be left in uP mode with a defocus of -200 µm, while the nP mode defocus remains at 0. One simple way to fix this is to press the Eucentric Focus button on the right-hand panel before restarting SerialEM.

For a normal-exit crash, the latest Testing version of SerialEM can detect the crash upon restarting and prompt you with an option to fix the issue automatically.

However, if you do not notice that the microscope is already at -200 µm defocus and you start SerialEM and enter Low Dose mode without correcting it first, then View mode will inherit the existing -200 µm defocus in uP mode and apply an additional -200 µm offset from the Low Dose settings. As a result, the View defocus becomes -400 µm.

This can be easily fixed by a fairly simple script as below:

.. code-block:: ruby

  ScriptName Fix-Defocus-Doubling
  ToolbarFillColor gold
  
  # fix double defocus offset problem due to crash
  
  # turn option off
  ReportUserSetting AdjustFocusForProbe
  If $RepVal1 == 1
     SetUserSetting AdjustFocusForProbe 0 1
  Endif
  
  # LD on
  ReportLowDose
  If $repVal1 == 1
     Echo In LD!
  Else
     Echo Now turn it on!
     SetLowDoseMode 1
  EndIf
  
  # fix it
  GoToLowDoseArea R
  SetStandardFocus 0
  GoToLowDoseArea V 
  SetLDDefocusOffset V 0
  SetStandardFocus 0
  SetLDDefocusOffset V -200
  SetUserSetting AdjustFocusForProbe 1 1
  
  # ReportLDDefocus
  ReportLDDefocusOffset V
