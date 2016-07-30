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
|  Property                | Gatan Orius 832   | Tietz TemCam 224-HD  |
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

.. _shutter-control

Shutter Control - Background Inforamtion
----------------------------------------

Shutter control usually means TEM shutter control by hardware or software of digital camera. The purpose, in most cases, is to have no beam on camera except exposure period. Not all camera requires having TEM shutter control to get an image. For cryoEM applications where accurate dose is to be concerned, it makes a lot of sense to have shutter control. Fortunately, most of high-end digital cameras for TEM have TEM shutter control support.

The shutter discussed here is not any kind of mechanical shutter. Instead, it is electronic. If an alignment coil in TEM column is put to a "bad" value, then the beam disappears due to misalignment. We call it shutter closed. And we call it open when the good alignment coil current is restored. Therefore, this kind of electronic shutter can open and close very quickly.

An exposure on film requires no beam before film is pushed in to final location and stabilized; and it opens shutter to let beam shine on film to expose and then shutter is closed again when exposure finishes. On FEI microscope with Low Dose functionality, one can even define pre-expose time. In this case, two shutters are required to work together - first shutter above specimen opens to have beam pre-expose on sample for certain period of time, then second shutter below specimen opens to start exposure on film. Usually, the two alignment coils to be controlled as upper and lower shutters are Gun Upper coil and a Film Shutter Coil next to projector lens. All this is done within FEI software control for when and which shutter is open or close. We usually call this internal shutter control.

To image with a digital camera, especially for a beam sensitive specimen, shutter control is therefore also needed. If configured correctly, when camera is inserted and large screen of TEM is lifted, the beam should be blanked. This shutter control is achieved by changing the same alignment coils using shutter cable(s) from camera controller. The shutter cable directly connects to TEM hardware so that when large screen is lifted, an extra voltage is sent to TEM coil to "screw up" a good alignment. Thus, beam is blanked. If we need to pre-expose our sample for whatever reason, then two cables will be needed - one for pre-specimen and one for post-specimen. This control is done by using external hardware. We normally call this "external" shutter control as it is not via TEM software.

For convenience, we usually call the shutter of Gun Upper Coil "the beam blanker"; and Film Shutter Coil "the film shutter".

