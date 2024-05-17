Quick Start
===========

These instructions will get you a copy of the project for development and testing purposes.

Prerequisites
-------------

GCC, CMake and Eigen are necessary to run SPHSOL on a Linux computer.

.. code-block::

  sudo apt-get update
  sudo apt-get install build-essential gdb cmake libeigen3-dev

Optionally, some Python packages are required to run ``example.py``.

.. code-block::

pip install numpy pandas scipy open3d

Build & Run
-----------

.. code-block::

  git clone https://github.com/jiaoyf-thu/sphsol.git
  cd SPHSOL
  mkdir build && cd build
  cmake ../ && make
  ./sphsol
