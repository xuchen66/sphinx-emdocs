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
      K2 computer is not that hard. This is partially because K2 computer is running a server operating system, it is Windows 2008 
      R2 server.
      
      In this doc, I list what I have done to setup email notification on our SerialEM system. 
      
.. _setup_smtp_relay:

Setup SMTP and Relay Service 
----------------------------

I followed instructions in an on-line article `Setup and Configure SMTP Server on Windows Server 2008 R2 
<http://www.vsysad.com/2012/04/setup-and-configure-smtp-server-on-windows-server-2008-r2/>`_. It has images for each step and very easy to 
follow. 

