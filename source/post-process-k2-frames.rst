.. _post-process-k2-frames:

Post Processing K2 Frames from SerialEM Data Collection
=======================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2017-02-13
:Last_Updated: 2019-05-07

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

.. code-block:: none

   $header YURI_B1_G1-SuperRes_636_Feb05_10.42.09.tif

   RO image file on unit   1 : YURI_B1_G1-SuperRes_636_Feb05_10.42.09.tif     Size=     805815 K

                       This is a TIFF file.

   Number of columns, rows, sections .....    7676    7420      80
   Map mode ..............................    0   (byte)
   Start cols, rows, sects, grid x,y,z ...    0     0     0    7676   7420     80
   Pixel spacing (Angstroms)..............  0.8714     0.8714     0.8714
   Cell angles ...........................   90.000   90.000   90.000
   Fast, medium, slow axes ...............    X    Y    Z
   Origin on x,y,z .......................    0.000       0.000       0.000
   Minimum density .......................   0.0000
   Maximum density .......................   15.000
   Mean density ..........................   7.5000
   tilt angles (original,current) ........   0.0   0.0   0.0   0.0   0.0   0.0
   Space group,# extra bytes,idtype,lens .        0        0        0        0

      1 Titles :
   SerialEMCCD: Dose frac. image, scaled by 1.00  r/f 0


The last parameter in title line shows the orientation of imaging. Here is 0 - no rotation and no flip. In this case, Gatan gain reference file doesn't need to do any rotation and flip. We simply convert it into MRC format. 

2. Convert Gatan gain reference .dm4 into MRC format. 

.. code-block:: none

   $dm2mrc GatanGainRef.dm4 GatanGainRef.mrc
   
3. Use "clip" to apply gain reference and deal with defects all in a single command line (later IMOD can take tiff file format as input directly). I quote a section from SerialEM helpfile here:

<helpfile>

       Once you have the reference in the right orientation, you can use the program 'clip' in IMOD to apply gain normalization (and defect correction with version 4.8.6 or higher).  In the following, 'scalingFactor' is the regular scaling factor applied to summed images, 'fileWithFrames' is the data file to normalize, 'gainReference.mrc' is the reoriented gain reference, and 'normalizedFrames.mrc' is the desired output file. The alternatives for GMS 2.3.0 or lower are:

       Counting mode, not packed:  The data need to be scaled to preserve precision after normalization.  The command is
       
       .. code-block:: none
       
            clip mult -n scalingFactor  fileWithFrames.mrc  gainReference.mrc  normalizedFrames.mrc
       
       Super-resolution mode, not packed:  The data need to be scaled to preserve precision after normalization.  To have the same scaling by 16 that the plugin would apply, the command is
       
       .. code-block:: none
       
            clip mult -n 16  fileWithFrames  gainReference.mrc  normalizedFrames.mrc
            
       but if you want to apply the regular scaling factor, the output will need to be integers and the command is
       
       .. code-block:: none
       
            clip mult -n scalingFactor  -m 1  fileWithFrames  gainReference.mrc  normalizedFrames.mrc
       
       Counting mode, packed as bytes:  The data need to be scaled to preserve precision and output as integers to preserve the range.  The command is
       
       .. code-block:: none
       
            clip mult -n scalingFactor  -m 1  fileWithFrames  gainReference.mrc  normalizedFrames.mrc
       
       Super-resolution mode, packed as 4-bit numbers: By default, the data will be scaled by 16 when unpacking with normalization, so the command to get this scaling is just
       
       .. code-block:: none
       
            clip unpack  fileWithFrames  gainReference.mrc  normalizedFrames.mrc
       
       but if you want to apply the regular scaling factor, the output will need to be integers and the command is
       
       .. code-block:: none
       
            clip unpack -n scalingFactor  -m 1  fileWithFrames  gainReference.mrc  normalizedFrames.mrc
       
       It is also possible to remove extreme values from the data at the same time with the '-h' and '-l' options.  For example, adding '-h 6 -l 1' after the 'unpack' will replace all values above 6 with 1.

       To apply defect correction to files from GMS 2.3.1 or higher, add '-D defects...txt' before 'fileWithFrames' in the appropriate command, where 'defects...txt' is the file saved by the plugin.
       
       In IMOD version 4.8.41 or higher, all programs can read 4-bit files directly.  The 'clip unpack' command has thus been changed so that it can be used for normalizing any kind of data, and it can also be invoked as either 'clip unpack' or 'clip norm'.  A command that works for all of the above cases is

      .. code-block:: none

            clip norm -n scalingFactor  -m 1  fileWithFrames  gainReference.mrc normalizedFrames.mrc

        where the default scaling factor is 16, extreme values can be removed with '-l' and '-h' options, and '-D defects...txt' would be added for files from GMS 2.3.1 or higher.  With IMOD 4.9.2/4.10.1 or higher, you can add add '-R -1' and use the DM reference directly instead of a rotated reference.

       For K3 frames, you should specify a scaling factor of 32.

</helpfile>
   
