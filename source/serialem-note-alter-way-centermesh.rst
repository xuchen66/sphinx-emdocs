.. _alternative_center_hole:

SerialEM Note: An Alternative Way to Center a Mesh in LM
========================================================
  
:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: Dec. 21, 2025
:Last_Updated: Nov. 21, 2025

.. glossary::

   Abstract
      When we make MMM maps, sometimes, they are not centered very well.
      If you have ever wondered if this is due to mesh position in LM mag not being
      centered well, for example, due to stage error, then you want to make sure you 
      fix that first. Of course, you could use the most powerful Realign routine to 
      do that. In order to do that, most likely you will have to change property 
      "RealignItemMaxLMField" to a larger value than default, at least temporarily.
      You perhaps also need to change C2 aperture back to larger size which was used for 
      initial LMM mapping. 

      Is there a different way? The answer is YES. In this note, I show how to do that
      by taking advantage of robust AutoContour functionality. 

.. _center_mesh:

Center a Mesh
-------------

**Fig.1 Center A Mesh Before and After**

.. image:: ../images/CenterMesh-before-n-after.png
..   :height: 361 px
..   :width: 833 px
   :scale: 50 %

The image on the left shows a typical LM image with small C2 aperture. And 
AutoControur can be easily run on this image. After going through the meshes, 
the closest one to the stage center is found and moved to. The image on the 
right is after the script run. This method seems direct and simple. 

The script is fiarly simple as shown below:

.. code-block:: ruby

  ScriptName MoveToMesh
  # script to draw mesh polygon and most to closest one
  
  # Assume buffer A has an LM image, crop to smaller area
  # to save time. 
  #Search          
  CropCenterToSize A 2000 2000      
  
  # get current stage pposition
  ReportStageXYZ X0 Y0
  
  # Contour
  AutoContourGridSquares A 2 0.5 0.4
  
  # last item before contour
  ReportOtherItem -1
  Index0 = $NavIndex 
  
  # make nav point and get the new last one
  # [#L] [#U] [#M] [#S] [#I] [#B]
  MakePolygonsAtSquares 2553 3175 39.3 0.91 696.9 
  ReportOtherItem -1
  Index1 = $NavIndex

  # how many meshes found and made into nav items
  len = $Index1 - $Index0
  
  # define arrays for distance and index
  NewArray DIS -1 $len
  NewArray RIG -1 $len
  
  # go throught all the polygon items just generated
  Loop $len ind
     Index = $index0 + $ind
     ReportOtherItem $index
     X1 = $repVal2
     Y1 = $repVal3
     # distence 
     DIS[$ind] = SQRT ( ( $X1 - $X0 ) *  ( $X1 - $X0 )  +  ( $Y1 - $Y0 ) *  ( $Y1 - $Y0 ) )
     RIG[$ind] = $index
  
     # Obtain minimum value
     If $ind == 1
          W =  $DIS[$ind] 
     Else 
          W = MIN $DIS[$ind] $W
     Endif
  EndLoop 
  
  # Now array is available, find the index for the minimal distance
  Loop $len ind
     if $DIS[$ind] == $W
        index = $RIG[$ind]
     Endif
  EndLoop 
  
  # now move to it
  ReportOtherItem $index
  echo MoveStageTo $repVal2 $repVal3
  MoveStageTo $repVal2 $repVal3
  
  # delete the polygons after moving
  Loop  $len ind
     DeleteNavigatorItem -1
  EndLoop 

  # Take a look 
  Search
