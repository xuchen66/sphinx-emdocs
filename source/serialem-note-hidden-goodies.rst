.. _serialEM-note-hidden-goodies:

SerialEM Note: Hidden Goodies
=============================

:Author: Chen Xu
:Contact: <Chen.Xu@umassmed.edu>
:Date-created: Nov 21, 2020
:Last-updated: May 5, 2021

.. glossary::

   Abstract
      One of the amazing things about SerialEM, to me, is that I almost always
      find some cool functions and features which I did not use before. It
      could be also true for some other users, even you might not regard yourself
      as a novice user. If you see someone using it, you immediately get
      it. Otherwise, you might not realize that the cool function exists. 

      In this note, I want to give a few examples to show some of the hidden
      beauties I like a lot. And I hope you can learn them from reading this
      note if you don't know yet. Hopefully, the example list can grow longer
      with time and more exploration. 
      
.. _example_1:

Example 1 - delete multiple nav items at once
---------------------------------------------

This might not sound anything special, as you can use the ``Delete`` button
on Navigator window easily, and you just click on the button when the item
is highlighted, and you do this multiple time to delete a bunch of items.
However, if you want to delete a series (a lot of points) items that do not 
belong to a single group or are only part of a group, this might become a 
little troublesome. 

Here is a trick - to use SHIFT-D for this: you first click on the first item
to be removed and press SHIFT-D, then you click on the last item and press
SHIFT-D again; this will remove all the items in the range regardless their
group properties. 

You might find there are many hot keys available which you did not pay
attention before.

https://bio3d.colorado.edu/SerialEM/betaHlp/html/about_mouse.htm

.. _example_2:

Example 2 - make multiple polygon montage maps at once
-------------------------------------------------------

Your reaction might be that you already well know how to do this. Indeed,
this can be done with steps below:

1. Open a polygon montage map file via menu *Navigator - Montaging &
   Grids - Setup Polygon Montage*.
2. Add point items to all and each of the montage positions to be acquired,
   and give them flag "**A**". 
3. *Navigator - Acquire at points - Acquire Map Image or Montage*.

One of the nice key things here is that once the montage map is open and current,
the program knows how to make montage for each of the positions. The montage
setup dialog is only set once. Because all the items share the same
file, there is no need to draw multiple polygons at different locations. 

We also do this very often. We collect all the good meshes to make them into
montage maps so we can pick and realign to each positions later. However, there are a
couple of things we don't like with this way: 

- The super-stack file for all the montages can be very large. It is not handy to look at a particular mesh off-line. 
- The section # of the file is from 0,1... to the last one, they are directly linked with mesh label/numbers. 

It would be really nice to make all the meshes to have their own separate files and
with the mesh ID in the filenames, something like *Grid3-Mesh8.map*. This way,
we can check each mesh map easily when off-line. On a windows computer
with 3dmod installed, this is as simple as double clicking on the filename. 

How to do that? Not so easy anymore? If you don't already know, here is how:

1. add a polygon item as you normally do. 
#. make sure this polygon item is above of all and any mesh point items
   you already add. You do this by left mouse dragging the polygin item UP.
#. *Navigator - Options - Use Item Labels in Filenames*.
#. With polygon item highlighted, check the boxes before "Acquire (A)" and "New file at
   item". 
#. In the pop-up "Property of File to Open" dialog window, select "Montaged
   Images". And check both boxes before "Fit montage to polygon" and "Skip
   montage setup dialog when fitting in future". 
#. Input in the pop-up montage setup dialog window.
#. Input your string, e.g. "Grid3-Mesh" in the filename dialog, click
   ``Save``.
#. Now highlight the mesh item you want to make map, check "Acquire (A)" and
   "New file at item". 
#. repeat for all the mesh items. 

After you do Navigator - Acquire at Point, you will get multiple files such as 
*Grid3-Mesh3.map, Grid3-Mesh4.map, Grid3-Mesh15.map* etc.. Very nice, isn't it?

