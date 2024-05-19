Theory
======

SPH Approximation
-----------------

The key idea of the smoothed particle hydrodynamics (SPH) method is to approximate any field :math:`f(x)` using a smoothing kernel :math:`W(r)`,

.. math:: \left \langle f(x) \right \rangle = \int_{\Omega} f(x')W(x-x',h)dx'

where :math:`h` is the smoothing length.

Replacing the integral with the sum over all particles, the SPH interpolant is discretized as,

.. math:: \left \langle f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) W_{ij}

Similarly, the gradient can be approximated as,

.. math:: \left \langle \nabla \cdot f(x_i) \right \rangle = \sum_j \frac{m_j}{\rho_j} f(x_j) \cdot \nabla_i W_{ij}

The cubic B-spline M4 function has been widely used as the smoothing kernel in the astrophsical SPH community, which is also adopted in our code,

.. math:: W(r) = \frac{1}{6} \alpha_d \times \left\{\begin{align} & 4-6r^2+3r^3, & 0\leq r<1 \\ & (2-r)^3, & 1\leq r \leq 2\\ & 0, & r>2 \end{align}\right.

where :math:`r=\left \| x_i-x_j \right \| /h`, and :math:`\alpha_d=3/(2\pi h^3)` in the 3-dimensional case.

Lagrange Equations
------------------

The Lagrange equations describle the conservation of mass, momentum, and energy,

.. math:: 

  \left\{\begin{align}
    & \dot{\rho} + \rho(\nabla\cdot v) = 0 \\
    & \dot{v} = \frac{1}{\rho} (\nabla \cdot \sigma) +g \\
    & \dot{e} = \frac{1}{\rho} (\sigma : \dot{\epsilon})
  \end{align}\right.

where :math:`\sigma=-pI+s` is the stress tensor, and :math:`\dot{\epsilon}=\tfrac{1}{2} (\nabla v + (\nabla v)^\top)` is the strain rate.

The equations are then discretized into SPH formulation as,

.. math:: 

  \left\{\begin{align}
    & \frac{\mathrm{d} \rho_i}{\mathrm{d} t} = \rho_i \sum_j \frac{m_j}{\rho_j} (v_i-v_j) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} v_i}{\mathrm{d} t} = \sum_j m_j \left(\frac{\sigma_i}{\rho^2_i} + \frac{\sigma_j}{\rho^2_j} -
      \Pi_{ij}I + \zeta_{ij} \right) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} e_i}{\mathrm{d} t} = \frac{1}{2} \sum_j m_j \left(\frac{p_i}{\rho^2_i} + \frac{p_j}{\rho^2_j} + \Pi_{ij} \right) 
      v_{ij} \cdot \nabla_i W_{ij} + \frac{1}{\rho_i} s_i:\dot{\epsilon}_i
  \end{align}\right.

where :math:`\Pi_{ij}` and :math:`\zeta_{ij}` are artificial terms.

Artificial Terms
----------------

The standard artificial viscosity :math:`\Pi_{ij}` is introduced to handle shocks in SPH, such as particle interpenetration and unphysical oscillations in the pressure field after the shock wave passes,

.. math::

  \Pi_{ij} = \left\{\begin{align}
    & \frac{-\alpha_\Pi \bar{c}_{ij} \phi_{ij} + \beta_\Pi \phi^2_{ij}}{\bar{\rho}_{ij}} , & v_{ij} \cdot x_{ij} \leq 0 \\
    & 0, & v_{ij} \cdot x_{ij} \leq 0 \end{align}\right.

where

.. math:: \phi_{ij} = \frac{\bar{h}_{ij}v_{ij} \cdot x_{ij}}{\left\| x_{ij} \right\|^2 + 0.1 \bar{h}^2_{ij}}

The artificial stress :math:`\zeta_{ij}` is also implemented to suppress the possible tensile instability that leads to numerical clumping of particles,

.. math:: \zeta_{ij} = (\zeta_i+\zeta_j) \left(\frac{W(x_{ij})}{W(\Delta x)}\right)^4

where :math:`\Delta x` is the mean particle spacing, and the tensor :math:`\zeta_i` represents a local measure of tension in the principal axes of :math:`\sigma_i`,

.. math:: \zeta_i = -0.2\frac{1}{\rho^2_i} U \mathrm{diag}\left(\max(\sigma_i^A,0), \max(\sigma_i^B,0), \max(\sigma_i^C,0)\right) U^\top

with the eigendecomposition of :math:`\sigma_i` as :math:`U\mathrm{diag}(\sigma_i^A,\sigma_i^B,\sigma_i^C)U^\top`.

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
