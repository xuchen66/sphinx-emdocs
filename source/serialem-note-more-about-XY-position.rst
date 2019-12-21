
.. _SerialEM_note_more_about_XY_positioning:

SerialEM Note: More About X,Y Positioning
=========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2019-12-12 
:Last-Updated: 2019-12-21

.. glossary::

   Abstract
      Robust positioning to the target position is critical for high level operation of CryoEM data collection. In this note, 
      I like to share my own version of the latest script to perform X,Y positioning task. And I try to explain every line 
      of the code and ideas behind them as well. 
      
      I have spent a lot of time thinking and testing about this. If you have better and different ideas, I love to hear. 
      
      
.. _script:

The script lines 
----------------

The script is fairly short, as shown below. It can be inserted in beginning of a single particle data collection script. 

.. code-block:: ruby

    buffer = T
    RealignToNavItem 0
    ResetImageShift 2
    #Copy A $buffer
    AcquireToMatchBuffer $buffer
    AlignTo $buffer 0 1

.. _explain:

Explanations Line by Line
-------------------------

.. code-block:: ruby

    buffer = T
    
This is to define which buffer will be used to store reference image, a whole image or a cropped area of an image. 
Buffer after N are all beyond rolling range, thus won't be pushed out by taking too many images. 

.. code-block:: ruby

    RealignToNavItem 0

``RealignToNavItem`` is one of the most important functions in **SerialEM**, in my opinion. It will bring the specimen stage to 
a valid map item. It typically uses combination of stage shift and image shift to get the job done. ``0`` here means to stays 
in the conditions from which the map was created. For example, the map was generated using LD **View**, and the scope currently is at LD **R**,the scope will switch to the View mag, beam intensity etc.. After *realign* is done, it stays in View mag. Argument ``1`` will bring scope back to **R**, after routine finishes. 

This command line will bring the specimen to the picked item position, with some image shift in the last image of the routine takes, in buffer A. 

I should point out that this perhaps reflects one of the most fundamental differences between SerialEM and other data collection
software - it doesn't rely on the template at all. As long as an item in a valid map is defined (picked), **SerialEM** will drive the stage there!

.. code-block:: ruby

    ResetImageShift 2

``ResetImageShift`` is to clear out any image shift existing in the system and use stage shift to compensate. Then, there is no image shift, which means beam is straight down on the axis. However, the intrinsic inaccuracy of stage movement makes 
target being slightly off, more or less.  

The argument ``2`` here means stage will clear the backlash by moving to opposite direction for 0.025 microns as default. This can be very useful to slow down the stage drifting after moving to a new location. Low drift is a very good thing since there is no way to correct drifts accumulated within a frame. This is particularly true if one has to use long frame time on some camera system. 

.. code-block:: ruby

  #Copy A $buffer
  
If not commented out, this line will copy the last image (after realign) in buffer A to a target buffer (T in this case). If 
one uses a fix image, for example, a cropped hole as reference image, then it should be manually copy into T and leave this 
line commented out. 

.. code-block:: ruby

    AcquireToMatchBuffer $buffer    

This is a new command, available in 3.8 beta Dec 10th, 2019 built and later. It does two things: 1) take a shot using the 
exact condition of what in the reference buffer for mag, beam condition, binning, exposure time etc.; 2) make the final image the same size as what in the reference buffer, by cropping if necessary. I used to have to do this in a lenthy script using two functions. 

.. code-block:: ruby

   AlignTo $buffer 0 1
   
Simply align the image in buffer A to reference buffer. This would make the target right on again with image shift. The very 
last argument ``1`` means no trimming to any of the source image and reference image. This is needed for UltrAuFoil® Holey Gold Films grid which have vary "dark" region of the film. 

.. thoughts:

Other thoughts
--------------

1. It is helpful to use large defocus offset for map and realigning, as the contrast is significantly better. On our Krios, we use -300um for View offset (in LD). 

2. If offset is more than 200um, it most likely needs High-def Mag calibration. With this, system dynamically interpolates the stage shift matrix which is calibrated using near-focus condition. This makes stage movement much more accurate and robust.

3. If possible, use whole image as template instead of sub-area. Using sub-area sush as a single hole is a quick workaround for a grid which has periodic feature and 5-point way of picking points might be not very accurate due to local geometry variation. 
