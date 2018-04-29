.. _SerialEM_note_utilize-jpg-file-format:

SerialEM Note: Utilize JPG File format
======================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Created: 2018-04-29 
:Updated: 2018-04-29

.. glossary::

   Abstract
      SerialEM supprots MRC and TIF file formats. It can be singel file or stack file for both format. Although it doesn't directly
      save JPG file as default, it supports JPG file too. 
      
      JPG file can be very covenient for sharing and viewing due to small file size. When we send screening results to users on DropBox, 
      we also upload all the JPG files for LMM maps, MMM maps and single record shots. These JPG files take very little disc space and 
      can be directly viewed on the web browers. 
      
      I intruduce two scripts or functions here to save maps and single shots to JPG format. The purpose is to do it with SerialEM 
      imaging, without having to convert afterwards. 
      
.. _map_to_jpg:

Save Map Overview to JPG 
------------------------

The function to save maps to JPG is below. 

.. code-block:: ruby

    Function MapToJPG 0 0
    # 
    # SerialEM Script to convert map overview to a jpg image. 
    # it works on currently selected map item and should work for "Acquire at points...".
    # 
    #################################
    # Chen Xu <chen.xu@umassmed.edu>
    # Created: 2018-04-27
    # Updated: 2018-04-27
    #################################

    # skip non-map item
    ReportNavItem
    If $RepVal5 != 2        # if not a map item
      Echo -> Not a map item
      Exit
    EndIf

    # load map overview into Q unbinned
    SetUserSetting BufferToReadInto 16	# Q is 16th in alphabet, if A is 0.
    SetUserSetting LoadMapsUnbinned 1   
    LoadNavMap

    # make a jpeg image
    ReduceImage Q 2         # assuming loading buffer is Q, and reduce 2 to make JPG image density range more pleasant
    SaveToOtherFile A JPG JPG $navNote.jpg
    EndFunction
    
One trick here is to load map into a buffer unbinned before saving to JPG. When we make montage maps using script to open montage 
files, the default binning for ovewview display is usually not 1. To take advantage of full resolution of the map, we load it unbinned.

This function can be used by inserting a line right below line `NewMap` in your script like here:

.. code-block:: ruby
  
  NewMap
  CallFunction MyFuncs::MapToJPG
  

