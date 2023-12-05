.. _SerialEM_note_refineZLP-on-Aufoil-grid:

SerialEM Note: Refine ZLP on Au Foil Grids
==========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date-Created: 2019-08-23 
:Last-Updated: 2023-12-04

.. glossary::

   Abstract
      This is to introduce new implemented "Custom Time" script commands to
      handle refining Zero Loss Peak (ZLP) over Au foil girds. 
      
.. _background_info:

Background Information 
----------------------

SerialEM has one script command to handle the ZLP refinement over time. Very
useful. For example, if we put a line at the end of script **LD-group** like
this:

.. code-block:: ruby

  RefineZLP 30
 
It will refine ZLP position if longer than 30 minutes since last refinement.
It works very well for us, on a typical carbon based holey grid.
Unfortunately, it doesn't run well on a gold foil based grid which it is
very "black" between holes. This is mainly due to large pieces of gold
crystals that form the film diffract electron beams away in relatively large
angle so they don't hit the camera. SerialEM's ``refineZLP`` routine works
by comparing camera counts at different energy shifts. If there is clear
count drop, the slit edge is detected. When with a Au foil grid, if the area
but not a hole is used for such refinement, the black nature of the film
causes this procedure likely to fail. When we take multi-hole exposure for 4
holes, for example, the stage position is on such "black" area.

It is possible to move to one of the multi-holes after exposure is done, but
we prefer not to it for every exposure because ZLP might have been refined
recently. Otherwise, time is wasted. 

Therefore, we need a timer to track time and then perform two tasks in this
situation after certain period. 1) move to a nearby hole, and 2) refine ZLP.  

.. _timer_function_commands:

Timer Function Commands
-----------------------

This timer function is now available.

The two script commands related to the timer functions are:

.. code-block:: ruby

  SetCustomTime name
  ReportCustomInterval name
  
Below is how to use them. 

.. _Use_the_timer_funtions:

Use the Timer Functions
-----------------------

1. set timer to start counting, for example, one can simply run one-line
command like this:

.. code-block:: ruby

  SetCustomTime ZLP
  
ZLP is the name of this timer. You can set up multiple timers with different
names so they won't confuse. 

2. Add this section to the end of your ``LD-group``:

.. code-block:: ruby

  #SetCustomTime ZLP
  ReportCustomInterval ZLP
  If $repVal1 >= 30 
    MoveToLastMultiHole         # move to a hole (bright area)
    RefineZLP                   # refine ZLP now!
    SetCustomTime ZLP           # reset timer
  Endif
  
You can use the timer function, similar to the above example, to perform
various periodic tasks across the Acquire items conveniently. 
