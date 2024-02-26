.. _SerialEM_note_run_script_during_montaging:

SerialEM Note: Running A Script During Montaging
================================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date Created: Feb. 24, 2024
:Last Updated: Feb. 24, 2024

.. glossary::

   Abstract
      SerialEM allows to run a script during montaging procedure. This can
      be handy sometimes. In this note, I will give a few cases where this
      can be very useful.

.. _enable:

Enable it by a property
-----------------------

If you put the line in your property file as below:

.. code-block:: ruby

    MontageScriptToRun   7

Restarting SerialEM will have this function enabled. 

Or you can even conveniently using script command to turn it on:

.. code-block:: ruby

    SetProperty MontageScriptToRun 7

And you can turn it off (disable it) by:

.. code-block:: ruby

    SetProperty MontageScriptToRun 0

When it is enabled, the script number 7 will run after the montage image
acquired and before saved. 

.. _case_studies:

Case Studies
------------

A few case studies I can think of might take advantages of this function. 

1. Dynamic defocus adjustment in-the-fly. 

If we collect a montage for a large area, the specimen might not be always
in flat plane, and height changes are expected. In this situation, one can 
easily run CTF program such as "ctfplotter" to obtain the defocus and adjust 
objective lens dynamically. 

2. Collect multiple montages at the same time

If we collect a montage tilting series, say 2 x 2 image shift montage, we
might also want to collect two more montages along the tilting axis so at
every angle we can have three montages rather than one. This is perfectly
reasonable for lamella specimen. 

This is what we can do.

a. before starting the tilting series collection, open three files - 
two for image stack file, and one for montage file say 2 x 2. After this, 
the montage file is on the "top" and file number is 3, while the other
normal image files opened are 1 and 2. Lets assume they have name as 
mon.st, f1.st and f2.st.  

b. Have a script in 7th script editor like below:

.. code-block:: ruby

    ScriptName ToRun

    ImageShiftByMicrons 3.5 0    # shift 3.5um along tilting axis
    R
    ImageShiftByMicrons -3.5 0   # clear IS

    ImageShiftByMicrons -3.5 0   # -3.5um
    R
    ImageShiftByMicrons 3.5 0    # clear IS
    # at this point, three images are in buffer A, B and C. C is one from
    # montage shot
    
    Save A 1
    Save B 2
    Copy C A        # let montager save which happens after the script finishes


Thus, after montage is done, there will be also two stack files for single
image shots. Those two images stacks are not a montage stack, as there is no
piece list info in the file header. However, with a piece list info, they
have all the info to be treated as montages. 

.. code-block:: ruby

    $ extractpieces mon.st mon.pl

f1.st and f2.st have the exact piece list info as mon.st.. So you can
display them with the piece list info:

.. code-block:: ruby

    $ 3dmod -p mon.pl f1.st

It will work! 

