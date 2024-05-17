Quick Start
===========

These instructions will get you a copy of the project for development and testing purposes.

Prerequisites
-------------

GCC, CMake and Eigen are necessary to run SPHSOL on a Linux computer.

.. code-block::

  sudo apt-get update
  sudo apt-get install build-essential gdb cmake libeigen3-dev

Optionally, you may require some Python packages to run ``example.py``.

.. code-block::

  pip3 install numpy pandas scipy open3d

Build & Run
-----------

Get the code and run your first simulation.

.. code-block::

  git clone https://github.com/jiaoyf-thu/sphsol.git
  cd sphsol

.. code-block::

  python3 PrePost/example/test.py

.. code-block::

  mkdir build && cd build/
  cmake ../ && make
  ./sphsol
