 
.. index:: SerialEM Trainin
.. serialem_training:

SerialEM Training - Basic, Tomography, Single Particle, Advanced
================================================================
(Each session is set 2 - 2.5 hours)

:Author: Chen Xu 
:Contact: <chen.xu@umassmed.edu>
:Date: 2016-08-12

.. glossary:: 

 Goal 
    This is to provide hands-on training on SerialEM. I will teach basic functions of the program. And I will teach how to use the powerful program for electron tomography data collection, and for single particle application as well. 

 Requirement 
    You are required to have basic knowledge for TEM operation, preferred for Tecnai/Titan/Talos system. You should be able to operate scope independently to get a properly foused image. You are *not* required, however, to have pre-knowledge of SerialEM itself. 

This document lists the training contents that are covered in the hour catagories - basics, tomography, single particle and advanced topic.

.. note:: If you have any thought and suggetion to improvement the training, I love to hear them. 

.. _basic:

SerialEM - Basics (3 sessions)
------------------------------

.. rubric:: Session 1

- Introduction to SerialEM, launch and exit the program. Explain system files and user setting file.
- Explain how SerialEM controls microscope and camera, and its relationship with microscope and camera control software interface. 
- explain interface and layout - control panels and menus
- Camera setup, how to acquire an image from SerialEM interface
- Demo basic function such as Eucentricity (and shift beam to tilting axis)

.. rubric:: Session 2

- Refresh Tune-scope procedure
- Prepare gain reference file in SerialEM
- Explain image buffers and how to save buffer image into file, explain MRC stack and modes 
- Image Shift and Stage Shift
- Eucentricity, Autofocus and montaging
- Explain pixelsize and dose/dose rate.

.. rubric:: Session 3

- Introduce Navigator - navigater items: map, point and polygon  
- Demo full grid montage, Medium Map Montage (Using Image Shift and Stage Shift)
- Realign To Nav Item, demo and explain
- demo acquire map or image at multiple points ...
- introduce script/macro

.. _Tomography:

SerialEM - Tomography (5 sessions)
----------------------------------

.. rubric:: Session 1

- demo and explain how to collect a tilting series
- explain cooking resin specimen, defocus and other parameters (mag, binning etc.)
- explain proper sample preparation for platic sections (thickness, gold beads etc.)

.. rubric:: Session 2

- Supervise user to acquire MMM maps and collect a tilting series,  answer questions and comment on the condition used.
- Setup montage tilting series
- Setup batch mode for multiple locations

.. rubric:: Session 3

- demo and explain dual axis tomography data collection
- demo how to rotate grid 90 degree and find the same location (registration tramsformation)

.. rubric:: Session 4

- Low dose mode setup for Cryo Tomography applications
- refresh cryo sample and holder handeling

.. rubric:: Session 5
 
- LMM, MMM in low dose mode
- bi-directional tilting series collection
- batch mode for cryo data collection. 

.. _single-particle:

SerialEM - Single Particle (5 sessions)
---------------------------------------

.. rubric:: Session 1

- positioning X,Y, Image Shift and Stage Shift, backlash
- dragging to a new position, with Script/Macro
- positioning for preselected multiple location using ``RealignToItem`` and ``ZeroIS``.
- positioning Z, using stage and using tilted beam image pair
- demo and explain scripts ``Z_byG`` and ``Z_byV``
- demo center beam using keyboard and script

.. rubric:: Session 2

- demo simple script ``LD``, and explain actions
- refine hole centering using template
- draw grid point - normal and grouping
- introduce script ``LD-group`` and explain the ideas 

.. rubric:: Session 3

- K2 specific - image format(MRC, TIFF), Compression, 4-bit special for Super-res frames
- Asynchronize mode for K2 imaging, separate gain reference from raw image frame stack
- Consideration for dose - total dose, dose per frame, frame time 
- In-fly frame aligning option

.. rubric:: Session 4

- go through whole single particle procedure
- LMM, LD setup, MMM with "Z_byV", draw grid point, prepare hole template
- run ``LD-group``

.. rubric:: Session 5

- supervising user practise session to go through all the steps
- answer question 
- explain script command to limit defocus changing range

.. _advanced:

SerialEM - Advanced Topics (3 sessions)
---------------------------------------

.. rubric:: Session 1

- SerialEM installation and Calibration

.. rubric:: Session 2

- Setup multiple accounts
- Setup multiple system files
- Setup executables for production and tests
- Setup Dummy instance to pick target holes while main instance is busy collecting

.. rubric:: Session 3

- explain script to take multiple shots around a centered hole
- script to control LN2 refilling and obtain K2 hardware dark background
- Setup email alert system
- in-fly align frames using standalone GPU server computer
