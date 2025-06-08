.. _alternative_center_hole:

SerialEM Note: An Alternative Way to Center Hole
================================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: June 8, 2025
:Last_Updated: June 8, 2025

.. glossary::

   Abstract
      If current stage position is a little off from a hole center and you
      want to center to it, one common way is to use a hole template to align
      to. 

      However, there is an alternative way for this. SerialEM hole finding 
      routine is powerful and robust. One can simply detect hole positions
      and center to the closest one, without using "AutoAlign". This is done
      conveniently with a script command. 

      In this note, I share how I use the function with two examples. 

.. _two_examples:

Two Examples
------------

**Fig.1 Center Hole Before and After**

.. image:: ../images/centerhole-before-after.png
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %

The image on the left is a typical LD_View image on 2250X, uP mode, on Krios.
The center of the hole with green marker is where I want, automatically. 

In this case, I can simple run a script command as below:

.. code-block:: ruby
  FindAndCenterOneHole 0 1.3 0 2

It results in the hole being centered, as shown in right. Marker is for
indication here, the procedure doesn't use it. The procedure will center the
closest hole using Image Shift. 

The command uses Hole Finder function. The prerequisite is that you already
have run the hole finder from the dialog on this grid, In this command, 1.3 is
hole diameter; all the other parameters are taken from the dialog windows
internally. This does work if the area only has a single hole, but doesn't
need to be. For an area with a lot of holes, it runs slightly slower. For a
single hole, it only takes roughly 0.1 seconds. 

As you can see, this method can be used to center a hole as an alternative
method to hole template matching. In a real data collection or screening,
you can use two lines to precisely go to each of many holes.

.. code-block:: ruby
  RealignToNavItem 0 
  FindAndCenterOneHole 0 1.3 0 2

The command directly uses the last image from Realign routine; there is
no need to take another LD_V shot. This seems working magically, if you
don't check both "include" and "exclude" to hide the found locations of
holes.

The method can also be applied to "StepTo & Adjust" dialog to refine the
IS vectors for multiple exposure pattern. See the image below:

**Fig.2 Centering Hole to refine IS vectors **

.. image:: ../images/stepto-center-hole-method.png
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %

As you can see, I used 15kX as middle mag to refine the IS vectors. At this
mag, a single hole is included in the view. With Automatic adjustment turn
on, it will go to each of the corner holes, center it and set the Image
Shift value. My experience is that the determination at this mag is close
enough for final LD_R mag at like 130kX etc., but you easily refine it with
edge of hole as landmark feature after you run auto-adjustment as lower mag. 

As of June 8, 2025, this command **FindAndCenterOneHole** is in 4.2 and 4.3 
testing with bug fixes. It was added on initially in 2024.   
