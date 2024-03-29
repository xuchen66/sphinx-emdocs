
.. _SerialEM_note_more_about_Z_height:

SerialEM Note: More About Z Height
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date Created: Dec 12, 2017
:Last Updated: Apr 08, 2021

.. glossary::

   Abstract
      This is the first of a series of SerialEM notes I have wanted to write
      for a while. An application of using SerialEM, even a simple one,
      could be very useful and "handy" for a SerialEM user. I try to give
      more explanation for what I did, rather than to just present plain
      lines of codes (yes, SerialEM scripting code) so that it can be
      helpful for a SerialEM user, especially a new comer to understand
      better how SerialEM works. 
      
      Quickly and accurately moving specimen to eucentric height is a
      frequently needed task. Everything is going to be easier if specimen
      is at eucentric height and objective lens at eucentric focus. I wrote
      a little document before how to use tilted-beam method to do this
      using SerialEM `"SerialEM HowTo: Positioning Z"
      <http://www.bio.brandeis.edu/KeckWeb/emdoc/en_US.ISO8859-1/articles/SerialEM-howto:positioningZ/>`_.
      In this note, I give you an improved version and hopefully it is
      easier to use and more robust too. 
      
.. _background_info:

Background Information 
----------------------

SerialEM has built-in task function to do eucentricity using stage-tilt
method. It is robust, but slower than beam-tilt method. Beam-tilt method is
opposite to autofocus function. When specimen is at eucentric height and
objective lens at eucentric focus, the tilted beams produce precise
overlapped image pair. This is how autofocus works. This principle can also
be used to adjust specimen height, because if we preset objective at
eucentric focus, cross-correlaton between two tilted beam images also
provides information how much specimen is away from eucentricity.
Specifically, beam-tilt method used here does a few things:

- it sets scope objective lens to eucentric focus value 
- it measures the defocus value for current specimen height using tilted-beam image pair,
- it then changes stage position to that reported value but in opposite direction, 
- it moves stage to determined direction and distance,
- and it iterates until the reported defocus value is close enough to zero.  

The beam-tilt method works very nicely most of time and it is pretty quick.
However, there are couples of things making it less perfect. First, the
signal becomes very weak when stage is already close eucentricity. We all
know the contrast is the lowest when focus matches z height. We can use
focus offset to increase the contrast, but non-linearity property causes some
inaccuracy. The calibrated standard focus value could also change a little
with time and scope condition. All these together makes it less robust. 

When we use SerialEM Low-Dose mode, we often give large focus offset such as
-200 microns to View area (I call it View beam) to make the View image good
contrast. If we can use this large defocused View beam to obtain tilt-beam
pairs for measuring defocus value accurately, that would be ideal. 

.. _Z_byV2_function:

Z_byV2 Function
---------------

The function code is below. 

.. code-block:: ruby
   :linenos:
   :caption: Function Z_byV2

   Function Z_byV2 2 0 iter offset
   Echo ===> Running Z_byV2 ...
   
   # for defocus offset of V in Low Dose, save it
   GoToLowDoseArea V
   SaveFocus

   ## set objective lens
   SetStandardFocus 0
   ChangeFocus $offset                         
   
   ## Adjust Z
   Loop $iter
      Autofocus -1 2
      ReportAutofocus 
      If ABS $repVal1 < 0.3
         Echo Close enough, break...
         Break
      Endif 
      Z = -1 * 0.72 * $repVal1               
      Z = ROUND $Z 2
      MoveStage 0 0 $Z
      echo Z has moved --> $Z microns 
   EndLoop

   ## restore the defocus set in V originally
   RestoreFocus
   EndFunction

The real difference between this and previous version *Z_byV* is an
additional line inserted after ``SetEucentricFocus``:

.. code-block:: ruby

   SetStandardFocus 0
   ChangeFocus $offset
   
These two lines will set objective lens to a specific strength; the large
defocus offset also offers good contrast. A special thing here is to measure
defocus value using a LD_View beam. The advantage of this command is to use
high contrast image.

.. code-block:: ruby

   Autofocus -1 2

This function should be called in script like this way:

.. code-block:: ruby

   CallFunction MyFuncs::Z_byV2 3 -288.32

Obviously, the -288.32 is to pass to variable $offset in the function, and 3
to iteration variable $iter.  

Now question is how to determine this offset value for accurate Z height for
and under current scope condition. 

.. _find_offset:

Find the Offset Value using Script FindOffset
---------------------------------------------

If we found the good "offset" value, it will be good for some time, at least
this session. So this like a short term calibration. Here is how to find it:

