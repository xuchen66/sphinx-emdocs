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
2. Take a anchor map for View mag. This is to help we can realign to the
Preview map, specially when Preview mag is high. 

And I can create a little script as below"

.. code-block:: ruby

  ScriptName Fix-IS-Offset

  RealignToOtherItem 3 0
  FindLowDoseShiftOffset 0         # 0 is for View Offset

You can run this little script whenever you like to. And you can also run
this periodically in your collection workflow pipeline. 

.. _fix_defocus_doubling:

To Fix Defocus Doubling Issue
-----------------------------

When we use latest Testing executable, sometimes when after crashes, we get
into this "Defocus doubling" issue. Namely, upon SerialEM program restarting
and turning on Low Dose, the scope defocus is at -400 microns, while LD
defocus offset for View is set at -200. 

The root source of this problem is that View was used with uP probe, while
F, T and R are usually with nP probe mode. When it crashes in View, the
scope is left in uP mode and with -200um defocus, but nP is still at 0. 
One easy way to fix this is to press Eucentric Focus button on right hand 
panel, BEFORE you even restarting SerialEM. 

If this is a normal exit crash, the late Testing version of SerialEM can
detect the crash upon restarting and prompt you option to fix the issue. 

If you fail to notice scope is at -200um, and you start SerialEM and go into
Low Dose without any fix, then View will take whatever you have on scope uP
mode which is -200, and add another -200 (the offset value). So your View
ends up with -400um. 

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
