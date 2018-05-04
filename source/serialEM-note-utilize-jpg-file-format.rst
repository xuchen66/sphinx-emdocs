.. _SerialEM_note_utilize-jpg-file-format:

SerialEM Note: Utilize JPG File format
======================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Created: 2018-04-29 
:Updated: 2018-05-03

.. glossary::

   Abstract
      SerialEM supprots MRC and TIF file formats. Although SerialEM doesn't directly save JPG file from graphic interface as default, 
      it does support JPG file format. 
      
      JPG file format can be very convenient for sharing and viewing due to its small file size. When we send screening results to 
      users via DropBox, we also upload all the JPG files for LMM, MMM maps and single record shots. These JPG files take very little 
      disc space and can be directly viewed on the web browers. People love this feature.
      
      In this doc, two scripts or functions were introduced to conveniently save maps and single shots to JPG format. It is nice 
      to be able to do it in-the-fly with SerialEM imaging.
      
.. _map_to_jpg:

Save Map Overview to JPG 
------------------------

The function to save maps to JPG is below. 

.. code-block:: ruby

    Function MapToJPG 0 0
     
    # SerialEM Script to convert map overview to a jpg image. 
    # it works on currently selected map item and should work for "Acquire at points...".
    # 
    # Chen Xu <chen.xu@umassmed.edu>
    # Created: 2018-04-27
    # Updated: 2018-04-27

    # skip non-map item
    ReportNavItem
    If $RepVal5 != 2        # if not a map item
      Echo -> Not a map item, exit!
      Exit
    EndIf

    # load map overview into Q unbinned
    SetUserSetting BufferToReadInto 16	# Q is 16th in alphabet, if A is 0.
    SetUserSetting LoadMapsUnbinned 1   
    LoadNavMap

    # make a jpeg image
    ReduceImage Q 2         # loading buffer is Q, and reduce 2 to make JPG image density range more pleasant
    SaveToOtherFile A JPG JPG $navNote.jpg
    EndFunction
    
One of tricks here is to load map into a buffer **unbinned** before saving to JPG. When we make montage maps using script to open montage 
files, the default binning for ovewview display is usually not 1. To take advantage of full resolution of the map, we load it unbinned. 
The other trick is to define a temporary loading (Read-in) buffer so the read-in buffer setup in **Buffer Control** panel becomes 
irrelevant.   

This function can be used by inserting a line right below line ``NewMap`` in your script like here:

.. code-block:: ruby
  
  NewMap
  CallFunction MyFuncs::MapToJPG
  
It can also be used standalone as a script for multiple maps using "Acquire at points...".  

.. code-block:: ruby
  
  ScriptName MapToJPG
  CallFunction MyFuncs::MapToJPG 

This function and its script work on **current** item of map. However, if you run on a point item and create a map from this, the above function or script won't work on this in-the-fly situation because **current** item is the very point item instead of newly created map. 
For this, one needs to use a slight different one as below.

.. code-block:: ruby

   Function NewMapToJPG 0 0
   # 
   # SerialEM Script to convert last item - map overview to a jpg image. 
   # It uses Note string as part of jpg filename.
   # it works on an item which creates a map and should work for "Acquire at points..."
   # as "Run Script after". 
   # 
   # Chen Xu <chen.xu@umassmed.edu>
   # Created: 2018-04-27
   # Updated: 2018-04-30
   #

   # skip non-map item
   ReportOtherItem -1      # last item - supposedly the newly created map.
   If $RepVal5 != 2        # if not a map item
     Echo -> Not a map item, exit ...
     Exit
   EndIf

   # load map overview into Q unbinned
   SetUserSetting BufferToReadInto 16	# Q is 16th in alphabet, if A is 0.
   SetUserSetting LoadMapsUnbinned 1   
   LoadOtherMap -1				# last item on the nav list

   # make a jpeg image
   ReduceImage Q 2         # assuming loading buffer is Q, and reduce 2 to make JPG image density range more pleasant
   SaveToOtherFile A JPG JPG $navNote.jpg
   EndFunction

The trick here is to Report and Load the last item in the nav list which is the newly created map.

.. _shot_to_jpg:

Save Single Shots to JPG 
------------------------

We can also save every single shot to JPG format along with MRC images. The MRC file is required to be opened. The JPG filename 
contains root name of the MRC file and section numbers. 

.. code-block:: ruby

   Function AToJPG 0 0
   
   # SerialEM Script to save image in buffer A to a jpg image. 
   # Tt reduces image in A by 2 for comfortable JPG density range. It 
   # takes current filename and Z into jpg filename. Therefore, MRC file
   # is required to be opened.
   # 
   # Chen Xu <chen.xu@umassmed.edu>
   # Created: 2018-04-29
   # Updated: 2018-04-29
   
   ReportCurrentFilename 1
   root = $RepVal1 
   ReportFileZsize
   z = $RepVal1

   ReduceImage A 2
   SaveToOtherFile A JPG JPG $root-$z.jpg

It can be used after saving MRC image for each exposure, like below:

.. code-block:: ruby

   Record
   Save
   CallFunction MyFuncs::AToJPG
