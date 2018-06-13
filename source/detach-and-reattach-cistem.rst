.. _cisTEM_detach_reattach:

cisTEM detaching and re-attaching sessions
==========================================

:Author: Albert Xu
:Contact: <albert.t.xu@gmail.com>
:Date-created: 2018-06-13
:Last-updated: 2018-06-13

.. glossary::

   Abstract
      In cisTEM when you close the main window, all running jobs are terminated. Xpra is a window server that saves the state of graphical
      applications so that you can detach from your cisTEM session and have it run in the background.

      https://xpra.org/
      "Xpra is an open-source multi-platform persistent remote display server and client for forwarding applications and desktop screens.
      ... it allows you to run programs, usually on a remote host, direct their display to your local machine, and then to disconnect from
      these programs and reconnect from the same or another machine, without losing any state."


.. _install_Xpra:

Installing Xpra on Linux
------------------------

1. Download the Xpra repository information into your system's package manager folder.
         
   For our Centos 7 system,
         
.. code-block:: ruby
        
   sudo bash -c "curl https://xpra.org/repos/CentOS/xpra.repo > /etc/yum.repos.d"
        
Xpra also supports Fedora, Debian, and Ubuntu. For Debian and Ubuntu, the package manager folder is /etc/apt/sources.list.d

2. Install
      
For Centos and Fedora,
   
.. code-block:: ruby
   
   sudo yum install xpra
   
For Debian and Ubuntu,
   
.. code-block:: ruby
   
   sudo apt install xpra

.. _usage_examples:

Usage Examples
--------------

1. Log into the cisTEM computer from your remote computer. You will need less strict X forwarding with the -Y option.
        
.. code-block:: ruby
        
   ssh -Y username@ipaddress
   
2. Start a cisTEM process using xpra. You will need to choose a sessionID number. I arbitrarily chose 100.
   
.. code-block:: ruby
   
   xpra start :100 --start-child=cisTEM
         
Hit enter one more time, and now the session has been created.

3. Detach from the session. From the command line, hit Ctrl-C and the window will disappear. You can kill the local cisTEM X-window too. 


4. Attach to the session. From the cisTEM computer,
         
.. code-block:: ruby
         
   xpra attach :100
    
and the cisTEM window should open. If there is only one session, you don't need the :100

.. _note::

   when re-attaching: Usually remote connections from outside the local network are laggy. Fortunately, Xpra has compressions to lessen the amount of bandwidth. To enable compression when reattaching, do

.. code-block:: ruby

       xpra attach --encoding=rgb --compress=1

   This is the recommended way from Xpra.org.

