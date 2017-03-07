.. _align-k2-frames-using-imod:

Align Movie Frames with SerialEM and IMOD Programs
==================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-03-17

.. glossary::

   Abstract
      IMOD can align movie frames nicely and very quickly. In late version of IMOD, the program *AlignFrames* also utilizes 
      GPU and can efficiently read in compressed TIFF frame images and decompress them, apply gain reference to normalize 
      image frames, deal with defects and align all the frames all at once. 
      
      To align small movie stack in-fly during tilting series data collection might be among motivations that David M developed 
      this. One can easily see how useful and nice it is that every tilt is aligned for small movie stacks and return to 
      SerialEM automatically in the background. It could save user huge mount of processing time unless you want to redo 
      the movie alignment again yourself. For this purpose, the same function and code of IMOD program are also included 
      into SerialEM program so that the alignment can be done during data collection. 
      
      I personally find this in-fly aligning capability extremely useful for single particle applications too. With 
      SerialEM setup properly, I can get aligned image for an exposure directly. This not only provides feedback 
      immediately, but also does it without changing low dose imaging conditions. I don't have to change back and forth the 
      low dose record exposure time, counting to Linear mode etc..
      
      *Framewatcher*, an IMOD python script program, makes it very easy to align all the movie frames in a changing directory.
      No need to bother with cron job and file lock etc.. It watches for any unprocessed image stack and align them for you. 

      In this document, I try to tell you how I use them. 

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
   
3. Use "clip" to apply gain reference and deal with defects all in a single command line (later IMOD can take tiff file format as input directly). 

.. code-block:: none

   $clip mult -n 16 -m 2 -D defects.txt fileWithFrames.tif GatanGainRef.mrc normalizedFrames.mrc
   
   
