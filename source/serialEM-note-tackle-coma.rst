.. _serialEM-note-tacke-coma:

SerialEM Note: Tackle the Coma
==============================

:Author: Chen Xu
:Contact: <Chen.Xu@umassmed.edu>
:Date-created: 2018-3-11
:Last-updated: 2018-3-11

.. glossary::

   Abstract
      For high resolution data, coma is always a concern or something we don't want to miss or ignore.  With image-beam shift, 
      even on a carefully aligned, coma-free scope, there is always some coma induced by the shift. On the other hand, if we can 
      collect CryoEM data with some image shift, that would increase the effcieny a lot. The question is how much worse the data becomes 
      with certainly mount of image shift in the shots. A more important question is if we can have a way to correct coma that is
      induced by image-beam shift. 
      
      In this note, I try to explain how to access coma induced by the shift, more or less quantitatively and how to correct the coma
      using currently available functions in SerialEM. 
      
      This is very fresh, work in progress, both in SerialEM and this document. 
      
      
.. _background:

Background
----------

The coma we discuss here is axial coma. It is how much the incedent electron beam is off from perfect optical axis. This small angle makes electron beam hitting on specimen not perpendicularly. The effect might not be easily seen visully and directly from a typical single particle electron image, but it is very real. If you look at inorganic material lattice image in electron micsocope, a tiny alpha angle change will make the lattice image no longer even and symmetic. For single particle image, this introduces a phase error becasue we assume
all the images are taken with perpandicular incedent beam. It is hard or almost impossible to correct this error from all the images taken with coma, at least for single particle images. Therefore, if we can elimnate this error experimentally, that would be a good thing to do.

