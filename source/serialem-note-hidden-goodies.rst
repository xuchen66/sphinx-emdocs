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
      could be also true some other users, even you might regard yourself
      as not a novice user. If you see someone uses it, you immediately get
      it. But you might not realize that the cool function exists. 

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

3. Navigator - Acquire at points - Acquire Map Image or Montage.

One of the key things here is that once the montage map is open and current,
the program knows how to make montage for each of the positions. The montage
setup dialog is only set once; and because all the items share the same
file, there is no need to draw multiple montage at different locations. 

