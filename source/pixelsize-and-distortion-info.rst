.. _pixelsize_distortion:

Pixelsize and Distortion Info on K2 Cameras
===========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-2-23

.. _glossary:

  Abstract
    We have K2 on Talos Arctica and GIF/K2 on Krios. 
    
    This doc lists pixelsize information for K2 cameras so you can decide which magnification you 
    want to use for your final image. I also try to give information about image distortion on these cameras.

.. _talos:

On Talos Arctica
----------------

According to FEI document http://www.fei.co.jp/_documents/DS0189-10-2014_Talos_Arctica_WEB.pdf, the Cs value for Talos 
Arctica is 2.7mm.

Below are pixelsizes on K2 for a few magnifications.

**Table.1 Pixelsize (A) of K2 camera**

+--------------------------+-------------------+----------------------+
|  Magnifications (X)      | Counted           | Super resolution     |
+==========================+===================+======================+
|  11,000                  |   3.68            |   1.79               |
+--------------------------+-------------------+----------------------+
|  17,500                  |   2.24            |   1.12               |
+--------------------------+-------------------+----------------------+
|  22,000                  |   1.74            |   0.87               |
+--------------------------+-------------------+----------------------+
|  28,000                  |   1.37            |   0.69               |
+--------------------------+-------------------+----------------------+
|  36,000                  |   1.08            |   0.54               |
+--------------------------+-------------------+----------------------+
|  45,000                  |   0.85            |   0.42               |
+--------------------------+-------------------+----------------------+

Also the distortion information at these few mags. This mag distortion is believed due to strech on projection lens
system. The measurement and correction programs are used and available from http://grigoriefflab.janelia.org/magdistortion . 

**Table.2 Mag Distortion Parameters for K2 camera**

+--------------------------+-------------------+----------------------+-------------------+-----------------------+
| Magnifications (X)       | Distortion Angle  | Major Scale          | Minor Scale       |  Totat Distortion (%) |
+==========================+===================+======================+===================+=======================+
|  11,000                  |                   |                      |                   |                       | 
+--------------------------+-------------------+----------------------+-------------------+-----------------------+
|  17,500                  |                   |                      |                   |                       |
+--------------------------+-------------------+----------------------+-------------------+-----------------------+
|  22,000                  |   65.9            |   1.016              |  1.000            | 1.56                  |
+--------------------------+-------------------+----------------------+-------------------+-----------------------+
|  28,000                  |   24.5            |   1.015              |  1.000            | 1.51                  |
+--------------------------+-------------------+----------------------+-------------------+-----------------------+
|  36,000                  |   22.5            |   1.015              |  1.000            | 1.51                  |
+--------------------------+-------------------+----------------------+-------------------+-----------------------+
|  45,000                  |   21.3            |   1.016              |  1.000            | 1.56                  |
+--------------------------+-------------------+----------------------+-------------------+-----------------------+


