.. _alternative_center_hole:

SerialEM Note: More on MMM Centering
====================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: March 2, 2026
:Last_Updated: March 2, 2026

.. glossary::

   Abstract
      Now Realign in LM ranges works better due to two recent improvements
      is SerialEM. 1) The property "RealignItemMaxLMField" is taken care of 
      automatically for LM range, and 2) The Aperture sizes for both
      condenser 2 and objective lens are not property for Map item in
      navigator. Thus, realign routine becomes a more convenient way for
      going to a mesh accurately.

      However, there is another source of MMM centering problem I did not
      realize before - the lateral shift! If your mesh Z height changed a
      lot from initial Z of LMM map, your MMM will be off centered due to
      this lateral shift. If the change is 100 micron or more, this is to be
      taken care. This is normal for meshes having quite different Z heights
      in a grid. 

      This issue can be simply addressed by another round of Realign. 

.. _script_after_Z_change:

The Script to Run after Z change
--------------------------------

One can use Realign just stage shift to go to a mesh initially. Then before
MMM map is acquired, the Z height has to get right before making MMM maps. 
We normally use "Eucentricity by Focus" to fix Z height for a mesh. After
that, we can run Realign again to touch up the XY of the mesh before MMM
montaging mapping. The key here is to skip Z change so it doesn't go back 
to initial Z height of LMM grid map. 

.. code-block:: ruby
  ScriptName MMM_Centering

  ReportStageXYZ
  Z0 = $repVal3

  RealignToNavItem 0 
  Eucentricity -1

  ReportStageXYZ
  Z1 = $repVal3

  If ABS ( $Z1 - $Z0 ) >= 70
     SkipZMoveNextNavRealign
     RealignToNavItem 0
  Endif
