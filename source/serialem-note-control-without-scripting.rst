
.. _scriptless_control:

SerialEM Note: Scriptless Control
=================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Date_Created: 2021-09-18
:Last_Updated: 2021-09-18

.. glossary::

   Abstract
      Since 4.0, sophisticated control without scripting has become possible. In this note, I give an example with some explanation how to do
      this for a typical single particle data collection. 
      
      
.. _background:

Background Information
----------------------

There are basically two new features available in version 4.0. 

1. Adjust Eucentric Height using tilted beam.

2. Flexiblly arrange helping tasks and primary actions from "Acquire at Items" dialog window, without a script. 

In the past, a sophisticated script is need to control pipeline of actions for single particle data collection. Typically, the actions may include
eucentric height positioning, X-Y positioning, beam centering, aufofocusing within a range, drift control and final image taking. It is now possible 
to arrange these actions without scripting in the new version. 

.. _dialog:

The New "Acquire at Items" Dialog Window
----------------------------------------

Below is the new dialog window.

.. image:: ../images/acquire-full.JPG
   :scale: 25 %

1. First, select a map item from the navigator. You can double click to load the image, or single click to simply highlight.

- Before running the script, you can optionally crop out a template in SerialEM. See TemplateMatch_GUI for details.

2. Run TemplateMatch_GUI

|template_match2| |template_match3|

.. |template_match2| image:: ../images/template_match2.png
   :width: 45%

.. |template_match3| image:: ../images/template_match3.png
   :width: 45%

- +/- to zoom in and out.

- To crop, just click and drag.

- Change the threshold and click search until you are satisfied with the result.

- Click Save and Quit to merge the generated points into SerialEM. To quit without saving, close the window from the top.


.. code-block:: ruby

	ScriptName TemplateMatch_GUI

	### Before running this script:
	#     (Optional) Save a template of a hole/pattern as a jpg image.
	#             1. Crop a hole using ctrl+shift+drag,
	#             2. Menubar->Process-> Crop Image.
	#             3. Using the Edit/Run one Line prompt, run
	#                      SaveToOtherFile A JPG JPG T.jpg
	#

	# If something goes wrong, set Debug = 1
	Debug = 0              # True = 1 ; False = 0

	### semmatch arguments
	threshold = 0.8

	acquire = 1                                    # True = 1 ; False = 0
	groupOption = 4
		 #    0 = no groups
		 #    1 = groups based on radius
		 #    2 = all points as one group
		 #    3 = specify a certain number of groups
		 #    4 = specify number of points per group

	# names of temporary files used by semmatch
	outputNav = semmatch_nav.nav
	image = MMM.jpg
	template = T.jpg

	ReportIfNavOpen
	If $reportedValue1 != 2
		Exit
	Endif
	ReportNavFile 1
	navfile = $reportedValue1$reportedValue2
	navdir = $reportedValue3
	SetDirectory $navdir

	If $acquire != 1 AND $acquire != 0
		Echo acquire should be either 1 or 0
		Exit
	Endif

	If $Debug == 1
		debugStr = /k
	ElseIf $Debug == 0
		debugStr = /c
	Else
		Echo Debug should be either 1 or 0
		Exit
	Endif

	## load and bin MMM map
	ReportNavItem
	If $RepVal5 != 2        # if not a map item
		Echo Not a map item. Select a Map item from the navigator.
		Exit
	Endif
	MAP = $navLabel
	Echo Map Label: $MAP
	SetUserSetting BufferToReadInto 16
	SetUserSetting LoadMapsUnbinned 1
	# uncheck Montage Controls "Align pieces in overview"
	ReportUserSetting MontageAlignPieces alignChecked
	If $alignChecked == 1
		SetUserSetting MontageAlignPieces 0
	Endif
	LoadNavMap

	# reduce image if larger than 2000x2000
	maxdimLimit = 2000
	ImageProperties Q width height
	maxdim = $width
	If $width < $height
		maxdim = $height
	Endif

	If $maxdim < $maxdimLimit
		Copy Q A
		reduction = 1
	Else
		reduction = $maxdim / $maxdimLimit
		ReduceImage Q $reduction
	Endif
	Show Q

	## make a jpeg image
	SaveToOtherFile A JPG JPG $image
	Echo saved $image

	ReportOtherItem -1
	newLabel = $navIntLabel + 1

	RunInShell cmd $debugStr " "semmatch" \
	"--gui" \
	"--navfile" "$navfile" \
	"--reduction" "$reduction" \
	"--image" "$image" \
	"--template" "$template" \
	"--mapLabel" "$MAP" \
	"--newLabel" "$newLabel" \
	"--threshold" "$threshold" \
	"--groupOption" "$groupOption" \
	"--output" "$outputNav" \
	"--noBlurTemplate" \
	"--noBlurImage" \
	"--acquire" "$acquire" "

	MergeNavFile $outputNav
	If $alignChecked == 1
		SetUserSetting MontageAlignPieces 1
	Endif
	Show Q
