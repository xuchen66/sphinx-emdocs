.. _SerialEM_note_cross-mag_alignment:

SerialEM Note: Cross-mag Alignment
==================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date Created: March 7, 2024
:Last Updated: May 6, 2024

.. glossary::

   Abstract
      Cross-mag alignment functions have been recently implemented since
      around beginning of 2024. They allow one to align two images at very
      different magnifications, and it takes into consideration of scale and
      rotation. 

      There are a few scripting commands available related to this
      function. The routine should work for more general purpose when different
      magnifications are involved. Here I give two application examples
      that were built upon this cross-mag alignment functions.  


.. _Set_LD_IS_automatically:

Set Image Shift Offset for LD View or Search Automatically
----------------------------------------------------------

The late Testing branch has a new look for Low Dose Control panel, as shown
below.

**Fig.1 New Low Dose Control Panel**

.. image:: ../images/new-LD-with-auto.png
   :scale: 50 %
..   :height: 544 px
..   :width: 384 px
   :alt: new LD looking
   :align: center

This new design re-arranges Offset portion. It seems to be more intuitive
for offsets of Image Shift and Defocus, for both View and Search areas.
Moreover, it has a ``Auto`` button for Image Shift offset now. 

This is how it works. If you take a Record or Preview image with some clear
feature in it, you click on ``Auto`` button. The program will transform this 
image to scale down to match magnification of View and align it to View
image for the same feature. This results in image shift offset be set
accurately. 

Similarly, we can do this easily for setting up IS offset for Search. 
If the mag of Search is lower than that of View, a View image will be 
reference instead of Preview. You take a View image first, and it 
would be good to contain some dirly-like feature. Then you press the 
``Auto`` button for Search. 

**Fig.2 Images for Preview, shrunk Preview and View**

.. image:: ../images/LD-shift-auto.png
   :scale: 35 %
..   :height: 544 px
..   :width: 384 px
   :alt: new LD looking
   :align: center

In above figure, the first image on the left - "A buffer" is from a Preview.
The image in the middle is a scaled down one of it based on the View mag. You can
see the FOV is small compared to a real View image which is on the right. By
aligning these two images at the same maginifications / pixelsizes of View, the
procedure determines IS offset for View and take it in. 

.. _Realign_a_feature_in_view_to_P:

Realign a Feature in View map to Match Preview/Record
-----------------------------------------------------

Realign routine now can take advantage of this cross-mag alignment functionality.
With this feature, Realign is able to bring the target to the center of
camera at higher mag (Previw) than original map (View). 

**Fig.3 Option for Realign to Scaled map**

.. image:: ../images/realign-to-scale-P-option.png
   :scale: 40 %
..   :height: 544 px
..   :width: 384 px
   :alt: new LD looking
   :align: center

This feature can be accessed from scripting command. If you run "Acquire at
Items", it can be activated from the option for Actions on the left column,
as shown in above figure. 

In this case, after a typical two rounds of aligning at View map, it them
scaled up the portion of the map to match final higher mag (Preview or P in
this case) and does the 3rd round aligning at Previw mag. 

**Fig.4 Scaled up Realign - ideal for initial target in TS**

.. image:: ../images/realign-to-P.png
   :scale: 35 %
..   :height: 544 px
..   :width: 384 px
   :alt: new LD looking
   :align: center

In above figure, the first image on the left has a feature centered after
normal Realign to Item, it is the scaled up this portion of the map to match
the higher target mag, as shown in the middle. The routine then aligns the real
Preview image (on the right) to this transformed one in the middle. So the
target is centered at Preview mag. This is ideal for initial targeting for
tilting series. 

As you can see, even your low dose image shift offset for View might have
not been adjusted perfectly, this procedure still can bring the final mag target to
the center. This is indeed very convenient! 
