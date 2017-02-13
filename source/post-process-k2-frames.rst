.. _post-process-k2-frames:

Post Processing K2 Frames from SerialEM Data Collection
=======================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date: 2017-02-13

.. glossary::

   Abstract
      At UMASS Cryo-EM Facility, we use SerialEM to collect data for both single particle and tomography applications. 
      And we do that on both Talos Arctica and Titan Krios with K2 cameras. 
      
      For single particle, 
      usually we save frames in compressed TIFF format without gain normalized (select Dark Substracted in camera 
      setup window). One of the advantages of doing this is to reduce data size. For Super-resolution frames, the 
      raw frame data is in unsigned 4-bit. Pixel values are in the range of 0 - 15. For weak beam, there are a lot of 
      zeros there too. With lossless compression methods, such data can be comprssed into much smalller filesize without losing 
      image information. Therefore, instead of applying gain normalized reference to all the frames, we leave the raw 
      data compressed and saved to the disk and we later do post-processing to recover the full information of the image data. 
      
      In this doc, the procedure to do post processing is presented. 

.. note::
      This doc is a working progress. If you have comment and suggestion, please let me know. Thank you!

.. _scope_tuning:

Check Scope Condition and Perform Tuning 
----------------------------------------

Before you commit large dataset time, it is always a good idea to check scope condition to make sure everything is good. Calm down and be patient! Here are a few things I usually check. 

- Check Gun Lens, Extracting Voltage, High Tension are set at correct values.
- Stare for a few seconds at the focused beam at the highest SA mag, to see if the beam has good shape and there is no shaking or jumping.  
- From Direct Alignment, do gun tilt, beam tilt PP, Coma-Free alignment if needed. 
- Check Thon Ring at roughly the same condition (mag, dose) as your image condition. Make sure there is no obvious frequency cutoff, and Thon Ring reaches the resolution as in good condition. 
