
.. _SerialEM_note_more_about_Z_height:

SerialEM Note: More About Z Height
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-12-09 Last Updated

.. glossary::

   Abstract
      This is the first of a series of SerialEM notes I have wanted to write for a while. An application of using SerialEM, 
      even a simple one, could be very useful and "handy" for a SerialEM user. I try to give more explanantion for what 
      I did, rather than to just present plain lines of codes (yes, SerialEM scripting code) so that it can be helpful for a 
      SerialEM user, especially a new comer to understand better how SerialEM works. 
      
      Quickly and accurately moving specimen to eucentric height is a frequently needed task. Everything is going to be easier 
      if speciment is at eucentric height and objective lens at eucentric focus. I wrote a litte document before how to use tilted-beam
      method to do this using SerialEM `"SerialEM HowTo: Positioning Z" <http://www.bio.brandeis.edu/KeckWeb/emdoc/en_US.ISO8859-1/articles/SerialEM-howto:positioningZ/>`_. In this note, I give you an improved version and hopefully it is easier to use and 
      more robust too. 
      
.. _background_info:

Background Information 
----------------------

SerialEM has built-in task function to do eucentricity using stage-tilt method. It is robust, but slower than beam-tilt method. Beam-tilt method is opposite to autofoccus funtion:

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
   Z = ROUND $Z 2
   echo Z has moved --> $Z micron 
   EndLoop

   #=========================================
   # restore the defocus set in V originally
   # ========================================
   RestoreFocus
   EndFunction

The real difference between this and previous version *Z_byV* is an additional line inserted after SetEucentricFocus:

.. code-block:: ruby

   ChangeFocus $offset
   
This is to use large defocus offset for good contrast. This function should be called in script like this way:

.. code-block:: ruby

   CallFunction Z_byV2 -288.32
   
Or if it is in a script "MyFuncs":

.. code-block:: ruby

   CallFunction MyFuncs::Z_byV2 -288.32

Obviously, the -288.32 is to pass to variable $offset in the function. 

Now question is how to determine this offset value for accurate Z height for and under current scope condition. 

.. _find_offset:

Find the Offset Value using Script FindOffset
---------------------------------------------

If we found the good "offset" value, it will be good for some time, at least this session. So this like a short term calibration. Here is how to find it:

- Adjust specimen to Eucentriciy, using FEI interface tool or SerialEM task function
- run script as below:

.. code-block:: ruby

   ScriptName FindOffset
   # script to find proper offset value to run Z_byV2
   # assume speciment is ON the eucentricity 

   ## Eucentric Z
   #Eucentricity 3
   ReportStageXYZ 
   Z0 = $repVal3

   ## now find the offset
   # for initial offset, get a close value from current setting
   ReportUserSetting LowDoseViewDefocus
   offset = $repVal1 - ( $repVal1 / 10 )
   # 
   Loop 10
   CallFunction MyFuncs::Z_byV2 $offset
   ReportStageXYZ 
   Z = $repVal3
   diffZ = $Z - $Z0
   echo $diffZ
      If ABS $diffZ < 0.5 
         offset = ROUND $offset 2 
         echo >>> Found "offset" is $offset
         echo >>> run script with line "CallFunction Z_byZ2 $offset" 
         exit
      Else 
         offset = $offset + $diffZ
      Endif 
   EndLoop 

It uses function Z_byV2 to see which offset value to recover the Z height determined early by other method. If this script runs and gives offset value as -290.5, then you should use the function with this value:

.. note::

   This offset value changes when V beam size changes. Therefore, it makes sense to do this "calibration" of finding 
   offset value after all the Low Dose area conditions are set and fixed. With the "good" offset value that gives good results,
   the program works very reliably, if the V beam doesn't change. For example, on our Krios, the V beam (called Low Dose area V)
   illumination area stays the same, the script works very well. 

.. code-block:: ruby

   CallFunction MyFuncs::Z_byV2 -290.5
   
It will move stage position to Eucentric Z height, almost magically! 

