
.. _FastTomo_a_hybreid_approach:

FastTomo: A Hybrid Approach to Speedup Tomography Data Collection
=================================================================

:Author: Albert Xu
:Contact: <albert.t.xu@gmail.com>
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2020-05-24 
:Last-Updated: 2020-05-24

.. glossary::

   Abstract
      Due to the imperfection of stage movement, a tomography target positon (X, Y, Z) usually keeps changing with tilting angle. 
      In order to obtain useful data, some kind of engineering control has to be in place to ensure the target postions are within 
      the acceptable range through the entire tilting series. This engineering control might involve complete prediction or/and 
      frequent tracking for X, Y and Z postions. Unfortunitely, these extra control actions cost time for each tilting series collection. 
      Here, we propose a hybrid method to make the data collection as fast as possible without sacrificing too much data quality. 
      
      We use a precalibration for each of every tilting series for Z, and we use proportional control to constantly compensate 
      X, Y potions. We try to eliminate tracking and auto-focusing shots as much as possible. 
      
      We present a SerialEM script using this Hybrid method for all three tilting schemes: uni-directional, bi-directional and
      dose-symmetirc. We slightly modify the oringal Hagen scheme to dose-symmetric with options to 1) switch to bi-directional
      beyond certain defined angle and 2) change exposure time at high angle ranges after switching to bi-directional. 
      
.. _background:

Some Brief background Information 
---------------------------------

Our tests on stage tilting behavious mainly show two things. 1) Statistically, it is hard to get specimen height to desirable range
of eucentricity. There seems to be always some error to get to eucentric height, large or small. 2) the relationshiop between
tilt angle and X,Y,Z position is not quite linear when the specimen is not close enough to eucentric height; even at very 
close to eucentric position it is no longer linear at high tilt range such as above +/- 45 degree.

Most of time consuming actions are the multiple tracking shots and auto-focusing precedures. 

This makes us to think we might be able to save time if we can have a way to eliminate most of, if not completely, the Focus and 
Tracking Shots, we can save time for tilting series data collection. 

.. _fasttomo:

FastTomo Script
---------------

For Z, we found that a **Sine** function can describle the Z change fairly well. We only need a few tilt points to pre-calibrate such
a **Sine** curve for each target postion. Such pre-calibration does cause some time, but the prediction based on the calibrated
 **Since** curve seems to be very robust and we then completely eliminate Focus shots in the collection step. The Objective 
 lens is used to compensate. 
 
For X,Y, we do not perform any precalibration. Instead, we use the returned Record images to perform the porportional control.
Namely, we align the feature from Record image at one tilt to its previous one and we use Image Shift to compensate. We completely 
eliminate Tracking T shots in the procedure except in dose-symmetric scheme which T is used only in switching angles. 

The SerialEM script - FastTomo and its usage can be found from the `github.com project page
<https://github.com/alberttxu/FastTomo/>`_.

.. _discussion:

A Few Points to Discuss
-----------------------

1. In roder to get a reliable correlation using R images, the dose is probably small. Therefore, a proper binning for R 
image is expected. This is not a problem for K2/K3 camera, as returned image can be binned while the raw frames are saved
on the side without any binning. For a non-K2/K3 camera, one can modify the script easily to `ReduceImage` for R shot after
saving and store that in the reference buffer. 

2. The same idea and method can be also applied to a FISE collection, provided the frames from each tilt can be grabbed and available 
for scripting. 

3. A single, long exposure with shutter opening and closing during tilting series saves time for startup delay and returning time, 
compared to conventional way of multiple exposure one after another. But the significant time saving is from 
minimizing the numbers of F and T shots aleady! Using our script **FastTomo**, the total time for bi-directional tilting series
from -51 to + 51 degree with 3 degee step is about 6 minutes including pre-calibration for Z prediction curve.  


