.. _monitor-data-collection-in-the-fly:

Monitor Data Collection In The Fly
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2018-08-02 
:Last-Updated: 2024-01-10

.. glossary::

   Abstract
      We already routinely align movie frames during data collection so we
      can check images from time to time. We did most directly using K2
      camera computers. This works very nicely. However, there are still a
      couple things we feel missing.  1) we need to see the defocus range
      and phase shift values computated and plotted out. 2) we need to do
      this with no delay and without slowing down the data collection
      itself. 
      
      We decided to align movies and perform CTF determiantion using a
      dedicated workstation with dual GPU. Our Summer Student, Albert Xu,
      made this completely automatic. During data collection, a plot is
      showing on web browser and refreshing itself.
      
      This document is mainly for oursleves as a check list. Hopefully, it
      can also be useful for your setup.  
      
      For Albert's *ctffindPlot* project, please see
      https://github.com/alberttxu/ctffindPlot .

.. _setup:

Setup for Shipping, alignframes and CTFfindPlot 
-----------------------------------------------

1. Ship raw data from K2 local SSD to storage tank. 

Assuming storge tank is CIFS mounted onto K2 computer, as W:, and we have a
new folder call ChenXu_20180802. We create a folder on local ssd drive X:
usually using the same folder name. We collect everying off camera onto this
local SSD folder X:\\ChenXu_20180802 first including all LMM, MMM maps etc.
and raw TIFF data as well. We use IMOD porgram ``framewatcher`` to ship the
raw data, pcm parameter files, defect file and gain reference file to
storage.

From cygwin shell terminal on K2 computer, go into local folder
X:\\ChenXu_20180802 and do this:
   
.. code-block:: ruby

   $ framewatcher -nocom -pr W:\ChenXu_20180802
   
From today - Sept 09, 2018 the latest package (currently in nightly builds)
supports multiple processed folders so the collected files can be shipped
into several folders. This is very useful to align them parallelly by
running alignframes from inside of each folder seprately. A example is
below:

.. code-block:: ruby

   $ framewatcher -nocom -pr W:\ChenXu_20180802\tmp1 -pr W:\ChenXu_20180802\tmp2 -pr W:\ChenXu_20180802\tmp3 -pr W:\ChenXu_20180802\tmp4
   
This will move all the raw files onto storage location, so local SSD never
fills.

2. ssh login GPU computer as you and su to "guest", make new folders and
align movies

.. code-block:: ruby

   $ ssh xuchen@gpu  
   [xuchen@gpu ~]$ su - guest
   [guest@gpu ~]$ cd /mnt/Titan/ChenXu_20180802
   [guest@gpu ChenXu_20180802]$ mkdir rawTIFF alignedMRC alignedJPG
   [guest@gpu ChenXu_20180802]$ framewatcher -gpu 0 -bin 2 -po 1024 -pr rawTIFF -thumb alignedJPG -dtotal 46.5
   
This will move raw data files (TIFF, dm4, defect, pcm) into *rawTIFF* and
_powpair.jpg into *alignedJPG*. You can also add an option "-o alignedMRC"
to move all the aligned MRC files into that folder *alignedMRC*.

As mentioned above, one can also run a few jobs of framewatcher from
multiple directories separately, so speed thing up, with or without a GPU
card. You can manually run the command from tmp1, tmp2, tmp3 and tmp4. You
can also ask a simple shell script to do that. The only disadvantage might
that you have to "kill" when you need to stop *framewatcher* because you
cannot do Ctrl_C from shell interactively in this case. 

.. code-block:: ruby

   #!/bin/bash

   for dir in tmp{1..4} ;
   do 
      cd $dir 
      framewatcher -bin 2 -po 1024 -o ../ -pr ../rawTIFF -thumb ../alignedJPG -thr 4 -dtotal 46.5 & 
      cd ..
   done

3. Copy and edit ctffind parameter file (as "guest", in the same folder; we
usually create a new terminal from tmux by "Ctrl_B C").

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

Laterly, testctfind of IMOD package can also do a very good job. You may
not get into hassle to compile ctffind on Windows by using IMOD installed.

You run a bash script from cygwin windows (assuming you have IMOD installed):

.. code-block:: ruby

# set parameters here
   volt=200
   sph=2.7
   dmin="5000"
   dmax="50000"
   dstep="400"
   minres=30
   maxres=5
   testctffind -v $volt -sph $sph -dstep $dstep -dmi $dmin -dma $dmax -rmi $minres -rma $maxres -pha (filename) > testctffind_output.txt


4. plot

.. code-block:: ruby

   [guest@gpu ChenXu_20180802]$ ctffindPlot
   
This will generate a plot and continuously update a file called
*ctf_plot.png* which can be loaded into a web browser and let it refresh
periodically. All the aligned MRC files will be moved into *alignedMRC* by
the plot program after done. 

For convenience, there are a few parameter files for common conditions which
you can directly use with option "-t". 

.. code-block:: ruby

   [guest@gpu ChenXu_20180802]$ ctffindPlot -t /usr/local/ctffindPlot/ctffind_params/Titan_130k_NoVPP.txt

.. _end_result:

End Results - CTF ploting and JPG of aligned image & power Spectrum  
-------------------------------------------------------------------

During the collection, the both CTF plotings and aligned image with power stectrum
can be viewed immediately. No delay. 

**Fig.1 CTF plot**

.. image:: ../images/ctffindplot_plot.png
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %
   :alt: CTF plot
   :align: left


**Fig.2 Aligned Image and its power spectrum**

.. image:: ../images/DG02-g1_00060_X+0Y+1-1_powpair.jpg
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %
   :alt: aligned image and its power spectrum
   :align: left
