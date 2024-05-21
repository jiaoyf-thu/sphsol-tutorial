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

where :math:`\sigma=-pI+s` is the stress tensor, and :math:`\dot{\epsilon}=\tfrac{1}{2} ((\nabla v)^\top + \nabla v)` is the strain rate.

The equations are then discretized into SPH formulations,

.. math:: 

  \left\{\begin{align}
    & \frac{\mathrm{d} \rho_i}{\mathrm{d} t} = \rho_i \sum_j \frac{m_j}{\rho_j} (v_{ij}) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} v_i}{\mathrm{d} t} = \sum_j m_j \left(\frac{\sigma_i}{\rho^2_i} + \frac{\sigma_j}{\rho^2_j} -
      \Pi_{ij}I + \zeta_{ij} \right) \cdot \nabla_i W_{ij} \\
    & \frac{\mathrm{d} e_i}{\mathrm{d} t} = \frac{1}{2} \sum_j m_j \left(\frac{p_i}{\rho^2_i} + \frac{p_j}{\rho^2_j} + \Pi_{ij} \right) 
      v_{ij} \cdot \nabla_i W_{ij} + \frac{1}{\rho_i} s_i:\dot{\epsilon}_i
  \end{align}\right.

where :math:`v_{ij}=v_i-v_j`. :math:`\Pi_{ij}` and :math:`\zeta_{ij}` are artificial terms.

Artificial Terms
----------------

Artificial Viscosity
^^^^^^^^^^^^^^^^^^^^

The standard artificial viscosity :math:`\Pi_{ij}` is introduced to handle shocks in SPH, such as particle interpenetration and unphysical oscillations in the pressure field after the shock wave passes,

