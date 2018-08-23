
.. _SerialEM_speed_speed_speed:

SerialEM Note: Speed, Speed and Speed
=====================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Created: 2017-12-26
:Updated: 2018-08-23

.. glossary::

   Abstract
      Speed of data collection is important, specially for a facility runnig 24/7 and for whole year long. If we can save a few seconds 
      for an exposure, which might not sound much, it becomes a lot accumulated in a year time. We could collect more data, help 
      more users if we are more efficient.  
      
      Thanks to the author and developer, David Mastronade, SerialEM is always under active development and improvement. I believe efficiency
      has been one of the goals for development. For example, positioning routine - Realign earlier always run two rounds: first round to 
      align to the center of a map or a center of a piece of a map; and second round to align to the actual target. Later version of SerialEM 
      can skip the first round of center align if it is done recently. This saves huge mount of time for single particle application. 
      
      In this document, I want to mention a few tips which you might or might not be aware of. Some of the things are related to newly added
      features of SerialEM. Some are from my personal experience. It would make me happy if they can also save you a few seconds.
      
.. _minimize_mag_switch:

Minimize Mag Switch 
-------------------

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

Order of Actions
----------------

When we use "Acquire at points ..." to collect single particle data, the default action of control mechanism is to move stage to the new item's stage position. And then it starts to run the actual collecting script like "LD". If the first action in the "LD" script is RealignToNavItem, the scope changes to the map mag, usually is View mag. Therefore, there are two physical actions here involved - stage move and mag switch. 

For whatever reason, before stage movement finishes, scope can not do anything. Since "RealignToNavItem" will also introduce stage movement, if we ask RealignToNavItem to take care of mag switching and stage movement, it can move stage while mag switching is happening. This can initiate two actions at the same time; therefore, saves time. 

This is new feature added not long ago. In late versions, there is a check box "Skip initial stage move" in "Navigator Acquire Dialog" window for this very purpose. 

.. _using_beam_tilt_for_Z:

Using Beam Tilt for Z Height Change
-----------------------------------

We all know how important is to have Z height close enough to eucentricity. If there is 10 micron off, then everything won't work quite right. 
SerialEM's built-in function "Eucentricity" is a robust function, straightward to use. However, it takes some time to run due to stage tiltig and settling time required. I wrote two scripts (functions) "Z_byG" and "Z_byV" to use beam tilting pair for the same job. They do not use stage tilt and takes less images, therefore, it runs faster. You do have to get calibration done for Standard Focus value though. 

In single particle data collection, sometimes, we have to make MMM maps from many meshes. The very first thing we do after getting to the center of a mesh is to fix the eucentricity height before map is collected. Using beam tilting method, it can save bit of time in this process. 

From my own experience, doing the eucentricity using beam tilting method even works fairly well in low range of magnifications. It seems to be accurate enough for parallel beam capable scope like Krios. 

.. _relax_stage:

Relaxing Stage After Moving to Target
-------------------------------------

For high quality movie stacks, even we use short frame time, the stage drift rate is still needed to be monitored. Some people use longer frame time due to worry the signal within frame being too weak for frame aligning later. In this case, drift control needs to be in place seriously, as stage naturally drifts and it can have different speeds at different time. 

SerialEM can ask stage to move with backlash retained or imposed. After such movement, relaxing stage stress by moving backwards a small 
distance can help stage settle down much faster, at least to a normal behaviour stage. This feature has been implemented into SeriaEM now. I have found it saves us huge mount of time for our routine data collection. I strongly recommend to upgrade to later version for this reason. 

The feature is used this way:

.. code-block:: ruby

   ResetImageShift 2 
   
2 means moving stage with backlash imposed or retained, and moving backward 25nm distance in the end. This small distance doesn't actually move the stage location, but helps relax the stage mechanical stress. You can also ask to move backwards a different distance by adding 2nd argument to the command, like below. 

.. code-block:: ruby

   ResetImageShift 2 50
 
This will move 50nm, rather than 25nm as default. 

Moving stage with backlash imposed takes extra time itself. Therefore, we don't want to move stage always using this way, but the final movement to the target. Here is a portion of a function called "AlignToBuffer" I wrote. 

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
  
