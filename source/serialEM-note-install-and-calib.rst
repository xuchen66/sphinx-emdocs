
.. _SerialEM_install_and_Calib:

SerialEM Note: Installation and Calibration
===========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-11-12

.. glossary::

   Abstract
      When I helped a few sites to install and calibrate SerialEM, I had impression that most new users felt this process was very hard. I felt the same way when I initially learned to install and calibate SerialEM by myself. I even got frustrated and had to call him a few times. When I think back about all the trouble I had to install and calibrate SerialEM, I believe I would have an easier time if I had a brief guideline documents what steps to follow in order, and what to do in each steps. The helpfile from SerialEM is very complete to provide almost all information needed, but it is perhasp a lot to read and not clear where to start for a beginner. 
      
      I wanted to list some steps here to guide you through this initial installation and calibration phase. For more detailed information, you should always find it from helpfile. 
      
.. _installation:

Installation 
------------

Here are steps to follow. 

1. Ask David for the initial system file. Normally, you would fill out a "questionnaire" available at the ftp server - http://bio3d.colorado.edu/ftp/SerialEM/questionnaire.txt and send it to David. David will then create a framework file on the same ftp server for you to download. This framework file is a zip file, you can download it to local like Desktop and unzip it by double clicking on the file. Beside a sub-folder "Admin" generted under C:\\ProgramData\\SerialEM, the most important file in the framework is one initial system file called "SerailEMproperties.txt". You must have this file to get started. 

#. Make sure your camera computer and microscope computer are on the same network. For example, K2 computer needs to have a network interface with IP address 192.168.1.2, and FEI scope with 192.168.1.1. And they should be able to ping each other. You might be confused by Gatan's DM aleady being able to communicate with scope, as it can detect magnification change of scope. This DM connection is usually via serial port by a direct serial cable. SerialEM uses standard TCP/IP to communicate and therefore reqires a standard network setup in place. 

#. Decide which computer to install SerialEM. In theory, you can install SerialEM on either computer - camera or microscope. For K2 camera, it is normally installed on the K2 computer. 

#. Decide which type of executable to use. SerialEM builds for both 32 and 64-bit platforms. Unless you have to run it on a Windows XP, you should choose 64-bit. 

#. Download SerialEM software. You should start with the latest release version from ftp server at http://bio3d.colorado.edu/ftp/SerialEM/  and save it somewhere local like Desktop.  

#. Unzip the installation package file downloaded. You can double click on this file, it will unzip the program into C:\\Program Files\\SerialEM. The folder "SerialEM" will be created automatically if this isn't one. The new package will be unzipped into a new sub-folder, e.g. SerialEM_3-6-13_64. 
   
#. Quit Gatan DM if it is running. 

#. Double click on a file called *install.bat* in the package folder C:\\Program Files\\SerialEM\\SerialEM_3-6-13_64. This will copy some files into upper folder which is C:\\Program Files\\SerialEM, register DM plugin file and copy it to the Gatan plugin folder at C:\\ProgramData\\Gatan\\Plugin. 

#. Manually copy a file called *SEM-FEIServer.exe* from C:\\Program Files\\SerialEM on K3 computer to C:\\Program Files\\SerialEM on scope computer. This is a bridge program to control scope by passing the scope function calls from SerialEM main program on remote computer to scope scripting interface. Run the program by double clicking on it(it needs to run or SerialEM cannot control scope). 

#. On K2 computer, Edit *SerialEMproperties.txt* file in folder C:\\ProgramData\\SerialEM to have proper lines defining network properties. 

.. code-block:: ruby

   GatanServerIP 192.168.1.2
   GatanServerPort 48890 
   SocketServerIP 1 192.168.1.1
   SocketServerPort 1 48892
   
#. On K2 computer which SerialEM is to be installed, define a system environment variable SERIALEMCCD_PORT with the value 48890 or other selected port number, as described in the section in helpfile. 

If everything goes smooth, you should be able to start SerialEM and it should connect to "see" both scope and DM. 

.. _installation:

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

We all know how important is to have Z height close enough to eucentricity. If there is 10 micron off, then everything won't work quite right. 
SerialEM's built-in function "Eucentricity" is a robust function, straightward to use. However, it takes some time to run due to stage tiltig and settling time required. I wrote two scripts (functions) "Z_byG" and "Z_byV" to use beam tilting pair for the same job. They do not use stage tilt and takes less images, therefore, it runs faster. You do have to get calibration done for Standard Focus value though. 

In single particle data collection, sometimes, we have to make MMM maps from many meshes. The very first thing we do after getting to the center of a mesh is to fix the eucentricity height before map is collected. Using beam tilting method, it can save bit of time in this process. 

From my own experience, doing the eucentricity using beam tilting method even works fairly well in low range of magnifications. It seems to be accurate enough for parallel beam capable scope like Krios. 

.. _relax_stage:

4) Relaxing Stage After Moving to Target
----------------------------------------

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

.. _using_compression:

5) Using Compression on K2 Data
-------------------------------

Most people collect single particle data with K2 camera using Super-resolusion mode. One of the "hidden" advantages is that the Super-res raw frame data is in 4-bit unsigned integer type, and there are lot of zero's there. Such data can be compressed very effciently and losslessly using mature compression algorithms. Unfortunitely, MRC is not a file format that can directly use those algorithm libraries for compression. TIFF is. 

SerialEM implemented this compression feature in. It gives options not to apply gain reference before saving and to use compressed TIFF as saved data format. This might not sound a big deal, but the minimal size of lossless compressed raw dataset makes huge difference for a facility that runs constantly. The small dataset file size is not only beneficial for long term storage, but also makes it a lot faster to transfer and copy off. Network behaves very differently for a lot of 400MB datasets from a lot of 10GB datasets. 

Personally, I recommend to use compressed TIFF and without gain normalization applied for data saving format. 

.. _using_local_drive:

6) Using Local HDD or SSD
-------------------------

It is usually fine to save the frame data directly onto a large size data storage network system. In our systems, a CIFS mount initiates a network drive on K2 computer so that we can directly save to that. However, in the case that the sotrage system is busy doing some other tasks such as transferring data to customers, being used by local image processing programs etc., directly saving to network drive could take extra time than saving onto local SSD drive on K2 computer. 

In our experience, it is best to save raw data on local SSD or HDD first, and then align frames using framewatcher (IMOD program) on-the-fly and let the *framewatcher* move the processed raw frames and aligned output average to network drive. This way, not only the loal SSD drive will never be filled, but also the network activities on the LAN are spreat out more evenly. Data collection won't slow down at all due to network performance. 

