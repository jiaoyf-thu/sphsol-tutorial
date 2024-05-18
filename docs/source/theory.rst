Theory
======

Smoothed Particle Hydrodynamics
-------------------------------

SPH Approximation
^^^^^^^^^^^^^^^^^

The key idea of the smoothed particle hydrodynamics (SPH) method is to approximate any field :math:`f(x)` using a smoothing kernel :math:`W(r)`,

.. math:: \left \langle f(x) \right \rangle = \int_{\Omega} f(x')W(x-x',h)dx'

where :math:`h` is the smoothing length. Replacing the integral with the sum over all particles, the SPH interpolant is discretized as,

.. math:: \left \langle f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) W_{ij}

Similarly, the gradient can be expressed as,

.. math:: \left \langle \nabla \cdot f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) \cdot \nabla_i W_{ij}

The cubic B-spline M4 function has been widely used as the smoothing kernel in the astrophsical community, which is also adopted in our code,

.. math:: W(r) = \frac{1}{6} \alpha_d \times \begin{cases} 4-6r^2+3r^3, & 0\leq r<1 \\ (2-r)^3, & 1\leq r \leq 2\\ 0, & r>2 \end{cases}

where :math:`r=\left \| x_i-x_j \right \| /h`, and :math:`\alpha_d=1/h,15/7\pi h^2,3/2\pi h^3` for dimensions from 1 to 3.

Lagrange Equations
^^^^^^^^^^^^^^^^^^

The Lagrange equations describle the conservation of mass, momentum, and energy.

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
