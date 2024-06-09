.. _SerialEM_two-scripts-for-highfocus-cals:

SerialEM Note: Two Scripts for High-Focus Calibrations
======================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2024-06-09
:Last_Updated: 2024-06-09

.. glossary::

   Abstract
      It is straightforward to do high-focus calibrations for both
      Mag and Image Shift. However, it becomes annoying when you have to 
      do this for multiple intensity values. Luckily, there are also two
      scripting commands to perform these two calibrations. With simple scripting,
      it makes the calibrating a lot easier and more automated. 

      In this note, I show you the two scripts I came up. I found them
      helpful.

.. _highfocus-mag:

For High-Focus Mag 
------------------

For the very first time, you have to draw lines in align buffer and A buffer
for 0 and a few defocuses. After doing it manually for the strongest beam, one can simply
use a scritps to add more intensity values. See below.

.. code-block:: ruby

   ScriptName CalibrateHighFocusMag

    # chen.xu@umassmed.edu 
    # April 28, 2024
    
    # after manually calibrate for one intensity (requires drawing lines) 
    # using this script to continue for all slightly higher intensities
    # more automated way (without needing drawn lines). 
    
    
    AlignBuf = O
    defs = { -50 -100 -150 -175 -200 -225 -250 -275 -300 -325 }
    
    Loop 50
       IncPercentC2 10             # increase 10%
       ReportPercentC2 
       If $repVal1 > 99.0
          Exit Intensity exceeds 99.0, quit.
       Endif 
    
       T
       Copy A $AlignBuf
       Loop $#defs ind
          ChangeFocus $defs[$ind]
          T
          CalibrateHighFocusMag     1   # skip the [YES] confirmation
          ChangeFocus -1 * $defs[$ind]
       EndLoop 
    EndLoop 
   
  
.. _highfocus-mag:

For High-Focus IS
-----------------

Similarly, the script makes calibration for multiple intensity values 
a lot easier.

.. code-block:: ruby

   ScriptName CalibrateHighFocusIS
   
   # chen.xu@umassmed.edu 
   # April 28, 2024
   
   
   AlignBuf = O
   defs = { -50 -100 -150 -175 -200 -225 -250 -275 -300 -325 }
   
   Loop 50
      IncPercentC2 10               # increase 10% 
      ReportPercentC2 
      If $repVal1 > 99.0
         Exit Intensity exceeds 99.0, quit.
      Endif 
   
      Loop $#defs ind
         ChangeFocus $defs[$ind]
         CalibrateHighFocusIS $defs[$ind]
         ChangeFocus -1 * $defs[$ind]
      EndLoop 
   EndLoop 
