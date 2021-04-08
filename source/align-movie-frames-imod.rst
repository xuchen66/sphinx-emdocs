.. _align-k2-frames-using-imod:

Align Movie Frames with SerialEM and IMOD Programs
==================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date Created: Dec 17, 2017
:Last Updated: Dec 17, 2017

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

   $alignframes -StandardInput
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
   
.. _framewatcher:

Framewatcher 
------------

*framewatcher* is a python script to run *alignframes* at batch process. One feature I like a lot is that it can watch 
a growing directory and process new coming frame files. For details usage, please refer man page http://bio3d.colorado.edu/imod/betaDoc/man/framewatcher.html.

If frame stack files are with their command file *.pcm, then one can just run it by issuing command in the directory:

.. code-block:: none 

   $framewatcher
   
This will start to align all the frame files in the same direcotry, until you do Ctrl_C. 

If there is no *.pcm existed for each file, and you just want to align them using the same parameters, then you can do that
using a master pcm file to take care all the files you wanted to align. Here is an example of master.pcm:

.. code-block:: none

   $alignframes -StandardInput
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
   InputFile 
   OutputImageFile 
   ScalingOfSum 37.549999
   CameraDefectFile defects_YURI_B1_G1-SuperRes_358_Feb01_07.52.46.txt
   GainReferenceFile SuperRef_YURI_B1_G1-SuperRes_001_Jan31_15.48.35.dm4
   RotationAndFlip -1
   DebugOutput 10

As you can see, this is the same as individual pcm file, except without InputFile and OutputImageFile defined in the 
command file. In this case, you tell the program to use this master.pcm file:

.. code-block:: none 

   $framewatcher -m master.pcm
   
The program will go through all the individual files and generate their individual pcm file based on master.pcm and 
align each one. 

Since *framewatcher* can flexibly define output location, we can utlize it to save all the raw files and as well as aligned result files into a network drive from local SSD drive. Sometimes, directly saving on network drive and also aligning frames there could cause slowdown of SerialEM data collection. This works as a neat way to empty X or Y drive on K2 computer, they will never fill. For example, following command will move all the new files saved by SerialEM and aligned files on X drive to the network drive Z. 

.. code-block:: none 

   $framewatcher -w X:\MyData -o Z:\Storage\MyData -pr Z:\Storage\MyData
   
*framewatcher* can also output aligned sum together with power spectrum into a single image in JPEG format. This is ideal to send to remote user who wants to check image quality during data collection session. The file is small and can be opened with any image viewer. 

.. code-block:: none 

   $framewatcher -w X:\MyData -po 1024 -o Z:\Storage\MyData -pr Z:\Storage\MyData

You can even simply move all the raw files without aligning them. 

.. code-block:: none 

   $framewatcher -w X:\MyData -noc -pr Z:\Storage\MyData
   
Interestingly, *framewatcher* will also copy (not move) Gatan gain reference file and Defect file to Z drive too. 

If you also want pcm file to move together with raw file, you can use "-after" option:

.. code-block:: none 

   $framewatcher -w X:\MyData -noc -pr Z:\Storage\MyData -after 'mv %{rootName}.pcm %{processedDir}'

You can even do ctffind and plot the curve using the "-after" option, if you installed Albert's *ctffindPlot* program. The command is like this:

.. code-block:: none 
   
   framewatcher -gpu 0 -bin 2 -po 1024 -dtotal 46.6 -after 'ctffindPlot %{outputFile}'

From November 23, *alignframes* and *framewatcher* also have options to do dose weighting. This is still in alpha version, but perhaps will be IMOD main production soon. Here I demo a couple of options to use with *framewatcher*:

.. code-block:: none 

   $framewatcher -w X:\MyData -po 1024 -dtotal 39.8 -Vt 200 -o Z:\Storage\MyData -pr Z:\Storage\MyData

where the total dose on sample is 39.8 electrons/A\ :sup:`2`, accelerating voltage is 200kV. 

If on the storage tank, we have a few subfolders to make things more organized, and we use K2 computer to align, we cando someting like this:

.. note::

   Very often, people get confused by the terms "dose" and "dose rate", partially because there seems to have no *official* definition here. 
   As per my understanding, "dose" means electron dose on specimen and usually has unit electron/A\ :sup:`2`, while "dose rate" means beam intensity level for detector and usually has unit electron/unbinned pixel/second. Dose rate is a reference value for the performance of a detector. In the case of K2 Summit counting or super-resolution mode, this value is usually choosen between 5 - 10. Much higher than 10, the performance of K2 camera is likely to be worse. Once this value is fixed under current microscope conditions, we select exposure time and frame time etc. to satisfy the total dose on the sample and frame dose (also on sample) within the frame time for movie alignment purpose. 

.. _using_GPU:

Using GPU 
---------

To my understanding, the code for *alignframes* is optimized to utlize GPU and paralellization as well. Reading in and decompressing TIFF stack file is also very efficient. On my linux box with Xeon(R) CPU E5-2650 v3, with 256GB memory and 
Nvidia M4000 GPU, it aligns a 50 Super-resolution frame file in about 22 seconds with GPU option. 

.. _on_k2:

On K2 Computer
--------------

Since K2 computer comes with pretty high-end hardware, it could be used to align the frames in background. All I had to do 
is to install a decent GPU card. I replaced the ATI video card that comes with the K2 box and install a M4000 GPU card in with 8GB 
memory on the card. One advantage for this card is that it is single slot high, not like most Nvidia cards which occupies 
two PCI slot space. This makes the replacement simple and easy. 

Now, after installing IMOD with Cygwin, I align all the movie frames right off the K2 computer box. 

.. _with_SerialEM:

Align using SerialEM directly
-----------------------------

Beside aligning frames at the background separately with IMOD, we can also use SerialEM plugin to align the frames directly. From camera setup page of SerialEM interface, you can define to let SerialEM Plugin to align the frames. Slightly different from using IMOD which aligns as separate process, SerialEM Plugin aligns all the frames from an exposure and returns the aligned average to SerialEM main instance. This is very handy for us to obtain sample information quickly and conveniently. 




