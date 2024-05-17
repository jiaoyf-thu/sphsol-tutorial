I/O Files
=========

Input
-----

.. Note:: Modify the ``Input/`` files to customise your simulation scenario.

The initial states of SPH particles are defined in ``Input/0-Particles.csv``, formatted as

.. code-block::

  ID,X,Y,Z,VX,VY,VZ,PART,MASS,DENSITY
  0,0.0e0,0.0e0,0.0e0,0.0e0,0.0e0,0.0e0,0,0.0e0,0.0e0
  1,0.0e0,0.0e0,0.0e0,0.0e0,0.0e0,0.0e0,0,0.0e0,0.0e0
  ...

You can generate this file by modifying the example

Output
------
