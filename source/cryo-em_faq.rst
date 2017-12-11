.. cryo-em_faq:

FAQ
===

This is list of Frequently Asked Questions about Umass Cryo-EM. Some questions are technical, while others are more general. 

How do I ...
------------

.. _display:

... display the screened data on my own computer locally?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The data for screening is usually at three kinds. 

1. Low Mag Montage (LMM) map, usually taken at mag from ~50X to ~150X. The data itself is in MRC 16-bit sign integer format. 
   It is a MRC stack file containing about ~62 pieces if at 46X. 
   
2. Medium Mag Montage (LMM) maps. This is usually taken at lower range of M on Talos, such as 1750X. It is also MRC stack file. 

3. High mag shots, usually taken at 22,000X or 28,000X. It is MRC stack file, each section is from an exposure. If K2 camera frame mode is used, the secion is usually a single image from aligned movie stacks in-fly. 

All the three kinds of data can be easily viewed using IMOD. For Windows, a package called "Windows3dmod" can be insatalled - from http://bio3d.colorado.edu/ftp/Windows3dmod/. For all the other platforms including Windows, a complete IMOD software package be be installed. IMOD User Guide can be found in http://bio3d.colorado.edu/imod/doc/guide.html. 

.. Note::
   
   Since September 2017, most of screening images are also saved into JPG format at the same time when MRC files are saved. This 
   gives you a quick feedback for your sample conditions. The small file size makes it easy for up to upload to DropBox to share with 
   users. 
   
.. _mount_ntfs:

... mount the data hard drive I received from you?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The hard drive for data should be in its original filesystem which is normally Windows NTFS. The interface on the hard drive should be USB3. It is in my mind that the drive should be most compatable possible. 

If you plug the hard drive onto a Windows or Mac computer, the volume should automatially show up. And you can copy data out from the volume directly. 

If you want to mount the hard drive directly onto a Linux computer, you have to connect and mount it manually. 

After plugging the hard drive to USB port (USB3 preferred) on Linux computer, you should be able to see lines similar to these from `dmesg` command output on linux computer. 

.. code-block:: none

   [334449.716558] usb 4-1: new SuperSpeed USB device number 2 using xhci_hcd
   [334449.728460] usb 4-1: New USB device found, idVendor=0bc2, idProduct=ab34
   [334449.728482] usb 4-1: New USB device strings: Mfr=2, Product=3, SerialNumber=1
   [334449.728485] usb 4-1: Product: Backup+  Desk
   [334449.728487] usb 4-1: Manufacturer: Seagate
   [334449.728489] usb 4-1: SerialNumber: NA7H29DX
   [334449.749996] usbcore: registered new interface driver usb-storage
   [334449.752139] scsi host6: uas
   [334449.752539] scsi 6:0:0:0: Direct-Access     Seagate  Backup+  Desk    040B PQ: 0 ANSI: 6
   [334449.752586] usbcore: registered new interface driver uas
   [334449.768013] sd 6:0:0:0: [sdc] Spinning up disk...
   [334449.768023] sd 6:0:0:0: Attached scsi generic sg3 type 0

From this, you can see the logic volume is assigned to *sdc*. 

On RedHat/RHEL7, CentOS 7 and Scientific Linux 7 and possibly later versions of Linux flavors, the NTFS filesystem is directly supported. For older version of Linux, you might have to install *ntfs-3g* package first. Therefore, you can mount the volume easily with a mounting command as below.

.. code-block:: none

   $ sudo mount -t ntfs /dev/sdc2 /mnt

This command should not give you errors. After the command, you should be able to see the volume is mounted using `df` output

.. code-block:: none

   /dev/sdc2                   4883638268  1418392 4882219876   1% /mnt

and you should see a few more lines in `dmesg` output like this:

.. code-block:: none

   [334450.768547] ................ready
   [334465.784580] sd 6:0:0:0: [sdc] 9767541167 512-byte logical blocks: (5.00 TB/4.54 TiB)
   [334465.784585] sd 6:0:0:0: [sdc] 2048-byte physical blocks
   [334465.817288] sd 6:0:0:0: [sdc] Write Protect is off
   [334465.817294] sd 6:0:0:0: [sdc] Mode Sense: 4f 00 00 00
   [334465.817451] sd 6:0:0:0: [sdc] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
   [334466.214227]  sdc: sdc1 sdc2
   [334466.215286] sd 6:0:0:0: [sdc] Attached SCSI disk
   [334626.393838]  sdc: sdc1 sdc2

.. _image_condition:

... know the image conditions of the data collected on your system?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. From Frames.mdoc file. This is a metadata file to record all the conditions for each frame stack file collected. It contains the most 
   complete information inclduing total dose, stage positions, frame dose, frame numbers and navigator label for this exposure. 
   
2. From Setup.png - an image file. This is snapshot for Camera Setup Dialog window and with frame data setup window. This image shows 
   total dose, dose rate on camera, frame numbers, frame time etc.. 
   
3. From header. You can get header information for MRC and TIFF image stack by an IMOD program *header*:

.. code-block:: none

   $ header image-stack.mrc 



