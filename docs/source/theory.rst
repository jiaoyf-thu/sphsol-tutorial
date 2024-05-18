Theory
======

SPH Approximation
-----------------

The key idea of the smoothed particle hydrodynamics (SPH) method is to approximate any field :math:`f(x)` using a smoothing kernel :math:`W(r)`,

.. math:: \left \langle f(x) \right \rangle = \int_{\Omega} f(x')W(x-x',h)dx'

where :math:`h` is the smoothing length. Replacing the integral with the sum over all particles, the SPH interpolant is discretized as,

.. math:: \left \langle f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) W_{ij}

Similarly, the gradient can be expressed as,

.. math:: \left \langle \nabla \cdot f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) \cdot \nabla_i W_{ij}

The cubic B-spline M4 function has been widely used as the smoothing kernel in the astrophsical community, which is also adopted in our code,

.. math:: W(r) = \frac{1}{6} \alpha_d \times \left\{\begin{align} & 4-6r^2+3r^3, & 0\leq r<1 \\ & (2-r)^3, & 1\leq r \leq 2\\ & 0, & r>2 \end{align}\right.

where :math:`r=\left \| x_i-x_j \right \| /h`, and :math:`\alpha_d=1/h,15/7\pi h^2,3/2\pi h^3` for dimensions from 1 to 3.

Lagrange Equations
------------------

The Lagrange equations describle the conservation of mass, momentum, and energy,

.. math:: 

  \left\{\begin{align}
    & \dot{\rho} + \rho(\nabla\cdot v) = 0 \\
    & \dot{v} = \frac{1}{\rho} (\nabla \cdot \sigma) +g \\
    & \dot{e} = \frac{1}{\rho} (\sigma : \dot{\epsilon})
  \end{align}\right.

where :math:`\sigma=-pI+s` is the stress tensor, :math:`\dot{\epsilon}=\tfrac{1}{2} (\nabla v + (\nabla v)^\top)` is the strain rate. The equations are then discretized into SPH formulation,

.. math:: 

  \left\{\begin{align}
    & \frac{\mathrm{d} \rho_i}{\mathrm{d} t} = \rho_i \sum_j \frac{m_j}{\rho_j} (v_i-v_j) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} v_i}{\mathrm{d} t} = \sum_j m_j \left(\frac{\sigma_i}{\rho^2_i} + \frac{\sigma_j}{\rho^2_j} -
      {\color{blue}\Pi_{ij}}I \right) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} e_i}{\mathrm{d} t} = \frac{1}{2} \sum_j m_j \left(\frac{p_i}{\rho^2_i} + \frac{p_j}{\rho^2_j} + {\color{blue}\Pi_{ij}} \right) 
      v_{ij} \cdot \nabla_i W_{ij} + \frac{1}{\rho_i} s_i:\dot{\epsilon}_i
  \end{align}\right.

where :math:`\Pi_{ij}` is the artificial viscosity term.

Artificial Terms
----------------

The standard artificial viscosity :math:`\Pi_{ij}` is introduced to handle shocks in SPH, such as particle interpenetration and unphysical oscillations in the pressure field after the shock wave passes,

.. math::

  \Pi_{ij} = \left\{\begin{align}
    & \frac{-\alpha_\Pi \bar{c}_{ij} \phi_{ij} + \beta_\Pi \phi^2_{ij}}{\bar{\rho}_{ij}} , & v_{ij} \cdot x_{ij} \leq 0 \\
    & 0, & v_{ij} \cdot x_{ij} \leq 0 \end{align}\right.

where

.. math:: \phi_{ij} = \frac{\bar{h}_{ij}v_{ij} \cdot x_{ij}}{\left\| x_{ij} \right\|^2 + 0.1 \bar{h}^2_{ij}}

The artificial stress can be implemented to suppress the possible tensile instability

Strength Model
--------------

Equation of State
-----------------

Damage Model
------------

Neighbor Search
---------------

Self Gravity
------------

Time Integration
----------------
