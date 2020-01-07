.. _scalebar:

Adding a Scalebar to Images
===========================

:Author: Christna Ouch
:Contact: <Christna.Ouch@umassmed.edu>
:Date_Created: 2020-01-02
:Last_Updated: 2020-01-06
.. _glossary:

Overview
--------
    
Stack file images taken with SerialEM can be processed and labeled using Imod and ImageMagick. 

I have written a simple shell script that will add a scale bar to the lower right-hand side of an image. This script first converts your stack file into a series of tifs using mrc2tif (Imod), scales the image by 50%, normalizes, adds a scale bar and converts to jpg using ImageMagick. 


Prerequistes
------------

This program requires that you have both Imod and Imagemagick installed on your computer. 

On linux, you can use your package manager to install Imagemagick. Download and run the install packages Imod.

On Windows, you can use use the cygwin environment to install both Imod and ImageMagick. Follow the instructions on Imod's website to install cygwin and Imod. After the installation, rerun Cygwin installer, then select the internet as a source. Type in magick into the search bar and install the ImageMagick package.

After installation, both Imod and ImageMagick should be installed. Check that each program is install by running 'convert --version' and 'imod' .


Script Download
---------------

Download the script from: https://github.com/ouchc/sphinx-emdocs/blob/ouchc/scripts/labeling_script_new_v4.zip

Extract the sh-script to the directory with your stack files.

Editing the Script
------------------

The script has a few fields that needs to be updated for your particular microscope setup. Edit the top of the file for the magifications and pixel size. For this case, we have three magnifications, 1250x, 17.5k and 45k with pixel sizes 32.91, 2.27 and 0.87 angstroms respectively. Edit these to match your micropscope setup. It is important that these values match the pixel sizes for your images for each mag. Verify it by running 'header' command on the stack file.

::

 RO image file on unit   1 : btx2-g2-Car7-R3-45k-edge.st     Size=     690529 K

 Number of columns, rows, sections .....    5760    4092      15
 Map mode ..............................    1   (16-bit integer)           
 Start cols, rows, sects, grid x,y,z ...    0     0     0    5760   4092     15
 Pixel spacing (Angstroms)..............  0.8700     0.8700     0.8700    
 Cell angles ...........................   90.000   90.000   90.000
 Fast, medium, slow axes ...............    X    Y    Z
 Origin on x,y,z .......................    0.000       0.000       0.000    
 Minimum density .......................   0.0000    
 Maximum density .......................   6621.0    
 Mean density ..........................   922.64    
 tilt angles (original,current) ........   0.0   0.0   0.0   0.0   0.0   0.0
 Space group,# extra bytes,idtype,lens .        0     3072        0        0

This script captures the pixel size from the header and generates a scale bar based on that pixel size.

The other parameters to edit is scale_length, scale_label and scale_label_offset. The length is in nanometers and the offset is used to center the scalebar label. Adjust this value to center the label. Alternatively, set the scalebar_label_offset to 0 to left-justify the label. The scale_x2 parameter defines the right-most position of the scale-bar. The current value of 2750 works well for K3 micrographs binned 2x. For K2 data or other cameras, you may need to reduce this value so that it is within the bounds of the bin2 image. Adjust y_anchor and y_text to position the scalebar and label respectively. The options for the header are shown below.

::

 # Magification #1
 mag1="1750"
 apix1="32.91"
 scale_length_1="1000"
 scale_label_1="1000 nm"
 scale_label_offset_1="10"
 
 # Magnification #2
 mag2="17.5k"
 apix2="2.277"
 scale_length_2="100"
 scale_label_2="100 nm"
 scale_label_offset_2="55"
 
 # Magnification #3
 mag3="45k"
 apix3="0.87"
 scale_length_3="50"
 scale_label_3="50 nm"
 scale_label_offset_3="100"
 
 # Scale bar location options
 # We will use coordinate 2750,1900 as an anchor point for the right-most position on our scale bar. labeling will be relative to that point.
 scale_x2="2750"
 y_anchor="1900"
 
 # Fonts will be positioned at a y value of 1940
 y_text="1940"


Running the Script
------------------
Place the script in the same directory with all of your stack files that need to be labeled. These stack files need to have a .st extension to be recognized by the script. Open a linux terminal or Cygwin and cd to that directory. Run the edited script. This script will create subdirectories using the mag variables defined in the header. 

Note: When editing the script in windows, hidden newline characters may be added to the the file. These newline characters prevent the script from running. Please run this command in cygwin to remove these hidden characters. Replace input with your original script and output for the fixed script

::

 Command syntax:
 tr -d '\r' <input >output

 Example:
 tr -d '\r' <labeling_script_new_v4.sh >labeling_script_new_v4-fixed.sh


Use a coding editor or notepad (do not use wordpad or office) to make future changes to the file.






