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
  
This will perform dark reference for K2/K3 camera every 3 hours. This is very handy indeed. Another example is ``RefineZLP``.

.. code-block:: ruby
   
   RefineZLP 30
   
This will perform refining ZLP every 30 minutes. 
   
However, if we want to set a timer and to do a multiple actions when time is up, a more flexible timer is needed. In the case shown in picture below, which is 
Au-foil grid, we use multiple hole mechanism to collected images in these 4 holes using beam-image shift while the stage is 
centered at the middle of the 4 hole pattern. When the procedure is finished, the shift is reset and Record beam would be 
hitting on the black Au crystals. 

**Fig.1 4-hole black Au crystals**

.. image:: ../images/Au-4-holes.png
   :scale: 100 %
..   :height: 544 px
   :width: 384 px
   :alt: DUMMY instance property
   :align: center

It is known that this kind of black crystal film is bad for ``refineZLP`` routine to work properly. It needs to use a hole are
make it work. As you can see, a simple timer built in for a specific function is not sufficient here. Two actions are needed: 1) move to one of the four holes and 2) perform RefineZLP. It requires a more flexible timer to do this. 

.. _flexible_timer:

Flexible Timer 
--------------

A typical timer which could handle multiple actions would be like this:

.. code-block:: ruby

   IF time is up
      do task1 
      do task2
      ...
   EndIf
   
One of the script commands related to timer is ``SetCustomTime``. Below is example code to perform two actions mentioned above.

.. code-block:: ruby

   ### move to a hole and refineZLP every 30 minutes
   ReportCustomTime ZLP
   if $elapsed >= 30
       StageToLastMultiHole
       #ImageShiftToLastMultiHole
       RefineZLP
       # reset it
       SetCustomTime ZLP
   Endif
   ###


