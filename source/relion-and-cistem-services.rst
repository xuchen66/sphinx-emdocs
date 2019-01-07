.. _Relion-and-cisTEM-services:

Relion and cisTEM Docker Services
=================================

:Author: Albert Xu
:Contact: <albert.t.xu@gmail.com>
:Date-created: 2018-06-13
:Last-updated: 2018-06-13

.. glossary::

   Abstract
      This is a quick information how to use Relion3 and cisTEM dockers on two Linux workstations in CryoEM Core Facility. 
      One of the potential advantages to use docker is to avoid CUDA version conflict.  


.. _relion3:

Relions
-------

1. on Eagle
         
.. code-block:: ruby
        
   ssh -Y relion3@172.18.8.143 -p 30003
   password = *******
   
2. on Falcon 

.. code-block:: ruby
        
  ssh -Y relion3@172.18.8.136 -p 30003
  password = *******
        

.. _cistem:

cisTEM
------

1. on Eagle
         
.. code-block:: ruby
        
   ssh -Y cisTEM@172.18.8.143 -p 30001
   password = *******
   
2. on Falcon 

.. code-block:: ruby
        
  ssh -Y cisTEM@172.18.8.136 -p 30001
  password = *******


.. Note::

  Let other people know if your are using either eagle or falcon. For each machine there is only one account 
  for Relion3 and one for cisTEM.