.. math::

  \Pi_{ij} = \left\{\begin{align}
    & \frac{-\alpha_\Pi \bar{c}_{ij} \phi_{ij} + \beta_\Pi \phi^2_{ij}}{\bar{\rho}_{ij}} , & v_{ij} \cdot x_{ij} < 0 \\
    & 0, & v_{ij} \cdot x_{ij} \geq 0 \end{align}\right.

where

.. math:: \phi_{ij} = \frac{\bar{h}_{ij}v_{ij} \cdot x_{ij}}{\left\| x_{ij} \right\|^2 + 0.1 \bar{h}^2_{ij}}

Artificial Stress
^^^^^^^^^^^^^^^^^

The artificial stress :math:`\zeta_{ij}` is implemented to suppress the possible tensile instability that leads to numerical clumping of particles,

.. math:: \zeta_{ij} = (\zeta_i+\zeta_j) \left(\frac{W(x_{ij})}{W(\Delta x)}\right)^4

where :math:`\Delta x` is the mean particle spacing, and the tensor :math:`\zeta_i` represents a local measure of tension in principal axes of the stress :math:`\sigma_i`,

.. math:: \zeta_i = -0.2\frac{1}{\rho^2_i} U \mathrm{diag}\left(\max(\sigma_i^A,0), \max(\sigma_i^B,0), \max(\sigma_i^C,0)\right) U^\top

with the eigendecomposition of :math:`\sigma_i` as :math:`U\mathrm{diag}(\sigma_i^A,\sigma_i^B,\sigma_i^C)U^\top`.

XSPH
^^^^

The XSPH term is optional to apply an averaged speed smoothed by the velocity of neighbor particles. The equation of motion is then modified as,

.. math:: \frac{\mathrm{d} x_i}{\mathrm{d} t} = v_i - 0.5 \sum_j \frac{m_j}{\bar{\rho}_{ij}} v_{ij} W_{ij}

Nothe that the linear and angular momentum are still conserved exactly when using the XSPH correction.

Strength Model
--------------

The elastic perfectly plastic mdoel is used the update the deviatoric stress tensor :math:`s`. The Hooke's law describes the linear elastic behavior of solid materials as,

.. math:: \frac{\mathrm{d} s}{\mathrm{d} t} = 2G\left( \dot{\epsilon} - \frac{1}{3}\mathrm{Tr}(\dot{\epsilon}I) \right) + s\dot{R}^\top + \dot{R}s

where :math:`G` is the shear modulus, :math:`\dot{R}=\tfrac{1}{2} ((\nabla v)^\top - \nabla v)` is the rotation rate. The yielding criterion is then introduced to model plasticity, with the deviatoric stress limited by, 

.. math:: s'=fs

Von Mises Yield Criterion
^^^^^^^^^^^^^^^^^^^^^^^^^

In the simple Von Mises yield criterion, the factor :math:`f` is computed as 

.. math:: f=\min\left(Y_0/\sqrt{\tfrac{3}{2}s:s}, 1\right)

where :math:`Y_0` is the material yield strengh.

Drucker-Prager Yield Criterion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A more general pressure-dependent yield criterion is to use the Drucker-Prager model. And the yield strength is given by,

.. math:: 

  \left\{\begin{align}  & Y_i=Y_0+\frac{\mu_i p}{1+\mu_i p/(Y_m-Y_0)} \\ & Y_d=\mu_d p \end{align}\right.

where :math:`Y_0` is the cohesion at zero pressure, :math:`Y_m` is the strength limit, and :math:`\mu` is the coefficient of internal friction. The subscripts ``i`` and ``d`` here denote intact and damaged respectively. For partially damaged material, the yield strength is interpolated according to the scalar damage :math:`D`,

.. math:: Y=(1-D)Y_i+DY_d

Damage and Fragmentation
------------------------

The Grady-Kipp scalar damage :math:`D\in[0,1]` is introduced to represent the degree of fragmentation, with :math:`D=0` being intact, and :math:`D=1` being fully fractured. The failure of solid material leads to a reduction of its strength in tension and shear deformation. The pressure and deviatoric stress are then modified by,

.. math:: \left\{\begin{align} & p' = p>0 \ ?\ p:(1-D)p \\ & s'=(1-D)s \end{align}\right.

The Weibull distribution is commonly used to describe the number of flaws per unit volume with  activation threshold lower than :math:`\epsilon`, following :math:`n(\epsilon)=k\epsilon^m`. With the particle strain measured as a scalar,

.. math:: \epsilon=\frac{max(\sigma^A,\sigma^B,\sigma^C)}{(1-D)E}

where :math:`E` is the elastic modulus, the damage growth is obtained by,

.. math:: \frac{\mathrm{d} D^{1/3}_i}{\mathrm{d} t} = n_{\mathrm{act}} \frac{c_g}{R_i}

where :math:`n_{\mathrm{act}}` is the number of active flaws inside the particle, :math:`c_g=0.4c_s` is the speed of crack growth, and :math:`R_i` is the particle radius.

The fracture area :math:`A_i` is integrated until fully damaged,

.. math:: \frac{\mathrm{d} A_i}{\mathrm{d} t} = \frac{(m+2)(m+3)}{2c_g} \left(\frac{8\pi c_g^3 k}{(m+1)(m+2)(m+3)}\right)^{2/3} \epsilon^{2m/3}_i D^{1/3}_i

The most frequent :math:`L_m` and the largest :math:`L_\max` of the fragment size distribution are calculated as

.. math:: L_m=\frac{3(m+3)}{(m+2)}\frac{1}{A_i} ,\ L_\max=\frac{m+2}{3}L_m

The cumulative number of fragments larger than a given size :math:`L` inside a particle is then,

.. math::

  \begin{align} F_{\mathrm{cum}}^i(L) = & \frac{(m+6)(m+5)(m+4)}{120}\frac{V_{\mathrm{cell}}}{L_{\max}^3}\left(1-\frac{L}{L_{\max}}\right)^m\left[1+m\left(\frac{L}{L_{\max}}\right)\right. \\
    & \left.+\frac{m(m+1)}{2}\left(\frac{L}{L_{\max}}\right)^2+\frac{m(m+1)(m+2)}{6}\left(\frac{L}{L_{\max}}\right)^3\right]
  \end{align}

Equation of State
-----------------

Neighbor Search
---------------

Self Gravity
------------

Time Integration
----------------
