.. _align-k2-frames-using-imod:

Align Movie Frames with SerialEM and IMOD Programs
==================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-03-17

.. glossary::

   Abstract
      IMOD can align movie frames nicely and very quickly. In late versions of IMOD, the program *AlignFrames* also utilizes 
      GPU and can efficiently read in compressed TIFF frame images and decompress them, apply gain reference to normalize 
      image frames, deal with defects and align all the frames - all at once. 
      
      To align small movie stack in-fly during tilting series data collection might be among motivations that David M
      developed 
      this. One can easily see how useful and nice it is that every tilt is aligned for small movie stacks and return to 
      SerialEM automatically in the background. It could save user huge mount of processing time unless you want to redo 
      the movie alignment again yourself. For this purpose, the same function and code of IMOD program are also included 
      into SerialEM program so that the alignment can be done during data collection. 
      
      I personally find this in-fly aligning capability extremely useful for single particle applications too. With 
      SerialEM setup properly, I can get aligned image for an exposure directly. This not only provides feedback 
      immediately, but also does it without changing low dose imaging conditions. I don't have to change back and forth the 
      low dose record exposure time, counting to Linear mode etc..
      
      *Framewatcher*, an IMOD python script program, makes it very easy to align all the movie frames in a changing directory.
      No need to bother with cron job and file lock etc.. It watches for any unprocessed image stack in the directory 
      and align them for you. 

      In this document, I try to tell you how I use them. 

.. _alignframes:

Alignframes 
-----------

This program takes many options as command line arguments. For details, please read the man page with example usages http://bio3d.colorado.edu/imod/betaDoc/man/alignframes.html. 

As usual, the long command line can be run with a command file. Here is an example of python command file YURI_B1_G1-SuperRes_2967_Feb04_01.14.57.pcm. 

.. code-block:: none

   $time alignframes -StandardInput
   UseGPU 1
   StartingEndingFrames 3 42
   MemoryLimitGB 20.0
   PairwiseFrames 20
   GroupSize 1
   AlignAndSumBinning 6 1
   AntialiasFilter 4
   RefineAlignment 2
   StopIterationsAtShift 0.100000
   ShiftLimit 20
   MinForSplineSmoothing 0
   FilterRadius2 0.060000
   FilterSigma2 0.008574
   VaryFilter 0.060000
   ModeToOutput 2
   InputFile YURI_B1_G1-SuperRes_2967_Feb04_01.14.57.tif
   OutputImageFile YURI_B1_G1-SuperRes_2967_Feb04_01.14.57_ali.mrc
   ScalingOfSum 37.549999
   CameraDefectFile defects_YURI_B1_G1-SuperRes_358_Feb01_07.52.46.txt
   GainReferenceFile SuperRef_YURI_B1_G1-SuperRes_001_Jan31_15.48.35.dm4
   RotationAndFlip -1
   DebugOutput 10

One can run this command file like this:

.. code-block:: none 
   $subm YURI_B1_G1-SuperRes_2967_Feb04_01.14.57.pcm
   
