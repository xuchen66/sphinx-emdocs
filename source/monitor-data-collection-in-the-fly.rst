.. _monitor-data-collection-in-the-fly:

Monitor Data Collection In The Fly
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2018-08-02 
:last-Updated: 2018-08-02

.. glossary::

   Abstract
      We already routinely align movie frames during data collection so we can check images from time to time. We did most 
      directly using K2 camera computers. This works very nicely. However, there are still a couple things we feel missing. 
      1) we need to see the defocus range and phase shift values computated and plotted out. 2) we need to do this with no 
      delay and without slowing down the data collection itself. 
      
      We decided to align movies and perform CTF determiantion using a dedicated workstation with dual GPU. Our Summer Student,
      Albert Xu, made this completely automatic. During data collection, a plot is showing on web browser and refreshing itself.
      
      This document is mainly for oursleves as a check list. Hopefully, it can also be useful for your setup.  
      

.. _setup:

Setup for Shipping, alignframes and CTFfindPlot 
-----------------------------------------------

1. Ship raw data from K2 local SSD to storage tank. 

Assuming storge tank is CIFS mounted onto K2 computer, as W:, and we have a new folder call ChenXu_20180802. We create a folder on local ssd drive X: usually using the same folder name. We collect everying off camera onto this local SSD folder X:\\ChenXu_20180802 first including all LMM, MMM maps etc. and raw TIFF data as well. We use IMOD porgram ``framewatcher`` to ship the raw data, pcm parameter files, defect file and gain reference file to storage.

From cygwin shell terminal on K2 computer, go into local folder X:\\ChenXu_20180802 and do this:
   
.. code-block:: ruby

   $ framewatcher -nocom -pr W:\ChenXu_20180802
   
This will move all the raw files onto storage location, so local SSD never fills.

2. ssh login GPU computer as you and su to "guest", make new folders and align movies

.. code-block:: ruby

   $ ssh xuchen@gpu  
   [xuchen@gpu ~]$ su - guest
   [guest@gpu ~]$ cd /mnt/Titan/ChenXu_20180802
   [guest@gpu ChenXu_20180802]$ mkdir rawTIFF alignedMRC alignedJPG pcmLogs
   [guest@gpu ChenXu_20180802]$ framewatcher -gpu 0 -bin 2 -po 1024 -dtotal 46.5 -pr rawTIFF -after 'mv %{rootName}_powpair.jpg alignedJPG; mv %{rootName}.log pcmLogs'
   
This will move raw data files (TIFF, dm4, defect, pcm) into *rawTIFF*, and powerpair JPG files into *alignedJPG*.

3. Copy and edit ctffind parameter file (as "guest", in the same folder; we usually create a new terminal from tmux by "Ctrl_B C").

.. code-block:: ruby

   [guest@gpu ChenXu_20180802]$ cp /usr/local/ctffindplot/test/ctffindoptions.txt .
   [guest@gpu ChenXu_20180802]$ vim ctffindoptions.txt
   
edit to fit your situation. The file looks like this:

.. code-block:: ruby

   ctffind << EOF
   (filename)
   (basename)_ali_output.mrc
   1.059
   300.0
   2.70
   0.07
   512
   30.0
   5.0
   5000.0
   50000.0
   100.0
   no
   no
   no
   yes
   0.0
   3.15
   0.5
   no
   EOF

4. plot

.. code-block:: ruby

   [guest@gpu ChenXu_20180802]$ ctffindPlot -p alignedMRC 
   
This will generate a plot and continously update a file called *plot.png* which can be loaded into a web browser and let it refresh periodically. All the aligned MRC files will be moved into *alignedMRC* by the plot porgram after done. 


   

   
   
   