Here, I asked stage to relax only at final round of iteration. If you use this function, you should update it to include this nice feature. 

Alternatively, we can also directly move stage backwards after ResetImageShift. This can be more accurate for final target position.

.. code-block:: ruby

   AlignTo $buffer      # comment out this line if last action is RealignToNavItem
   ResetImageShift

   ## relax
   # report shift in buffer A from last round of Align
   # move stage 0.025um in opposite directions
   ReportAlignShift
   shiftX = $repVal5
   shiftY = $repVal6
   
   # just in case it got a blank image so no shift found
   If $shiftX == 0 OR $shiftY == 0
      signX = 0
      signY = 0
   Else
      signX = $shiftX / ABS ( $shiftX )
      signY = $shiftY / ABS ( $shiftY )
   Endif
   
   moveX = -1 * $signX * 0.025
   moveY = -1 * $signY * 0.025
   echo Relaxing ...
   MoveStage $moveX $moveY

This can relaxing portion can be put into a function so the script can be neater. 

.. code-block:: ruby

   AlignTo $buffer      # comment out this line if last action is RealignToNavItem
   ResetImageShift
   CallFunction Relax

   Function Relax 0 0
   ## relax
   # report shift in buffer A from last round of Align
   # move stage 0.025um in opposite directions
   ReportAlignShift
   shiftX = $repVal5
   shiftY = $repVal6
   
   # just in case it got a blank image so no shift found
   If $shiftX == 0 OR $shiftY == 0
      signX = 0
      signY = 0
   Else
      signX = $shiftX / ABS ( $shiftX )
      signY = $shiftY / ABS ( $shiftY )
   Endif
   
   moveX = -1 * $signX * 0.025
   moveY = -1 * $signY * 0.025
   echo Relaxing ...
   MoveStage $moveX $moveY
   EndFunction
  
And if you allow final position with a little Image Shift (very OK with coma compensation in place), then this part positioning can be accurate and simple:

.. code-block:: ruby

   AlignTo $buffer      # comment out this line if last action is RealignToNavItem
   ResetImageShift
   CallFunction Relax
   AlignTo $buffer      # final round of align to buffer, so position is accurate
   
.. _using_compression:

Using Compression on K2 Data
----------------------------

Most people collect single particle data with K2 camera using Super-resolusion mode. One of the "hidden" advantages is that the Super-res raw frame data is in 4-bit unsigned integer type, and there are lot of zero's there. Such data can be compressed very effciently and losslessly using mature compression algorithms. Unfortunitely, MRC is not a file format that can directly use those algorithm libraries for compression. TIFF is. 

SerialEM implemented this compression feature in. It gives options not to apply gain reference before saving and to use compressed TIFF as saved data format. This might not sound a big deal, but the minimal size of lossless compressed raw dataset makes huge difference for a facility that runs constantly. The small dataset file size is not only beneficial for long term storage, but also makes it a lot faster to transfer and copy off. Network behaves very differently for a lot of 400MB datasets from a lot of 10GB datasets. 

Personally, I recommend to use compressed TIFF and without gain normalization applied for data saving format. 

.. _using_local_drive:

Using Local HDD or SSD
----------------------

It is usually fine to save the frame data directly onto a large size data storage network system. In our systems, a CIFS mount initiates a network drive on K2 computer so that we can directly save to that. However, in the case that the sotrage system is busy doing some other tasks such as transferring data to customers, being used by local image processing programs etc., directly saving to network drive could take extra time than saving onto local SSD drive on K2 computer. 

In our experience, it is best to save raw data on local SSD or HDD first, and then align frames using framewatcher (IMOD program) on-the-fly and let the *framewatcher* move the processed raw frames and aligned output average to network drive. This way, not only the loal SSD drive will never be filled, but also the network activities on the LAN are spreat out more evenly. Data collection won't slow down at all due to network performance. 

.. _multishot:

Using Multishot
---------------

Multi-shot method is perhaps the most efficient way for single particle data collection. It can speed up quite a bit. Please refer a separate note - **Tackle the Coma**. 
