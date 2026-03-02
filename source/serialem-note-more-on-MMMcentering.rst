.. _alternative_center_hole:

SerialEM Note: More on MMM Centering
====================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: March 2, 2026
:Last_Updated: March 2, 2026

.. glossary::

   Abstract
      Now Realign in LM range works better due to two recent improvements
      is SerialEM. 1) The property "RealignItemMaxLMField" is taken care of 
      automatically for LM range, and 2) The aperture sizes for both
      condenser and objective lenses are now properties for the Map item in
      navigator. Thus, realign routine becomes a more convenient way for
      positioning a mesh accurately.

      However, there is another source of MMM's off-centered problem I did not
      realize much before - the lateral shift! If your mesh Z height changed a
      lot from initial Z of LMM map, your MMM will be off centered due to
      this lateral shift. If the Z change is 100 micron or more, the lateral
      shift becomes fairly noticeable. This needs to be taken care of for accurate
      centering for MMM mapping. It is not uncommon for meshes to have quite different 
      Z heights acrossing a grid. 

      This issue can be simply addressed by another round of Realign. 

.. _script_after_Z_change:

The Script to Run after Z change
--------------------------------

One can use Realign or just use stage shift only to move to a mesh initially. 
Before acquiring the MMM map, the Z height must be properly adjusted. We 
normally use “Eucentricity by Focus” to correct the Z height for the selected mesh.

After that, Realign can be run again to fine-tune the XY position of the mesh, 
correcting the XY offset caused by the Z-height adjustment before starting 
MMM montaging. The key point is to skip any Z adjustment during this second 
realignment so that the stage does not revert to the original Z height of 
the LMM grid map.

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
