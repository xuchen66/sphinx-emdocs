.. cryo-em_faq:

FAQ
===

This is list of Frequently Asked Questions about Umass Cryo-EM. Some questions are technical, while others are more general. 

How do I ...
------------

... display the screened data on my own computer locally?

The data for screening is usually at three kinds. 

1. Low Mag Montage (LMM) map, usually taken at mag from ~50X to ~150X. The data itself is in MRC 16-bit sign integer format. 
   It is a MRC stack file containing about ~62 pieces if at 46X. 
   
2. Medium Mag Montage (LMM) maps. This is usually taken at lower range of M on Talos, such as 1750X. It is also MRC stack file. 

3. High mag shots, usually taken at 22,000X or 28,000X. It is MRC stack file, each section is from an exposure. If K2 camera frame mode is used, the secion is usually a single image from aligned movie stacks in-fly. 

All the three kinds of data can be easily viewed using IMOD. For Windows, a package called "Windows3dmod" can be insatalled - from http://bio3d.colorado.edu/ftp/Windows3dmod/. All the other platform, a complete IMOD software package should be installed. IMOD User Guide can be found in http://bio3d.colorado.edu/imod/doc/guide.html. 
   
   
