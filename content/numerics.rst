.. _numerics:

Numerics
========

.. questions::

   - What is 
   - What problem do 

.. objectives::

   - Explain 

.. instructor-note::

   - 15 min teaching
   - 0 min exercises

Numerical schemes
-----------------

OpenFOAM includes a wide range of solution and scheme controls, specified via dictionary files in the case system sub-directory. These are described by:

    - Numerical schemes: Transforming partial differential equations to a linear system of equations using the Finite Volume Method. The treatment of each term in the system of equations is specified in the fvSchemes dictionary. This enables fine-grain control of e.g. temporal, gradient, divergence and interpolation schemes. Additional run-time selectable physical modelling and general finite terms are prescribed in the fvOptions dictionary, targeting e.g. acoustics, heat transfer, momentum sources, multi-region coupling, linearised sources/sinks and many more
    - Solution methods: Case solution parameters are specified in the fvSolution dictionary. These include choice of linear equation solver per field variable, algorithm controls e.g. number of inner and outer iterations and under-relaxation.



OpenFOAM applications are designed for use with unstructured meshes, offering up
to second order accuracy, predominantly using collocated variable arrangements.
Most focus on the Finite Volume Method, for which the conservative form
of the general scalar transport equation for the property  :math:`\phi`  takes the
form:

.. math::
   \frac{\partial \rho \phi }{\partial t} +  \nabla \cdot \left(\rho \phi \vec{u} \right) =  \nabla \cdot \left(\Gamma \nabla \phi \right) + S_\phi 



The Finite Volume Method requires the integration over a 3-D control volume,
such that:

.. math::
      \int_V \frac{\partial \rho \phi }{\partial t}  dV
    + \int_V \nabla \cdot \left(\rho \phi \vec{u} \right) dV
    = \int_V \nabla \cdot \left(\Gamma \nabla \phi \right) dV
    + \int_V S_\phi dV

Next step is to transform the volume integral to surface integral by using Gauss Thereom.

.. math::
      \int_V \frac{\partial \rho \phi }{\partial t}  \mathrm{d} V
    + \oint_S \left(\rho \phi \mathbf{u \cdot n} \right) \mathrm{d} S  
    = \oint_S \Gamma  (\mathbf{ \nabla \phi \cdot n})  \mathrm{d} V
    + \int_V S_\phi \mathrm{d} V


Up to this point, the integral form is valid for an arbitrary volume, and for each volume, the integral equations are valid.
In principle, as long as we are consistent in how we compute the surface integrals, it is conservative. 

Then we split the volume integrals across the volume faces/surfaces, note that 

 - Each volume is assumed to be a convex polyhedron
 - The value at each face centroid is approximated given the values at the centroids of the two cells sharing the face
 - To transform the quantity from the cell centre to the face centre, an interpolation scheme is required

.. math::
      \int_V \frac{\partial \rho \phi }{\partial t}  \mathrm{d} V
    + \sum_{faces} \left(\rho_f \phi_f \mathbf{u_f \cdot n_f} \right) S_f  
    = \sum_{faces} \Gamma  (\mathbf{ \nabla \phi \cdot n})  \mathrm{d} V
    + \int_V S_\phi \mathrm{d} V


.. math::
      \int_V \frac{\partial \rho \phi }{\partial t}  \mathrm{d} V
    + \oint\limits_{S(V)} \left(\rho \phi \mathbf{u \cdot n} \right) \mathrm{d} S  
    = \oint_S \Gamma  (\mathbf{ \nabla \phi \cdot n})  \mathrm{d} V
    + \int_V S_\phi \mathrm{d} V


Until now, a semi-discretised system of equations is obtained



This equation is discretised to produce a system of algebraic equations of the form

.. math::
    \begin{equation}
      \begin{pmatrix}
        a_{11} & a_{12} & \ldots  & a_{1n}  \\
        a_{21} & a_{22} & \ldots  & a_{2n}  \\
        \vdots & \vdots & \ddots & \vdots  \\
        a_{n1} & a_{n2} & \ldots  & a_{nn}
      \end{pmatrix}

\begin{pmatrix}
x_{1}  \\
        x_{2}  \\
        \vdots \\
        x_{n}
\end{pmatrix}



\begin{pmatrix}
    b_{1}  \\
        b_{2}  \\
        \vdots \\
        b_{n}
\end{pmatrix}
    \end{equation}
 

or more concisely:

.. math::
    A \vec{x} = \vec{b}


where:

:math:`A`
: coefficient matrix

:math:`\vec{x}`
: vector of unknowns

:math:`\vec{b}`
: source vector

The discretisation process employs user selected schemes to build the
A matrix and \vec{b} vector, described in the following
sections.  Choice of schemes are set in the
 "fvschemes"  dictionary.



Interpolation schemes
---------------------

Interpolation schemes are specified in the fvSchemes file under the interpolationSchemes sub-dictionary using the syntax:

.. tabs::

   .. tab:: InterpolationSchemes

      .. code-block:: txt

         interpolationSchemes
         {
             default         none;
             <equation term> <interpolation scheme>;
         }


A wide variety of interpolation schemes are available, ranging from those that are based solely on geometry, and others, e.g. convection schemes that are functions of the local flow:

   - Linear scheme : The most obvious option is linear interpolation, 2nd order accurate.  However, for convective fluxes it introduces oscillations
   - Convection scheme: many options for interpolating the  convective flux exist. Often it is the most important numerical choice in the simulation. Many of the convection schemes available in OpenFOAM are based on the TVD and NVD: 

        - NVD/TVD convection schemes::
         
            - Limited linear divergence scheme
            - Linear divergence scheme
            - Linear-upwind divergence scheme
            - MUSCL divergence scheme
            - Mid-point divergence scheme
            - Minmod divergence scheme
            - QUICK divergence scheme
            - UMIST divergence scheme
            - Upwind divergence scheme
            - Van Leer divergence scheme
         
        - Non-NVD/TVD convection schemes::

            - Courant number blended divergence scheme
            - DES hybrid divergence scheme
            - Filtered Linear (2) divergence scheme
            - LUST divergence scheme


