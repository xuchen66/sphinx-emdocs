
.. _SerialEM_K3_installed_on_Talos:

SerialEM Note: K3 is installed on Talos
=======================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2018-10-25
:Last_Updated: 2018-10-25 

.. glossary::

   Abstract
      We have upgraded from K2 to K3 on Talos Arctica. Overall, we have had more positive experience than negative one. 
      We are now collecting the first benchmark data, just one week after K3 installation was completed. Of course, we are using 
      SerialEM on it. 
      
      I wanted to share our experience here. Hope it helps people to prepare for their own installation.    
      
.. _installation:

Installation Went Smooth 
------------------------

The installation was fairly smooth. The engineers had installed K3 at other sites before so they have quite some experience 
already. Total installation only took three full days. We had two K3 systems in crates - one for Talos and one for Krios. 
Therefore, one unique advantage we had was an extra set of identical hardware components to swap test when needed. 

There were two failed hardware in the original package for Talos, one was processing board and the other was MIB box and 
cable. Once these two faulty hardware components were replaced with ones in Krios K3 crates, the system came up nicely. 

One can imagine, without spare hardware parts to trial and test, the installation could have taken a lot longer. We were lucky.

.. _serialem:

SerialEM Control 
----------------

It was pretty easy to get good control of K3 camera based on previous K2 camera setup. There are only a few things we needed to redo for 
SerialEM calibration. 

1. Image Shift calibration for all the mags to be used

2. Pixelsizes at mag index 4, 16 and 17. They are 46X(LM), 2300X(LM) and 1250X (M). Note: *this is only to get SerialEM going, precise 
measurement of pixelsize at final image mags will need to be done carefull using different methods.* 

3. Stage Shift calibration for mag index 4, 16 and 17. They are 46X(LM), 2300X(LM) and 1250X (M). Double check tilting axis angles from 
this step too. 

The K3 camera section of properties is below:

.. code-block:: ruby

    CameraProperties	     1
    Name	                     K3
    K2Type	                     3
    DMGainReferenceName	     K3-18140113 Gain Ref. x1.m0.dm4
    # THESE 5 WILL NEED CHANGING IF CAMERA ORIENTATION CHANGES
    CameraSizeX	             5760
    CameraSizeY	             4092
    SizeCheckSwapped             1
    RotationAndFlip              0		# accedently 1 before
    DMRotationAndFlip            0
    #UsableArea                  0 0 3712 3840 	# top left bottom right!
    UseSocket	             0
    MakesUnsignedImages	     1
    XMustBeMultipleOf	     4
    YMustBeMultipleOf	     2
    FourPortReadout	             0
    Binnings	             1 2 3 4 5 6 8
    BasicCorrections	     49
    HotPixelsAreImodCoords	     1
    #DarkXRayAbsoluteCriterion   20
    #DarkXRaySDCriterion	     15
    #DarkXRayRequireBothCriteria 1
    MaximumXRayDiameter	     6
    BeamBlankShutter	     0
    OnlyOneShutter	             1
    StartupDelay                 1.195
    ExtraBeamTime                0.10
    BuiltInSettling              0.0 
    ShutterDeadTime	             0.00		
    MinimumDriftSettling	     0.05
    MinimumBlankedExposure       0.35
    ExtraUnblankTime	     0.012
    ExtraOpenShutterTime	     0.12
    Retractable	             1
    InsertionDelay	             5.0
    RetractionDelay	             3.0
    GIF	                     0
    Order                        2
    FilmToCameraMagnification    1.31	# orig=1.342
    PixelSizeInMicrons	     5.0  
    #CountsPerElectron	     #37.55	not needed for K3 # measured at 3.15 e/p/s
    ExtraRotation	             0.
    # MagIndex  DeltaRotation (999 not measured)  SolvedRotation (999 not measured)   Pixel size (nm, 0 not measured)
    ##RotationAndPixel 33 0.04 999 0.0749
    #RotationAndPixel 1 999 -3.9 0
    #RotationAndPixel 4 999 -102.4 0
    RotationAndPixel 3 999 -94.4 0
    #RotationAndPixel 16 999 -4.9 1.797
    RotationAndPixel 16 999 -94.4 1.74	#k2=1.797
    RotationAndPixel 17 999 90.9 3.291	#k2=3.396

    EndCameraProperties

.. _shutter:

Shutter Control 
---------------

There are a number of things one should pay attention to, in my opinion. The shutter control is the top 1 on the list. 

**Shutter control**. This is perhaps the most important thing you do not want to miss. If shutter control is not working properly, 
you might have sample burned without notice. Normally, if shutter control is not working, you will have hard time preparing gain 
reference. So you might notice it. However, since we are not required to prepare gain reference often in daily bases, if it stops working, 
you might or might not notice it promptly. You might still get image, but your sample might not be protected as it should be. 