Here each mesh map will have its own filename. During collection, the filenames 
are opened and closed for each item. The setup step uses **heritage** function which 
is also quite hidden. 

.. tip::

   To obtain filename with MapID and ItemID in the filename string, here is a tip:
   
   .. code-block:: ruby
      :linenos:
      :caption: Get Map-index as part of filename

      ReportNavItem 
      NavIndex = $repVal1
      pt_label = $NavLabel

      NavIndexItemDrawnOn $NavIndex
      ReportOtherItem $repVal1
      map_label = $NavLabel

      filename = $map_label-$pt_label

.. _example_3:

Example 3 - make snapshot with nav features
-------------------------------------------

We already know SerialEM script has a command to make a snapshot of an image
in a buffer and save it in JPG format. But that doesn't include any nav
items drawn or a scalebar. A snapshot function with possibility of
including navigator items drawn and a scalebar can be very useful sometimes.

For example, in a screening session, we make a mesh montage map and pick a
new point items on the map from different areas. Before or after we take all
the images from these point items, it would be really cool to have a
snapshot map image with all the points drawn on it. It can be very useful 
for us to track the image quality with ice conditions etc., because we know 
which image is from which area in the map. 

Something like an image below:

**Fig.1 An example snapshot** (click for full size image)

.. image:: ../images/snap.jpg
   :scale: 15 %
   :alt: snapshot with nav feature
   :align: center

This function has been implemented fairly recently. It has a small GUI tool window 
and a script command for this task. You can open this tool window from *Window - Take Image Snapshot...*.

**Fig.2 Snapshot Tool Window** (click for full size image)

.. image:: ../images/snapshot-window.jpg
   :scale: 50 %
   :alt: snapshot with nav feature
   :align: center

There is also a command which can be used like this:

.. code-block:: ruby

   SnapshotToFile 1 1 1 JPG JPG snap.jpg

For more complete information about this little function, please check helpfile section:

https://bio3d.colorado.edu/SerialEM/betaHlp/html/hidd_screenshot.htm

and command usage description. 

.. _example_4:

Example 4 - Run script at SerialEM program start or exit
--------------------------------------------------------

This is a new feature in 3.9 beta. It can be very handy if you have some
tasks to do when you startup and exit. For example, you might want to get into
Low Dose mode and clear some persistent variable from last run. Or you have 
some other tasks to do when you quit SerialEM program. 

You can setup it from menu *Script - Run at Program Start ...* or *- Run at
Program End...*, and define a script accordingly. Here I give two examples
- one to define current working directory in the startup and one to make sure 
column and gun valves be taken care of.

Below script running at program start will pop up a file chooser to ask you 
define the current working directory. 

.. code-block:: ruby
   :linenos:
   :caption: StartUp Script

   ScriptName StartUp
   # script to run when starting SerialEM program

   #SetLowDoseMode 1
   #GoToLowDoseArea V

   SetDiectory X:\
   UserSetDirectory 
   Echo -----------
   ReportDirectory 
   Echo -----------
   OpenChooserInCurrentDir

Here is my little script to run at end.

.. code-block:: ruby
   :linenos:
   :caption: Ending Script

    ScriptName EndingScript

    # script to run when exiting SerialEM program
    
    ReportProperty NoScope noscope           # determine on Dummy or not
    If $noscope == 0
      ## Close Column/Gun Valves if they are OPEN
      ReportColumnOrGunValve
      If $repVal1 == 1    # open
         YesNoBox Column/Gun Valves are OPEN, do you want to Close Them?
         If $repVal1 == 1 # answer Yes
           SetColumnOrGunValve 0
           Echo ------ Now Valves are CLOSED! ------
         Else
           Echo Valves are still OPEN!
         Endif
      Else
         Echo Already closed!
      Endif 
    Endif

The window below will pop up when exiting SerialEM so you will never forget about this. 