- Adjust specimen to Eucentriciy, using FEI interface tool or SerialEM task function
- run script as below.

.. code-block:: ruby
   :linenos:
   :caption: FindOffset

   ScriptName FindOffset
   
   # script to find proper offset value to run Z_byV2
   # assume speciment is ON the eucentricity 

   ## Eucentric Z
   ##
   #Eucentricity 3
   ReportStageXYZ 
   Z0 = $repVal3
   #Z0 = 187.81

   SetCameraArea V H
   ReportUserSetting AutofocusBeamTilt BT
   echo BT = $BT
   SetUserSetting AutofocusBeamTilt 1.6

   ## now find the offset
   # for initial offset, get a close value from current setting
   ReportUserSetting LowDoseViewDefocus
   offset = 0.72 * $repVal1   # or
   # offset = -153            # some starting value from previous run

   Loop 10
      CallFunction MyFuncs::Z_byV2 1 $offset
      ReportStageXYZ 
      Z = $repVal3
      diffZ = $Z - $Z0
      echo $diffZ
      If  ABS $diffZ < 1
         offset = ROUND $offset 2
         echo >>> Found "offset" is $offset
         echo >>> run "Z_byZ2 $offset" 
         Break
      Else 
         offset = $offset + $diffZ
      Endif 
   EndLoop

   NewArray X 1 11
   NewArray Y 1 11
   
   temp_offset = $offset - 10

   Loop 11 i
      CallFunction MyFuncs::Z_byV2 1 $temp_offset
      ReportStageXYZ 
      Z = $repVal3
      diffZ = $Z - $Z0
      Y[$i] = $diffZ
      X[$i] = $temp_offset
      temp_offset = $temp_offset + 2
   EndLoop 

   LinearFitToVars X Y
   echo $repVal1 $repVal2 $repVal3 $repVal4

   real_offset = - $repVal3 / $repVal2
   echo =====> $real_offset

   SetUserSetting AutofocusBeamTilt  $BT
   RestoreCameraSet

It uses function Z_byV2 to see which offset value to recover the Z height
determined early by other method. It first find an *offset* value that
recovers Z height within 1um(you can define 0.5), then it uses a fitting
method to refine this value to make it more accurate. If this script runs
and gives offset value as -153.51, then you should use the function with
this value.

.. note::

   This offset value changes when V beam size changes. Therefore, it makes
   sense to do this "calibration" of finding offset value after all the Low
   Dose area conditions are set and fixed. With the "good" offset value that
   gives good results, the program works very reliably, if the V beam
   doesn't change. For example, on our Krios, the V beam (called Low Dose
   area V) illumination area stays the same, the script works very well. 

.. code-block:: ruby

   CallFunction MyFuncs::Z_byV2 3 -153.51
   
It will move stage position to Eucentric Z height, almost magically! 

.. _damping_factor:

Note about Damping Factor
-------------------------

You might have noticed I used 0.72 in the value of Z movement:

.. code-block:: ruby 

   Z = -1 * 0.72 * $repVal1 
   
This is to compensate the non-linear behavior of autofocus measurement, with
the condition of large defocus offset used. For example, when the stage Z
position is -100 microns off from the eucentric height, the autofocus
measurement gives something like -136 microns. Therefore, using a proper
damping factor (100 / 136 ~ 0.73 here) can make the Z movement more
accurately to the target. Since this is a non-linear behavior, this damping
factor changes with Z. For example, when Z is off very little, say 5 micron,
the factor can be larger like 0.85. One would naturally try to find the
curve so to use a more accurate damping factor value in interpolating
fashion dynamically. However, if you think about backlash of stage movement,
it is the best to avoid any overshoot. Using a single, slightly smaller
value could help to keep stage move with backlash corrected when iterating a
few times. 0.72 is found to be a good number in our situation. 

What exactly the damping factor value should you use? I suggest you move
your stage 200 microns away, and you calculate the the ratio of 200 to
autofocus measurement value $repVal1 after ``ReportAutofocus`` ( damping
factor = 200 / $repVal1 ) and use the result.

If setting correctly, even your stage is more than 150 microns away, calling
the function with three rounds of iteration can bring the stage to eucentric
height close to within 0.5 microns in a few seconds. Amazing to me.  

.. note::
   Since 4.0 Testing, eucentricity task using titled beam has been built in
   SerialEM task. Please see SerialEM helpfile `Eucentricity by Focus Setup
   Dialog <https://bio3d.colorado.edu/SerialEM/betaHlp/html/hidd_z_by_g_setup.htm>`_.
