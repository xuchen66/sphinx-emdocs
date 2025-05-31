.. _SerialEM_two-scripts-for-highfocus-cals:

SerialEM Note: Two Scripts for High-Focus Calibrations
======================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2024-06-09
:Last_Updated: 2025-05-31

.. glossary::

   Abstract
      It’s straightforward to perform high-focus calibrations for both 
      Magnification and Image Shift. However, it can become tedious when 
      you need to repeat the process for multiple intensity values. Fortunately, 
      there are two scripting commands that automate these calibrations. With 
      simple scripting, the process becomes much easier and more efficient.

      In this note, I’ll share the two scripts I developed, which I’ve found to 
      be quite helpful.

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
   # Note - only need to calibrate this for one brightest spot size for LD.
   
   AlignBuf = O                  # set this accordingly
   defs = { -50 -100 -150 -175 -200 -225 -250 -275 -300 -325 }
   
   # get initial C2%
   ReportPercentC2
   iniC2 = $repVal1
   
   Loop 50
      IncPercentC2 10             # increase 10%
      ReportPercentC2 
      If $repVal1 > 99.0
      SetPercentC2 $iniC2
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
   # one mag, one spotsize is enough (e.g. 1250X, uP spotsize 7)

   AlignBuf = O            # set this accordingly
   defs = { -50 -100 -150 -175 -200 -225 -250 -275 -300 -325 }

   # get initial C2%
   ReportPercentC2
   iniC2 = $repVal1

   Loop 50
      IncPercentC2 10               # increase 10% 
      ReportPercentC2 
      If $repVal1 > 99.0
         SetPercentC2 $iniC2
         Exit Intensity exceeds 99.0, quit.
      Endif 
   
      Loop $#defs ind
         ChangeFocus $defs[$ind]
         CalibrateHighFocusIS $defs[$ind]
         ChangeFocus -1 * $defs[$ind]
      EndLoop 
   EndLoop 
