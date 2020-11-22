.. _serialEM-note-hidden-goodies:

SerialEM Note: Hidden Goodies
=============================

:Author: Chen Xu
:Contact: <Chen.Xu@umassmed.edu>
:Date-created: 2020-11-21
:Last-updated: 2020-11-21

.. glossary::

   Abstract
      One of the amazing things about SerialEM, to me, is that I almost always
      find some cool functions and features which I did not use before. It
      could be also true for some other users, even you might not regard yourself
      as a novice user. If you see someone using it, you immediately get
      it. Otherwise, you might not realize that the cool function exists. 

      In this note, I want to give a few examples to show some of the hidden
      beauties I like a lot. And I hope you can learn them from reading this
      note if you don't know yet. Hopefully, the example list can go longer
      with time and more exploration. 
      
.. _example_1:

Example 1 - delete multiple nav items at once
---------------------------------------------

This might not sound anything special, as you can use the ``Delete`` button
on Navigator window easily, and you just click on the button when the item
is highlighted, and you do this multiple time to delete a bunch of items.
However, if you want to delete a series (points) items that do not belong to
a single group, this might become a little troublesome. 

Here is a trick to use SHIFT-D for this: you first click on the first item
to be removed and press SHIFT-D, then you click on the last item and press
SHIFT-D again; this will remove all the items in the range regardless their
group. 

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

* The super-stack file for all the montages can be very large. It is 
   not handy to look at a particular mesh off-line. 
* The section # of the file is from 0,1... to the last one, they
   are directly linked with mesh label/numbers. 

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


