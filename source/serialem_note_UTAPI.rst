.. _early_adaption_utapi:

SerialEM Note: Early Adaption of UTAPI 
======================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: Jan. 24, 2026
:Last_Updated: Jan. 24, 2026

.. glossary::

   Abstract
      Around mid-2025, Thermo Fisher began shipping their TEM platform
      software with the Universal TEM Application Interface (UTAPI). This is
      a new scripting interface built on its own framework, independent of 
      the earlier Standard Scripting and Advanced Scripting frameworks. 

      On both our Talos Arctica and Glacios systems, the UTAPI server is 
      included with platform server version 7.22. It was said the UTAPI will 
      be the only scripting interface eventually. 

      SerialEM immediately supported it. It currently supports all three
      scripting interfaces - Standard Scripting, Advanced Scripting and
      UTAPI. They can all run at the same time.

      As one of the early adopters, we have been experimenting with UTAPI
      using the latest version of SerialEM. In this note, I describe what is
      required to use UTAPI. 

.. _in_property_file:

In Property File
----------------

If your system comes with UTAPI, which you can check the existence of the
folder:

.. code-block:: ruby

  C:\Tecnai\Exe\UTAPI

you can start to use by adding some new property lines in your property
file.

In main section (before any camera properties), you can add there a few
lines

.. code-block:: ruby

  UseUtapiScripting     1      # 0 -> disable
  SkipAdvancedScripting 0
  SkipUtapiServices     0 2-19 21-32   # except Aperture(1) and camera (20)

In Falcon camera section, there are lines in the property:

.. code-block:: ruby

  Name            Falcon 4i
  DetectorName    0 EF-Falcon
  ## for UTAPI			
  UtapiName       Falcon 4i EnergyFilter
  FEICameraType   6
  ## For Advanced Scripting, using \\192......
  FalconLocalFramePath      Z:\        # \\192.168.10.81\OffloadData\TemScripting\EF-Falcon
  FalconRemoteFramePath     Z:\        # \\192.168.10.81\OffloadData\TemScripting\EF-Falcon       
  ## For UTAPI
  UtapiLocalFramePath       Z:\
  #FalconGainRefDir         \\192.168.10.81\OffloadData\ImagesForProcessing\EF-Falcon\200kV
  FalconGainRefDir          Z:\ImagesForProcessing\EF-Falcon\200kV

.. _whatif_problem:

What If There Is A Problem
--------------------------

It seems at this time, the UTAPI is not quite solid yet. We have bumped into
situation the beam tilt control and stigmator were not working. You can
simply turn off the specific services of UTAPI, and let Standard Scripting
and Advanced Scripting do the routine work. This is a mixed use case. You do
this by the line:

.. code-block:: ruby

  SkipUtapiServices	    0 2-19 21-32   # except Aperture(1) and camera (20)

In this case, only Aperture control and Falcon camera are using UTAPI
services, all the rest are not being used (skipped). The skipped ones are
still using Standard and/or Advanced Scripting. 

How do we know what number is referred to which service? David's code has
this portion to help. I listed them here:

.. code-block:: ruby

  ## index for Utapi support for skipping service (mainly for testing purpose) 
  ## https://raw.githubusercontent.com/mastcu/SerialEM/refs/heads/master/SerialEM.h
  #UTSUP_DEFLECTORS1 = 0, 
  #UTSUP_APERTURES, 1
  #UTSUP_NORMALIZE, 2
  #UTSUP_BEAM_STOP, 3
  #UTSUP_BLANKER, 4
  #UTSUP_COL_MODE, 5
  #UTSUP_FLUSCREEN, 6
  #UTSUP_FOCUS, 7
  #UTSUP_ILLUMINATION, 8
  #UTSUP_MAGNIFICATION, 9
  #UTSUP_PHASE_PLATE, 10
  #UTSUP_STIGMATOR, 11
  #UTSUP_FLASHING, 12
  #UTSUP_STAGE, 13
  #UTSUP_HIGH_TENSION, 14
  #UTSUP_COL_VALVES, 15
  #UTSUP_VACUUM, 16
  #UTSUP_VAC_CHAMBERS, 17
  #UTSUP_XLENS, 18
  #UTSUP_FEG, 19
  #UTSUP_CAM_SINGLE, 20
  #UTSUP_ACQUIS, 21
  #UTSUP_STEM_RASTER, 22
  #UTSUP_STEM_DYNAMIC, 23
  #UTSUP_CAM_CONTIN, 24
  #UTSUP_LOADER, 25
  #UTSUP_SAMPLE_TEMP, 26 
  #UTSUP_COLUMN_TEMP, 27
  #UTSUP_VIBRATION, 28
  #UTSUP_FILTER, 29 
  #UTSUP_TEMP_CONTROL, 30
  #UTSUP_CAM_INSERT, 31 
  #UTSUP_DEFL_ALIGN, 32
  #UTAPI_SUPPORT_END

I manually added the sequential numbers here for the index. 

You can also just not using the UTAPI at all, but you don't have to do that. 

.. code-block:: ruby

  UseUtapiScripting	    0

For more details, you should refer the helpfile. Currently, there are some
new features with UTAPI. For example, the Falcon 4(i) camera can also output
compressed TIFF LZW for raw frames, just like Gatan K2/K3 camera. This can
reduce the filesize quite a bit. However, this doesn't have any
super-resolution feature as compared to EER. It is more like option "Bin
counting by 2" for K3 camera. If you acquire images with some over-sampling
and you Fourier cropping back at processing step to reduce the aliasing
noise, that might be good enough in some cases. 