With properly working shutter, the beam will get blanked if following conditions are all met:

1. Hardware components are communicating with each other normally. 
#. DM is running and K3 camera is in inserted position.
#. Software configuration in DM interface - Camera Configuration has set properly as idle state for shutter one "Pre-specimen" 
to be closed. There is normally only single shutter cable from Gatan MIB box - shutter 1 connecting to FEI shutter router "CSU" box at one 
of the channels. This is an BNC connctor. In our case, it connects to Channel C - *Blanker*. Make sure it is the blanker, as the other one 
on CSU channel "shutter" means below specimen. 
#. large screen of scope is in raised position (large screen is a switch to trigger sending or retracting 5V signal through the 
shutter cable.).
#. In FEI scope "CCD/TV Camera" interface, make sure the fake camera name assgined for K2/K3 (Falcon in our case) is selected from the 
list and "insert" button is in yellow color. Click on it if this is not. This is to tell FEI CSU shutter router to let Channel C take 
control.

In our case, when all above conditions are met, the green LED "shutter" indicator on K3 power supply unit should be on. The "Blanker" 
orange color LED indicator on Channel C will be lit when idle. It blinks when a shot is taken from DM or SerialEM. If you take an 
exposure for 3 seconds, the LED will disappear for 3 seconds. The two images below show Gatan Power Supply unit and FEI CSU unit:

**Fig.1 Gatan K3 Camera Power Supply Unit**

.. image:: ../images/K3-PS.png
   :scale: 75 %
..   :height: 544 px
   :width: 384 px
   :alt: DUMMY instance property
   :align: center

**Fig.2 FEI Shutter Router Unit (CSU)**

.. image:: ../images/CSU.png
   :scale: 75 %
..   :height: 544 px
   :width: 384 px
   :alt: DUMMY instance property
   :align: center
   
Please note: at least in our case, there is nothing change to monitor shutter status from CCD/TV camera interface or FEI's Jave program 
"Shutter Blanker Monitor". This is probably due to Gatan camera is "external" camera.

To make absolute sure the shutter is working properly, check it with burn marker method. You lift large screen and wait for sometime and 
take an image of ice sample or plastic sample in lower mag, and you check if you see any sign of burn marker.   

.. _watch:

Other things to Watch
---------------------

#. Make sure your camera computer and microscope computer are on the same network. For example, K2 computer can be configured to have a network interface with IP address 192.168.1.2, and FEI scope with 192.168.1.1. And they should be able to *ping* each other. You might be confused by Gatan's DM aleady being able to communicate with scope, as it can detect magnification change of scope. However, this DM connection to scope is usually via serial port by a direct serial cable. SerialEM uses standard TCP/IP to communicate to a remote computer and therefore requires a standard network setup in place. 

#. Decide which computer to install SerialEM. In theory, you can install SerialEM on either computer - camera or microscope. For K2 camera, SerialEM should be normally installed on the K2 computer, as K2 image returning to SerialEM is usually faster than via network. 

#. Decide which type of executable to use. SerialEM builds for both 32 and 64-bit platforms. Unless you have to run it on a Windows XP, you should choose 64-bit. 

#. Download SerialEM software. You should start with the latest release version from ftp server at http://bio3d.colorado.edu/ftp/SerialEM/  and save it somewhere local like Desktop.  

#. Unzip the installation package file downloaded. You can double click on this file, it will unzip the program into C:\\Program Files\\SerialEM. The folder "SerialEM" will be created automatically if there isn't one already. The new package content will be unzipped into a new sub-folder, e.g. SerialEM_3-6-13_64. 
   
#. Quit Gatan DM if it is running. 

#. Double click on a file called *install.bat* in the package folder C:\\Program Files\\SerialEM\\SerialEM_3-6-13_64. This will copy some files into upper folder which is C:\\Program Files\\SerialEM, register DM plugin file and copy it to the Gatan plugin folder at C:\\ProgramData\\Gatan\\Plugin. 

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

   - Waffle grating grid is good and handy for pixel size calibration, but it is not ideal for Image Shift and Stage Shift calibrations, as the waffle pattern might screw up the correlation in the calibration procedures. I found the normal Quantifoil grid with some 10nm Au particles absorbed onto can be very good for normal calibration purpose. I glow discharge a Quantifoil grid and add 1 *ul* deca-gold solution on the grid and let it dry. 
   - Most of SerialEM actions are cross-correlation based including calibration. Therefore, a clean and recent preparation of camera gain reference file is desired, because it will help to have less screw-up due to fixed noise pattern dominating the cross-correlation. 
