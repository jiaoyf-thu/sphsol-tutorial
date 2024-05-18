Theory
======

Smoothed Particle Hydrodynamics
-------------------------------

SPH Approximation
^^^^^^^^^^^^^^^^^

The key idea of smoothed particle hydrodynamics (SPH) method is to approximate any field :math:`f(x)` using a smoothing kernel :math:`W(r)`

.. math:: \left \langle f(x) \right \rangle = \int_{\Omega} f(x')W(x-x',h)dx'

where :math:`h` is the smoothing length.


Lagrange Equations
^^^^^^^^^^^^^^^^^^

Artificial Terms
^^^^^^^^^^^^^^^^

Neighbor Search
^^^^^^^^^^^^^^^

Time Integration
^^^^^^^^^^^^^^^^

Physical Models
---------------

Strength Model
^^^^^^^^^^^^^^

Equation of State
^^^^^^^^^^^^^^^^^

Damage Model
^^^^^^^^^^^^

Self Gravity
^^^^^^^^^^^^
