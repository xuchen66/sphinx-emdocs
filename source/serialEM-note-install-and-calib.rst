
.. _SerialEM_install_and_Calib:

SerialEM Note: Installation and Calibration
===========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2017-11-12
:Last_Updated: 2019-12-20

.. glossary::

   Abstract
      When I helped a few sites to install and calibrate SerialEM, I had impression that most new users felt this process was very hard. I felt the same way when I initially learned to install and calibate SerialEM by myself. I even got frustrated and had to call David for a few times. When I think back about all the troubles I had to install and calibrate SerialEM, I believe I would have an easier time if I had a brief guideline document for what steps to follow in order, and what to do in each step. The helpfile from SerialEM is very complete to provide almost all information needed, but it is perhasp a lot to read and not clear where to start for a beginner. 
      
      I wanted to list some steps here to guide you through this initial installation and calibration phase. It is like a crush list. For more detailed information, you should always find it from helpfile. 
      
.. _installation:

Installation 
------------

Here are steps to follow. 

1. Ask David for the initial system file. Normally, you would fill out a "questionnaire" available at the ftp server - http://bio3d.colorado.edu/ftp/SerialEM/questionnaire.txt and send it to David. David will then create a framework file on the same ftp server for you to download. This framework file is a zip file, you can download it to local like Desktop and unzip it by double clicking on the file. Beside a sub-folder "Admin" created under C:\\ProgramData\\SerialEM, the most important file in the framework is one initial system file called "SerailEMproperties.txt". You must have this file to get started. Please refer to the SerialEM webpage for the latest information regarding this. 

#. Make sure your camera computer and microscope computer are on the same network. For example, K2 computer can be configured to have a network interface with IP address 192.168.1.2, and FEI scope with 192.168.1.1. And they should be able to *ping* each other. You might be confused by Gatan's DM aleady being able to communicate with scope, as it can detect magnification change of scope. However, this DM connection to scope is usually via serial port by a direct serial cable. SerialEM uses standard TCP/IP to communicate to a remote computer and therefore requires a standard network setup in place. 

#. Decide which computer to install SerialEM. In theory, you can install SerialEM on either computer - camera or microscope. For K2 camera, SerialEM should be normally installed on the K2 computer, as K2 image returning to SerialEM is usually faster than via network. 

#. Decide which type of executable to use. SerialEM builds for both 32 and 64-bit platforms. Unless you have to run it on a Windows XP, you should choose 64-bit. 

#. Download SerialEM software. You should start with the latest release version from ftp server at http://bio3d.colorado.edu/ftp/SerialEM/  and save it somewhere local like Desktop.  

#. Unzip the installation package file downloaded. You can double click on this file, it will unzip the program into C:\\Program Files\\SerialEM. The folder "SerialEM" will be created automatically if there isn't one already. The new package content will be unzipped into a new sub-folder, e.g. SerialEM_3-6-13_64. 
   
#. Quit Gatan DM if it is running. 

#. Right click on a file called *install.bat* in the package folder C:\\Program Files\\SerialEM\\SerialEM_3-6-13_64 and select 'Run as Administrator'. This will copy some files into upper folder which is C:\\Program Files\\SerialEM, register DM plugin file and copy it to the Gatan plugin folder at C:\\ProgramData\\Gatan\\Plugin. 

#. Manually copy a file called *FEI-SEMServer.exe* from C:\\Program Files\\SerialEM on K2 computer to C:\\Program Files\\SerialEM on scope computer. This is a bridging program to control scope by passing the scope function calls between SerialEM main program on remote K2 computer and the scope scripting interface. Run the program by double clicking on it(it needs to run or SerialEM cannot control scope). This is 32-bit application, runs on both 32 and 64-bit Windows platforms. So there is only one such executable to run on Windows 7, XP or 2000 Windows OS. 

#. On K2 computer, Edit *SerialEMproperties.txt* file in folder C:\\ProgramData\\SerialEM to have proper lines in general property area to define network properties. 

.. code-block:: ruby

   #GatanServerIP 192.168.1.2
   GatanServerIP 127.0.0.1
   GatanServerPort 48890 
   SocketServerIP 1 192.168.1.1
   SocketServerPort 1 48892

11. On K2 computer which SerialEM is to be installed, define a system environment variable SERIALEMCCD_PORT with the value 48890 or other selected port number, as described in the section in helpfile. 

If everything goes north, you should be able to start SerialEM and it should connect to "see" both scope and DM. Congratulations!

.. _Calibration:

Calibration 
-----------