Temporal schemes
----------------


Temporal schemes define how a field is integrated as a function of time. OpenFOAM includes a variety of schemes to integrate fields with respect to time:

Time scheme properties are input in the fvSchemes file under the ddtSchemes sub-dictionary using the syntax:

.. tabs::

   .. tab:: Time scheme properties

      .. code-block:: txt

         ddtSchemes
         {
             default         none;
             ddt(Q)          <time scheme>;
         }




Available **<time scheme>** include

    - Backward time scheme
    - Crank-Nicolson time scheme
    - Euler implicit time scheme
    - Local Euler implicit/explicit time scheme
    - Steady state time scheme


When choosing temporal scheme, here are a few things to consider:

 - Explicit or implicit: the latter means we have to solve a linear system at each time-step.
 - Order of accuracy
 - Numerical stability, and its implications for the time-step


Spatial schemes
---------------

At their core, spatial schemes rely heavily on interpolation schemes to transform cell-based quantities to cell faces, in combination with Gauss Theorem to convert volume integrals to surface integrals.

Gradient
++++++++

Gradient schemes are specified in the fvSchemes file under the gradSchemes sub-dictionary using the syntax:

.. tabs::

   .. tab:: gradSchemes

      .. code-block:: txt

            gradSchemes
            {
                default         none;
                grad(p)         <optional limiter> <gradient scheme> <interpolation scheme>;
            }


Gradient schemes

   - Gauss gradient scheme
   - Least-squares gradient scheme

Interpolation schemes

   - linear: cell-based linear
   - pointLinear: point-based linear
   - leastSquares: Least squares

Gradient limiters

The limited gradient schemes attempt to preserve the monotonicity condition by limiting the gradient to ensure that the extrapolated face value is bounded by the neighbouring cell values.

   - Cell-limited gradient scheme
   - Face-limited gradient scheme
   - Multi-directional cell-limited gradient scheme
   - Multi-directional face-limited gradient scheme
   - clippedLinear: limits linear scheme according to a hypothetical cell size ratio


Divergence
++++++++++

Divergence schemes are specified in the fvSchemes file under the divSchemes sub-dictionary using the general syntax:

.. tabs::

   .. tab:: Time scheme properties

      .. code-block:: txt

            divSchemes
            {
                default         none;
                div(Q)          Gauss <interpolation scheme>;
            }


A typical use is for convection schemes, which transport a property under the influence of a velocity field specified using:

.. tabs::
   .. tab:: divSchemes
      .. code-block:: txt
            divSchemes
            {
                default         none;
                div(phi,Q)      Gauss <interpolation scheme>;
            }

The phi keyword is typically used to represent the flux (flow) across cell faces, i.e.
https://doc.openfoam.com/2312/tools/processing/numerics/schemes/divergence/
- volumetric flux:
- mass flux:


NVD/TVD convection schemes

Many of the convection schemes available in OpenFOAM are based on the TVD and NVD [PROVIDE REF] For further information, see the page invalid item schemes-divergence-nvdtvd

    Limited linear divergence scheme
    Linear divergence scheme
    Linear-upwind divergence scheme
    MUSCL divergence scheme
    Mid-point divergence scheme
    Minmod divergence scheme
    QUICK divergence scheme
    UMIST divergence scheme
    Upwind divergence scheme
    Van Leer divergence scheme

Non-NVD/TVD convection schemes

    Courant number blended divergence scheme
    DES hybrid divergence scheme
    Filtered Linear (2) divergence scheme
    LUST divergence scheme



Laplacian
+++++++++

Laplacian schemes are specified in the fvSchemes file under the laplacianSchemes sub-dictionary using the syntax:

laplacianSchemes
{
    default         none;
    laplacian(gamma,phi) Gauss <interpolation scheme> <snGrad scheme>
}

All options are based on the application of Gauss theorem, requiring an interpolation scheme to transform coefficients from cell values to the faces, and a surface-normal gradient scheme.


SnGrad
++++++

Surface-normal gradient schemes are specified in the fvSchemesfile under the snGradSchemes sub-dictionary using the syntax:

snGradSchemes
{
    default         none;
    snGrad(Q)       <snGrad scheme>;
}

Options

    Corrected surface-normal gradient scheme
    Face-corrected surface-normal gradient scheme
    Limited surface-normal gradient scheme
    Orthogonal surface-normal gradient scheme
    Uncorrected surface-normal gradient scheme



OpenFOAM executables
--------------------

Unlike many other software, OpenFOAM does not have a unique executable. 
For every solver, mesh generation etc. there is a separate executable! 
You should run the right executable according to the solver you are
using!

- ‘simpleFoam’: if you use SIMPLE algorithm
- ‘icoFoam’: if you use PISO algorithm for laminar flow
- ...

Check the documentation to see recommended solvers for different cases


Pressure-velocity coupling

    Introduction: Pressure-velocity algorithms
    Steady state: SIMPLE
    Transient: PISO
    Transient: PIMPLE

Capability matrix




Summary
- fvOptions and functionObject practically remove the need for
modifying the solver, as long as it captures your physics.
- Lot’s of fvOptions and functionObjects out there. Try and play with
them during the hands on!
There is a coded type of fvOption and functionObject, which
allows you to simply write you own C++ to be executed! Will be
compiled when the case runs, with no involvment from your side.







OpenFOAM output files
• Similar to the input files, the output files are also in plain text
dictionary format



