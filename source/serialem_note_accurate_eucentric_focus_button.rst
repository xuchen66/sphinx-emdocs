.. _refine_eucentric_focus_button:

SerialEM Note: Accurate values for "Eucentric Focus" button 
===========================================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: July 30, 2026
:Last_Updated: Aug. 3, 2026

.. glossary::

   Abstract
      It is quite common for the "Eucentric Focus" button on the right pad of 
      the TFS microscope to set the focus inaccurately, especially at lower 
      magnifications. One possible reason is that it is difficult to determine 
      the true focus of a low-magnification image during the microscope alignment 
      procedure.

      In this note, I describe how SerialEM's Autofocus function can be used to 
      refine the value associated with the "Eucentric Focus" button, allowing it 
      to set the focus more accurately.

.. _background:

Background Information
----------------------

Pressing the **"Eucentric Focus"** button sets the microscope focus 
to a predefined value for a specific combination of magnification, 
probe mode, and TEM mode. For example, the stored value may correspond 
to **910× magnification**, **µP probe mode**, and **EFTEM mode**. These 
values are established during the microscope alignment procedure.

During the alignment, you are typically asked to use the multi-function 
keys to bring an image feature or the crossed beam to the center of the 
screen and then carefully focus the image. At this point, the 
**Eucentric Focus** value for that particular magnification and mode 
combination is stored.

In normal operation, you do not need to press the **"Eucentric Focus"** 
button for these values to be used. Whenever you switch to a magnification, 
the microscope automatically recalls the stored focus value for that 
magnification and mode combination and applies it to the objective lens. 
This is why the **Obj%** value usually changes when you change magnification.

At high magnifications, it is relatively easy to achieve accurate focus 
during the alignment procedure because image features are clearly visible. 
At low magnifications, however, image features are often difficult to see 
well enough to judge the exact focus. You can use a direct camera to assist 
with focusing, and continuous imaging with Thon ring information can also help. 
Even so, it is still possible to store a focus value that is off by 
**50–100 µm**, because it is difficult to determine the true zero-defocus 
point from Thon rings at low magnifications.

Although this level of error is usually not critical for routine operation, 
it can be inconvenient. Each time you switch to a low magnification, you may 
find that the focus is approximately **100 µm** away from the desired value 
and need to correct it manually. It would therefore be beneficial to refine 
the stored **Eucentric Focus** values so that they are more accurate.


.. _help_from_G:

SerialEM Autofocus Comes to Help
--------------------------------

You can use **SerialEM** to refine these values.

Assuming that **Image Shift** has been calibrated for 
all magnifications and **Autofocus** has been calibrated 
for at least one magnification in the SA range, you can run the 
following SerialEM script to determine the "true" focus value 
for each magnification, especially in the low-magnification ranges 
(such as **M** and several **SA** magnifications). The script performs 
autofocus at zero target defocus and prints the corresponding objective 
lens value to the log.

.. code-block:: ruby
        
  SuppressReports
  SetTargetDefocus 0
  Autofocus
  ReportObjectiveStrength OBJ
  obj = round $OBJ * 100 4
  Echo Obj = $obj%

  SuppressReports 0
  ReportMag

  Echo

After recording the objective values for each magnification, repeat 
the corresponding microscope alignment procedure. Instead of relying 
solely on visual focusing, manually adjust the focus knob until the 
objective lens value matches the value determined by the SerialEM script. 
Then complete the alignment procedure so that the **Eucentric Focus** 
value for that magnification is stored.

By repeating this process for the magnifications of interest, you can 
significantly improve the accuracy of the stored **Eucentric Focus** values. 
As a result, switching to those magnifications will place the microscope much 
closer to the correct focus, reducing or eliminating the need for manual focus 
adjustments during routine operation.

.. _note:

  After this note is written, I learnt this from Wim Hagan that if one uses 
  DarkField Wobbler on the right handpanel during the alignment the focus could
  be assigned fairly accurate. I decide not to retract this note, as the idea in 
  this note is still somewhat valid. 