Although most of calibration results will be written into another system file *SerialEMcalibraions.txt* when you save the calibrtion from Calibretion menu, there are a few places you need to manully edit the *SerialEMproperties.txt* to take in the calibration results. These include pixelsize and tilting axis angle - they are more like instrument parameters. 

0. Determine camera orientation configuration. Make sure the image orientation from camera shot agree with that of on large screen or FluCam. If it doesn't, try to adjust the camera orientation of Gatan K2 camera from Camera - Configuration. You can use beamstop to help.  You should add a property entry to reflect the DM configuration so SerialEM takes care of it even someone might have changed DM configuration. 

.. code-block:: ruby

   DMRotationAndFlip 7

#. Edit property file to define the camera configuration information about orientation determined by step 0. SerialEM will return to main display with proper orientation. This is initial starting point for all the calibrations.

.. code-block:: ruby

   RotationAndFlip 7

2. SerialEM - Calibration - List Mag. Scope will go through all the mags and list them on log window, from lowest to highest. Check it with what are in *SerialEMproperties.txt*, update that if needed.  

#. Load standard waffle grating grid (TedPella Prod.# 607, http://www.tedpella.com/calibration_html/TEM_STEM_Test_Specimens.htm#_607).

#. Start with lowest magnification above LM range. On Talos, it is 1250X. At close to Eucentricity, and clost to eucentric focus. 

#. Take a T shot with 2x binning on a K2 camera, make sure the counts are neither too low nor too high. 

#. Take a T shot, then Calibration - Pixel Size - Find Pixel Size. The log window shows both mag index and pixel size. Edit *SerialEMproperties.txt* to add a line like below in K2 camera property section. 

.. code-block:: ruby

   # MagIndex  DeltaRotation (999 not measured)  SolvedRotation (999 not measured)   Pixel size (nm, 0 not measured)
   RotationAndPixel 17 999 999 3.396
   
Here, 17 is mag index for 1250X, and 3.396 is pixel size in nm just calibrated.

7. Calibration - Image & Stage Shift - IS from Scratch.

#. Calibration - Image & Stage Shift - Stage Shift.

#. Calibration - Administrator, turn it on.

#. Calibration - Save Calibration. 

#. Take the tilting axis value (e.g. 86.1) from step 8 - stage shift calibration, edit it into the 2nd "999" in *SerialEMproperties.txt* like below.

.. code-block:: ruby

   RotationAndPixel 17 999 86.1 3.396

.. Note:: 
   The pixel size and tilting axis can just be done for a couple of switching mags such as the lowest M and the highest LM. 
   SerialEM uses these a couple of calibrations and all the Image Shift calibration to inpterpolate to obtain the pixelsize and tilting 
   axis angle for all other magnifications. This is very cute. 

12. Increase Mag by 1 click and do Calibration - Image & Stage Shift - Image Shift

#. Repeat above step to cover all the magnification till the highest to be used such as 100kX. 

#. Now bring scope to highest LM mag (2300X on Talos), remove Obj aperture; do pixel size, image shift calibration, stage shift calibration; edit property file to take in pixel size and tilting axis angle and save the calibrations. 

#. Decrease Mag by 1 click and do Calibration - Image & Stage Shift - Image Shift

#. Repeat above step to cover all magnication till the lowest to use like 46X. 

#. At about 20kX, do Autofocus calibration (only need to do at single mag).

#. Beam Crossover claibration

#. Start with most used spotsize like 7, do Beam Intensity calibration 

#. repeat Beam Intensity Calibration for all other spot sizes likely to be used - 3,4,5,6,8,9.

#. At one mag like 5000X, using spot size 9, do Beam Shift Calibration (only need to do at single mag).

#. Usually, people use the lowest M mag for Low Dose View beam and with large defocus offset such as -200 or -300 mirons. You need to the calibrate High-Defocus Mag for this View mag. This will make stage shifts still good for such large defocus, as they are interpolated with defocus offset. 

.. Note::

   - Waffle grating grid is good and handy for pixel size calibration, but it is not ideal for Image Shift and Stage Shift calibrations, as the waffle pattern might screw up the correlation in the calibration procedures. I found the normal Quantifoil grid with some 10nm Au particles absorbed onto can be very good for normal calibration purpose. I glow discharge a Quantifoil grid and add 1 *ul* deca-gold solution on the grid and let it dry. I found that standard **PtIr** grid for TFS to perfom Thon Ring test also works very well for calibration purpose. 
   - Most of SerialEM actions are cross-correlation based including calibration. Therefore, a clean and recent preparation of camera gain reference file is desired, because it will help to have less screw-up due to fixed noise pattern dominating the cross-correlation. 