**Fig.3 YesNo Window** (click for full size image)

.. image:: ../images/YesNo-valves.JPG
   :scale: 50 %
   :alt: Yes No window to remind valve
   :align: center

Clicking on ``Yes`` will close the valves and ``No`` will keep them open. 

.. _example_5:

Example 5 - Scripting With Python
---------------------------------

SerialEM Python Code Example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Around March 23, 2021, scripting also supports python. SerialEM native collection of commands act like a module. Here is an example 
of the same CycleTargetFocus function in Python code. 

.. code-block:: python
   :linenos:
   :caption: Python Example Code

   #!Python
   #ScriptName CycleTargetDefocus
   import serialem
   
   ## function
   def CycleTargetDefocus(low, high, step):
      print('==> running CycleTargetDefocus ...')
      print('    ---> low, high, step is ', low, high, step)
      tarDef = serialem.ReportTargetDefocus()
      if tarDef > low or tarDef < high + step:
         serialem.SetTargetDefocus(low)
      else:
         serialem.IncTargetDefocus(-step)
         serialem.ChangeFocus(-step)

   ## run it 
   CycleTargetDefocus(-1.0, -3.0, 0.1)


Calling Python Script From Regular Script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Around Apr 29, 2021, SerialEM not only got more matured at supporting Python, but also provides functionality to call regular script from a Python script and Vice Versa. The variable's value can also pass to each other. Below are two example scripts, one in Python and one in regular. 

.. code-block:: Ruby
   :linenos:
   :caption: Regular Script
   
   ScriptName Regular
   a = { 1 2 3 }

   echo a = $a
   Call Python
   Echo b = $b

.. code-block:: python
   :linenos:
   :caption: Python Script
   
   #!Python
   #ScriptName Python
   import serialem as sem

   #print('>>> running Python script ...')
   #sem.CallFunction('Hello::ChangeMag', '', 4)
   #sem.CallFunction('Hello::SetMagIndex', '', 17)

   ## get a
   ret = SEMarrayToInts(sem.GetVariable('a'))
   #print(ret)

   ## make reversed a into b
   b = ret[::-1]
   #print(b)

   ## get b ready for regular
   sem.SetVariable('b',listToSEMarray(b))
   
Running regular SerialEM script "Regular" by clciking *Run* button from the editor, the log window prints:

.. code-block:: python

   a = 1  2  3
   b = 3  2  1
   
As you can see, it calls Python script "Python". The array *a* defined in the regular script is received and convert to python list in the python script. After doing something in Python script (reverse array a in this case), it can get new array ready to be fetched by regular script. 

Calling Regular Script and Function From Python Script 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

One can also do reverse - calling regular script from a Python one and passing values of list variable to from Python to regular script. Look at two scripts below:

.. code-block:: python
   :linenos:
   :caption: Python Script
   
   #!Python
   #ScriptName Python
   import serialem as sem

   a = [ 1, 2, 3, 4, 5, 6, 7 ]
   sem.SetVariable('a',listToSEMarray(a))
   sem.Call('Regular')
   
.. code-block:: ruby
   :linenos:
   :caption: Regular Script
   
   Echo --- running Regular Script
   Echo $a
   
Running the "Python" script gives this in log window:

.. code-block:: ruby

   --- running Regular Script
   1  2  3  4  5  6  7

Python External Control
~~~~~~~~~~~~~~~~~~~~~~~

You can also use Python Console to control SerialEM processs. SerialEM acts as a complete embedded system in this case, and Python is completely external. 

