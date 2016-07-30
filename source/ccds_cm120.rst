.. _ccd_cm120:

CCD Cameras on CM120
====================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2016-7-30

.. _glossary:

  Abostract
    We no longer use film for TEM exposure anymore! Instead, we use digital camera. There are basically two types of digital 
    cameras available on the market for TEM applications - Charge-Coupled Device (CCD) and Direct Electron Detector. On CM120, 
    there are two cameras installed - Gatan Orius model 832 and Tietz TemCam 224-HD. They both are CCD type of cameras. In this 
    document, I give information about basic properties of the cameras, how to use them with CM120 and specific technical note 
    for why we need use them in such way.

  It also lists pixelsize information for both cameras so you can decide which magnification you want to use for your final image. 
  I also try to show you how to use SerialEM to control them.

.. _property:

Some Basic Properties of the Two Cameras
----------------------------------------

Here are images for the two cameras on the CM120.

**Fig.1 Camera Heads of Gatan Orius & Tietz 224HD**

.. image:: ../images/orius-224hd.png
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %
   :alt: Gatan Orius & Tietz 224HD Cameras
   :align: left

The table below lists some basic properties of these two cameras. 

**Table.1 Some basic properties**

+--------------------------+-------------------+----------------------+
|  Property                | `Gatan Orius 832`_   | Tietz TemCam 224-HD  |
+==========================+===================+======================+
|  Format                  |   3768 x 2672     |   2048 x 2048        |
+--------------------------+-------------------+----------------------+
| Physical PixelSize (μm)  |   9.0             |   24.0               |
+--------------------------+-------------------+----------------------+
| Digitization (bit)       |   14              |   16                 |
+--------------------------+-------------------+----------------------+
| Light Coupling Mechanism |  Fiber Optic      |   Fiber Optic        |
+--------------------------+-------------------+----------------------+
| Mounting Position        |   sided-mount     |   bottom-mount       |
+--------------------------+-------------------+----------------------+

