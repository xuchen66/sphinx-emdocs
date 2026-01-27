.. _SerialEM_make_all_LMM:

SerialEM Note: Make All LMM Maps Automatically
==============================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: Apr 3, 2018
:Last_Updated: Jan. 26, 2026

.. glossary::

   Abstract
     We found that it was extremly useful to be able to make Low Mag Montage
     (LMM) maps for all the grids in autoloader cassette automatically.
     Since it can take a while for multiple grids, you should give yourself
     a good break while scope is busy working without feeling guilty.   
      
.. _procedure:

Procedure
---------

Here are steps to follow. 

1. Dock the cassette. After temperature in the autoloader recovers, do
   *Inventory*.
#. Setup image condition. I do it inside or SerialEM Low-Dose mode. I use
   **Search** area for the job. On our Krios with GIF/K2, I set mag for
   **Search** as 220X (I cannot go lower as wish, because some hardware
   piece in lower portion of column will start to cut into image, unless
   with very large IS offset for LD_Search). 
#. Setup proper exposure and binning for **Search** parameter from camera
   control panel. I usually use binning 2, exposure 1 seconds, and in Linear
   mode (mP mode, Spotsize 8).
#. Take a *Search* shot, make sure the count value is proper, no
   beam/aperture edge in the image. 
#. Navigator - Montaging & Grids -  Setup Full Montage. Make sure **Search**
   is checked in the montage setup dialog window.  Define a file like
   LMM.map. 
#. Edit script **Cars** to reflect cartridge and sample information, like
   below:

.. code-block:: 
   :linenos:
   :caption: Cars.txt

    ScriptName Cars

    # script of cartridges information 

    rootDir = X:\Munan_20180402
    cat = { 2 3 4 5 6 7 }
    name = { 56-g1 56-g2 56-g3 56-g4 54-g2 54-g4 }

    SetDirectory $rootDir 
    ReportDirectory 

    If $#cat != $#name
        Echo     >>> Arrays "cat" and "name" have different length, exit ...
        Exit
    else
        echo     cat = { $cat }
        echo name = { $name }
    Endif
    
Here you define folder location, cartridge #, and sample names. The map
filename will have the info in it, such as ``LMM-Car2-56-g1.st``. 

7. Now run the Script **LMMCars** as below:

.. code-block:: ruby
   :linenos:
   :caption: LMMCars.txt

    ScriptName LMMCars
    # LMM for multiple cartriges, assumes the montage file opened.

    ##########################
    # navigator must be open
    ##########################
    Call Cars
    
    ##### No editing Below ############
    CallFunction LMMCars
    ## in the end, rise mag to settle temp & Close the valves
    #GoToLowDoseArea Search
    SetColumnOrGunValve 0
    
    ###############################################################
    Function LMMCars 0 0 

    Loop $#cat index
       LoadCartridge $cat[$index]
       #SetNavRegistration $cat[$index]
       SetColumnOrGunValve 1
       SetApertureSize C2 150
       SetApertureSize Obj 0
       MoveStageTo 0 0 
       OpenNewMontage 0 0 LMM-Car$cat[$index]-$name[$index].st
       Montage 
       NewMap
       CloseFile
    EndLoop 

    EndFunction 

.. _convert_to_jpeg:

Convert LMM maps into JPEG format 
----------------------------------

For easy display and small file size, we usually convert all the maps in MRC
format to JPEG. 

   - Set Bin Overview to 1 on Montage control panel (default is usually
     higher than 1 with montage from command)
   - Load the map file, the overview will be displayed in a specific buffer
     such as Q
   - Run a small script 
   
.. code-block:: ruby
   :linenos:
   :caption: LMM->JPEG.txt

   ScriptName LMM->JPEG
   # convert to JPEG format for easy display
   
   SetDirectory X:\Munan_20180402
   
   # reduced image for good JPEG density range, redeuced one will be in A
   ReduceImage Q 2     
   SaveToOtherFile A JPEG JPEG LMM-Car2-56-g1.jpeg
   
.. note::

   - The JPEG image generated from above script is *true* JEPG file, not a
     JPG compressed TIFF file as before. Compressed JPG cannot be displayed
     properly by Photoshop and ImageJ, although preview, paint and webbroser
     can show them nicely. 
   - You can also convert MMM maps and single shot MRC image the same way. 
   
