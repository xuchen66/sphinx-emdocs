.. _post-process-k2-frames:

Post Processing K2 Frames from SerialEM Data Collection
=======================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-02-13

.. glossary::

   Abstract
      At UMASS Cryo-EM Facility, we use SerialEM to collect data for both single particle and tomography applications. 
      And we do that on both Talos Arctica and Titan Krios with K2 cameras. 
      
      For single particle, 
      usually we save frames in compressed TIFF format without gain normalized (select Dark Substracted in camera 
      setup window). One of the advantages of doing this is to reduce data size. For Super-resolution frames, the 
      raw frame data is in unsigned 4-bit. Pixel values are in the range of 0 - 15. For weak beam, there are a lot of 
      zeros there too. With lossless compression methods, such data can be comprssed into much smalller filesize without losing 
      image information. Therefore, instead of applying gain normalized reference to all the frames, we leave the raw 
      data compressed and saved to the disk and we later do post-processing to recover the full information of the image data. 
      
      In this doc, the procedures to do post processing are presented here for your reference. 

.. _k2-on-Talos:

For K2 camera on Talos Arctica 
------------------------------

The DM camera configuration for camera orientaion setup for K2 camera on Talos Arctica is 270 degree rotation and Flip along Y. The idea is that with a proper orientation setup, the image from camera is at the same orientation as on FluCam. This is initial condition for SerialEM setup. 

However, when saving frames from single particle data collection, this orientation might not always be needed. As long as all the data is saved the same way for the entire session, it is fine with and without this orientation applied to all the frames before saving. This option is from a check box "Save frames without rotation/flip to standard orientation" in K2 Frame File Option dialog window.  

If you saved frame as un-normalized TIFF, and you need to recover the image stack to a MRC format and apply gain reference file and mast out defects, here are steps.

1. check out the orietation from header of the file. 

.. code-block:: ruby

   RO image file on unit   1 : YURI_B1_G1-SuperRes_784_Feb06_12.39.01.tif     Size=     621334 K

                       This is a TIFF file.

   Number of columns, rows, sections .....    7676    7420      40
   Map mode ..............................    0   (byte)
   Start cols, rows, sects, grid x,y,z ...    0     0     0    7676   7420     40
   Pixel spacing (Angstroms)..............  0.8714     0.8714     0.8714
   Cell angles ...........................   90.000   90.000   90.000
   Fast, medium, slow axes ...............    X    Y    Z
   Origin on x,y,z .......................    0.000       0.000       0.000
   Minimum density .......................   0.0000
   Maximum density .......................   255.00
   Mean density ..........................   127.50
   tilt angles (original,current) ........   0.0   0.0   0.0   0.0   0.0   0.0
   Space group,# extra bytes,idtype,lens .        0        0        0        0

        1 Titles :
   SerialEMCCD: Dose frac. image, scaled by 16.00  r/f 0

The last parameter in title line shows the orientation of imaging. Here is 0 - no rotation and no flip. 

- Check Gun Lens, Extracting Voltage, High Tension are set at correct values.
- Stare for a few seconds at the focused beam at the highest SA mag, to see if the beam has good shape and there is no shaking or jumping.  
- From Direct Alignment, do gun tilt, beam tilt PP, Coma-Free alignment if needed. 
- Check Thon Ring at roughly the same condition (mag, dose) as your image condition. Make sure there is no obvious frequency cutoff, and Thon Ring reaches the resolution as in good condition. 
