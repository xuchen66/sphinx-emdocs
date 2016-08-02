.. cryo-em_faq:

FAQ
===

This is list of Frequently Asked Questions about Umass Cryo-EM. Some questions are technical, while others are more general. 

How do I ...
------------

... convert .emi image file saved by FEI TIA software to MRC format?
..   You use `raw2mrc <http://bio3d.colorado.edu/imod/doc/man/raw2mrc.html>`_ to do the job. It is part of `IMOD <http://bio3d.colorado.edu/imod/>`_ package. An *.emi* file is a raw file with no extra header, but with 16-bit signed integer data type. As long as you know the X, Y dimensions of your image, the convertion is straightforward. For example, if an image has size 4096 x 4096, then the convertion is as below:
   
  .. ::
   
..      $ raw2mrc -x 4096 -y 4096 -t short input.emi output.mrc
   
 ..  If you need to batch convert a number of files, you can try this shell script. 
..  
..   .. code-block:: sh
..   
..      #!/bin/sh
..      for file in *.emi ;
..         do
..               root=`basename ${file} .emi`
..              raw2mrc -x 4096 -y 4096 -t short $file $root.mrc  
         done
..      exit
