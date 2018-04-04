
.. _SerialEM_make_all_LMM:

SerialEM Note: Make All LMM Maps Automatically
==============================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2018-04-03
:Last_Updated: 2018-04-03 

.. glossary::

   Abstract
     We found that it was extremly useful to be able to make Low Mag Montage (LMM) maps for all the grids in autoloader 
     cassette automatically. Since it can take a while for multiple grids, you should give yourself a good break while 
     scope is busy working without feeling guilty.   
      
.. _procedure:

Procedure
---------

Here are steps to follow. 

1. Dock the cassette. After temperature in the autoloader recovers, do *Inventory*.
#. Setup image condition. I do it inside or SerialEM Low-Dose mode. I use **Search** area for the job. On our Krios with GIF/K2, 
   I set mag for **Search** as 220X (I cannot go lower as wish, because some hardware piece in lower portion of column will
   start to cut into image.). 
#. Setup proper exposure and binning for **Search** parameter from camera control panel. I usually use binning 2, 
   exposure 1 seconds, and in Linear mode (mP mode, Spotsize 8).
#. Take a *Search* shot, make sure the count value is proper, no beam/aperture edge in the image. 
#. Navigator - Montaging & Grids -  Setup Full Montage. Make sure **Search** is checked in the montage setup dialog window.
   Define a file like LMM.map. 
#. Edit script **Cars** to reflect cartridge and sample information, like below:

.. code-block:: ruby

    ScriptName Cars

    ## parameter of 1)  folder 2) Car and 3) sample name
    ## to be called by LMMCars and other

    # define where to save 
    SetDirectory X:\Munan_20180402

    ## define cartirges and sample names
    cat = { 2 3 4 5 6 7 }
    name = { 56-g1 56-g2 56-g3 56-g4 54-g2 54-g4 }
    
Here you define folder location, cartridge #, and sample names. The map filename will have the info in it, such as 
`LMM-Car2-56-g1.st`. 

7. Now run the Script **LMMCars** as below:

.. code-block:: ruby

    ScriptName LMMCars
    # LMM for multiple cartriges, assumes the montage file opened.

    ##########################
    # navigator must be open
    ##########################
    Call Cars
    
    ##### No editing Below ############
    CallFunction LMMCars
    ## in the end,  rise mag to settle temp & Close the valves
    #GoToLowDoseArea  V
    SetColumnOrGunValve 0
    
    ###############################################################
    Function LMMCars 0 0 

    Loop $#cat index
    LoadCartridge $cat[$index]
    #SetNavRegistration $cat[$index]
    SetColumnOrGunValve 1
    MoveStageTo 0 0 
    OpenNewMontage 0 0 LMM-Car$cat[$index]-$name[$index].st
    Montage 
    NewMap
    CloseFile
    EndLoop 

    EndFunction 
