.. _SerialEM_scripting_timer:

SerialEM Note: Flexible Timer
=============================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-created: 2019-10-09
:Last-updated: 2018-10-09

.. glossary::

   Abstract
      Note about the flexible timer availble for scripting. And example how to use it.  
      
.. _background_information:

Background information 
----------------------

In SerialEM scripting, there are a few command with timer built in. For example, command ``LongOperation`` can take a few actions
and with timer defined. Here is one of the example:

.. code-block:: ruby

  LongOperation Da 3 
  
This will perform dark reference for K2/K3 camera every 3 hours. This is very handy indeed. However, if we want to set a timer 
and to do a multiple actions when time is up, a more flexible timer is needed. In the case shown in picture below, this is 
Au-foil grid. We use multiple hole mechanism to collected images in these 4 holes using beam-image shift while the stage is 
centered at the middle of the 4 hole pattern. When the procedure is finished, the shift is set to 0 and Record beam would be 
hitting on the black Au crystals. 




0. Before LD is turned on, make sure beam is centered for both *mP* and *nP* beam. I usually use Direct Alignments to do this with 
   *mP* and *nP* beam. That is, turn *mP* on, Directly Alignments - Beam Shift (multi-function to center) - done. Repeat with *nP* mode. 
1. Turn on SerialEM LD.
#. Lower Down large screen or insert screen.
#. From Task - Specialized Options, make sure the "Adjust Focus on Probe Mode Change" is NOT checked. 
#. Set View Defocus Offset to 0 using dial Up-Down button on SerialEM LD Control Panel.
#. Select R area (radio button) on LD control panel. 
#. On microscope right panel, press "Eucentric Focus".
#. Reset Defocus (L2 button on our current setup for soft buttons, yours could different), this makes defocus display 0. 
#. Select V area (radio button) on LD control panel.
#. wait 6-7 seconds to allow scope to switch to this mag and *mP* mode.
#. On microscope right panel, press "Eucentric Focus".
#. Reset Defocus (L2 button on our current setup for soft buttons, yours could different), this makes defocus display 0. 
#. Set View Defocus Offset to target value (-300 in my case) using dial Up-Down button on SerialEM LD Control Panel.
#. From Task - Specialized Options, make sure the "Adjust Focus on Probe Mode Change" is **NOW** checked. 

That's it. 
