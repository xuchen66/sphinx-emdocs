.. _SerialEM_note_setup_email:

SerialEM Note: Setup Email on K2 Computer
=========================================

:Author: Chen Xu
:Contact: <chen.xu@umassmed.edu>
:Created: 2018-05-05 
:Updated: 2018-05-05

.. glossary::

   Abstract
      SerialEM can send email; this feature has been there for a long time. I did not feel this was a must have so did not pay much
      attention to it. Now, we run scope 24/7 and like to get notification when something is wrong so we won't lose too much time. 
      
      Although setting up a standalone email server might not be the easist thing, setting up a SMTP server and relay service on 
      K2 computer is not that hard. This is partially because K2 computer is running a server operating system - Windows 2008 R2.
      
      In this doc, I list what I have done to setup email notification on our SerialEM system. 
      
.. _setup_smtp_relay:

Setup SMTP and Relay Service 
----------------------------

I followed instructions in an on-line article `Setup and Configure SMTP Server on Windows Server 2008 R2 
<http://www.vsysad.com/2012/04/setup-and-configure-smtp-server-on-windows-server-2008-r2/>`_. It has images for each step and very easy to 
follow. 

.. _on_serialem_part:

On SerialEM Part
----------------

There are a few steps we have to do on SerialEM side.

1. add two property lines in property file:

.. code:: Ruby 

   # SMTPserver
   SMTPServer		127.0.0.1
   SendMailFrom	admin@talos-k2.cryoem.umassmed.edu
   
2. define email address to receive the notification. This from Tilt Series Menu - Set Email Address.
3. insert a command line in main collection script for single partile data collection. Location of this line doesn't matter. 
 
.. code:: Ruby

   ErrorBoxSendEmail Script Stopped!
  
4. check "Send email at end" in Acquire at points ... dialog window. 
 
 