.. code-block:: ruby
   :caption: Python External Control - Console
   
   PS C:\Users\xuchen> python
   Python 3.9.2 (tags/v3.9.2:1a79785, Feb 19 2021, 13:44:55) [MSC v.1928 64 bit (AMD64)] on win32
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import sys
   >>> sys.path.insert(0,'C:\Program Files\SerialEM\PythonModules')
   >>> import serialem as sem
   >>> sem.ReportTargetDefocus()
   -1.600000023841858
   >>> sem.ReportMag()
   (59000.0, 0.0)
   >>> x = sem.ReportMag()
   >>> type(x)
   <class 'tuple'>
   >>> print(x[0])
   59000.0
   >>> sem.GoToLowDoseArea('R')
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   serialem.SEMerror
   >>> sem.SetLowDoseMode(1)
   0.0
   >>> sem.GoToLowDoseArea('R')
   >>> sem.ReportLowDose()
   (1.0, 3.0)
   >>>
   
Below is Python external control from a Mac. 

.. code-block:: ruby
   :caption: Python Console on Mac (to connect to SerialEM on Windows (192.168.1.16))
   
   (base) UMWMLF8LVCJ% python                     ~/tem/SerialEM/build/lib.macosx-10.9-x86_64-3.8
   Python 3.8.5 (default, Sep  4 2020, 02:22:02) 
   [Clang 10.0.0 ] :: Anaconda, Inc. on darwin
   Type "help", "copyright", "credits" or "license" for more information.
   >>> import sys
   KeyboardInterrupt
   >>> sys.path.insert(0,'/Users/xuchen/tem/SerialEM/build/lib.macosx-10.9-x86_64-3.8')
   >>> import serialem as sem
   >>> sem.ConnectToSEM(48888,'192.168.1.16')
   >>> sem.ReportMag()
   (59000.0, 0.0)
   >>> sem.GoToLowDoseArea('V')
   >>> sem.ReportLowDose()
   (1.0, 0.0)
   >>> 

Here is another example to run a python script on Mac to control a SerialEM runnning on Windows:

.. code-block:: 
   :linenos:
   :caption: Another Python Script Example - to cycle LD areas, twice. 
   
   import sys
   sys.path.insert(0,'/Users/xuchen/tem/SerialEM/build/lib.macosx-10.9-x86_64-3.8')
   import serialem as sem
   sem.ConnectToSEM(48888,'192.168.1.16')
   sem.ReportMag()
   ld = sem.ReportLowDose()        # tuple
   ld = float(ld[0])               # float
   if ld == 0:
      sem.SetLowDoseMode(1)

   ## function to cycle LD areas, twice
   def cycleLD():
       for area in [ 'V', 'F', 'T', 'R' ] * 2 :
           sem.GoToLowDoseArea(area)
           sem.ReportMag()
           sem.ReportLowDose()

   cycleLD()

   sem.ReportLowDose()
   sem.Exit(1)
   exit()

Embedding a Python Script in Regular Script
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We know that both type of scripts can call each other. As things getting even fancier, now we can also embed a Python Script block directly in the Regular Script. Below is an example of that. Assuming there is a Python Script called "PyFuncs", inside that there is a Python function call "CycleTargetDefocus()", as mentioned earlier. The example below is to call that Python function directly from Regular script without a dedicated script editor for the python part. The single "hybrid" script gets the job done.

.. code-block:: ruby
   :linenos:
   :caption: Embedded Python Script Example
   
   ScriptName Regular
   
   low = -1.
   high = -2.5
   step = 0.1

   PythonScript $low $high $step
   #!Python
   #inlcude PyFuncs

   a = SEMargStrings      # string list ["-1.", "-2.5", "0.1"] now available 
   
   # convert to float
   for i in range(0, len(a)):
       a[i] = float(a[i])

   CycleTargetDefocus(a[0],a[1],a[2])
   EndPythonScript


The embedded Python clode is bewteen "PythonScript" and "EndPythonScript". More usefully, we can even pass some of the regular script variables into
the Python, by placing arguments after "PythonScript" and a special Python variable "SEMargStrings". In this case, SEMargStrings has value of a string list [ "-1.", "-2.5", "0.1"]. We convert it into real floats so they can be given to the function. 


