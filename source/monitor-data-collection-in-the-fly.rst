.. _monitor-data-collection-in-the-fly:

Monitor Data Collection In The Fly
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2018-08-02 
:last-Updated: 2018-08-02

.. glossary::

   Abstract
      We already routinely align movie frames using data collection so we can check images from time to time. We did most 
      directly using K2 camera computers. This works very nicely. However, there are still a couple things we feel missing. 
      1) we need to see the defocus range and phase shift values computated and plotted out. 2) we need to do this with no 
      delay and without slowing down the data collection itself. 
      
      We decided to align movies and perform CTF determiantion using a dedicated workstation with dual GPU. Our Summer Student,
      Albert Xu, made this completely automatic. During data collection, a plot is showing on web browser and refreshing itself.
      
      This document is mainly for oursleves as a check list. Hopefully, it can also be useful for your setup.  
      

.. _alignframes:

Alignframes 
-----------

This program takes many options as command line arguments. For details, please read the man page with example usages http://bio3d.colorado.edu/imod/betaDoc/man/alignframes.html. 

As usual, the long command line can be run with a command file. Here is an example of python command file YURI_B1_G1-SuperRes_2967_Feb04_01.14.57.pcm. 
