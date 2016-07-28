.. cryo-em_faq:

FAQ
===

This is list of Frequently Asked Questions about Umass Cryo-EM. Some questions are technical, while others are more general. 

How do I ...
------------

... convert .emi image file saved by FEI TIA software to MRC format?
   You use `raw2mrc <http://bio3d.colorado.edu/imod/doc/man/raw2mrc.html>`_ to do the job. 
   It is part of `IMOD <http://bio3d.colorado.edu/imod/>`_ . 
   emi file is a raw file with no extra header. As long as you know the X, Y dimensions of
   your image, the convertion is straightforward. 
   
   ::
   
      $ raw2mrc -x 4096 -y 4096 input.emi output.mrc
  
