
.. _SerialEM_speed_speed_speed:

SerialEM Note: Speed, Speed and Speed
=====================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-10-24

.. glossary::

   Abstract
      Speed of data collection is important, specially for a facility runnig 24/7 and for whole year long. If we can save a few seconds for 
      an exposure, it might not sound much. But it becomes a lot in a year time. We could collect more data, help more users if we are more 
      efficient.  
      
      Thanks to the author and developer, David Mastronade, SerialEM is always under active development and improvement. I believe efficiency
      has been one of the goals for development. For example, positioning routine - Realign earlier always run two rounds: first round to 
      align to the center of a map or a center of a piece of a map; and second round to align to the actual target. Later version of SerialEM 
      can skip the first round of center align if it is done recently. This saves huge mount of time for single particle application. 
      
      In this document, I want to mention a few tips which you might or might not be aware of. Some of the things are related to newly added
      features of SerialEM. Some are from my personal experience. It would make me happy if they can also save you a few seconds.
      
.. _minimize_mag_switch:

1) Minimize Mag Switch 
----------------------

Switching between mags takes time. You can definitely feel the slowness of mag switching, say bewteen 1250X and 130kX. You might think of
turning off some lens normalization via FEI interface, but I always worry the stability of the system might suffer. I am not trying to save
time from there. 

However, I found that I could save time from this positioning actions:

.. code-block:: ruby

   RealignToNavItem 1
   Copy A P
   ResetImageShift
   
   View
   AlignTo P
   ResetImageShift
   
This little script uses the last image of Realign routine which has some image shift in it, as reference to do another round of aligning 
and ResetImageShift to get rid of image shift. It seems to be flawless and it is actually working. But I noticed the scope switched from View 
mag to Record mag for short period of time and then switch to View mag again during the actions. There is an extra switch there! At first, 
I was very puzzled, then I realized that I had been using a wrong command! 

The problem is caused by the argument 1 in command line:

.. code-block:: ruby

   RealignToNavItem 1
   
The argument "1" here means scope will resume to the state before realigning routine. And that state is high, record mag from exposure of 
last navigator point. Therefore, with above script, scope switch to View mag to perform realign function and then it siwthes back to record mag. It then switches to View mag again when at line of 

.. code-block:: ruby

   View
   
If I put "0" as argument for "RealignToNavItem" like here:

.. code-block:: ruby

  RealignToNavItem 0
  
then scope stays in View mag. It at least saves 5 seconds! 

.. _order_of_actions:

2) Order of Actions
-------------------

When we use "Acquire at points ..." to collect single particle data, the default action of control mechanism is to move stage to the new item's stage position. And then it starts to run the actual collecting script like "LD". If the first action in the "LD" script is RealignToNavItem, the scope changes to the map mag, usually is View mag. Therefore, there are two physical actions here involved - stage move and mag switch. 

For whatever reason, before stage movement finishes, scope can not do anything. Since "RealignToNavItem" will also introduce stage movement, if we ask RealignToNavItem to take care of mag switching and stage movement, it can move stage while mag switching is happening. This can initiate two actions at the same time; therefore, saves time. 

This is new feature added not long ago. In late versions, there is a check box "Skip initial stage move" in "Navigator Acquire Dialog" window for this very purpose. 

.. _using_beam_tilt_for_Z:

3) Using Beam Tilt for Z Height Change
--------------------------------------

We all know how important to have Z height close enough to eucentricity. If there is 10 micron off, then everything won't work quite right. 
SerialEM's built-in function "Eucentricity" is a robust function, straightward to use. However, it takes some time to run due to stage tiltig and settling time required. I wrote two script (functions) "Z_byG" and "Z_byV" to use beam tilting pair for the same job. They do not use stage tilt and takes less images, thus it runs faster. One do have to get calibration done for Standard Focus value though. 

In single particle data collection, sometimes, we have to make MMM maps from many meshes. The very first thing we do after getting to the center of a mesh is to fix the eucentricity height before map is collected. Using beam tilting method, it can save bit of time in this process. 

From my own experience, doing the eucentricity using beam tilting method even works fairly well in low range of magnifications. It seems to be accurate enough for parallel beam capable scope like Krios. 

.. _relax_stage:

4) Relaxing Stage After Moving to Target
----------------------------------------

For high quality movie stacks, even we use short frame time, the stage drift rate is still needed to be monitored. Some people use longer frame time due to worry the signal within frame being too weak for frame aligning later. In this case, drift control needs to be in place seriously, as stage naturally drifts and it can have different speeds at different time. 

SerialEM can ask stage to move with backlash retained or imposed. After such movement, relaxing stage stress by moving backwards a small 
distance can help stage settle down much faster, at least to a normal behaviour stage. This feature has implemented into SeriaEM now. I have found it saves us huge mount of time for our routine data collection. I strongly recommend to upgrade to later version for this reason. 

The feature is used this way:

.. code-block:: ruby

   ResetImageShift 2 
   
2 means moving stage with backlash imposed or retained, and moving backward 25nm distance in the end. This small distance doesn't actually move the stage location, but helps relax the stage mechanical stress. You can also ask to move backwards a different distance by adding 2nd argument to the command, like below. 

.. code-block:: ruby

   ResetImageShift 2 50
 
This will move 50nm, rather than 25nm as default. 

Moving stage with backlash imposed takes extra time itself. Therefore, we don't want to move stage this way, but the final movement to the target. Here is a portion of a function called "AlignToBuffer" I wrote. 

.. code-block:: ruby

   ## align
   Loop $iter ind
       $shot
       # still need crop, for Camera which doesn't do flexible sub-size like FEI cameras
       ImageProperties A
       XA = $reportedValue1
       YA = $reportedValue2
       If $XA > $XP OR $YA > $YP
           echo CallFunction  MyFuncc::CropImageAToBuffer $buffer
           CallFunction  MyFuncs::CropImageAToBuffer $buffer
       Endif
       AlignTo $buffer
       If $ind == $iter  	# last round of loop, relax stage
         ResetImageShift 2
       Else 
         ResetImageShift
       Endif
   EndLoop 
  
Here, I asked stage to relax only at final round of iteration. If you use this function, you need to update it to include this nice feature. 

.. _using_compression:

5) Using Compression on K2 Data
-------------------------------

.. _using_local_drive:

6) Using Local HDD or SSD
-------------------------


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
   offset value after all the Low Dose area conditions are set and fixed. 

.. code-block:: ruby

   CallFunction MyFuncs::Z_byV2 -290.5
   
It will move stage position to Eucentric Z height, almost magically! 

