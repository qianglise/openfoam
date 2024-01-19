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


Intro
-----

OpenFOAM includes a wide range of solution and scheme controls, specified via dictionary files in the case system sub-directory. These are described by:

    - Numerical schemes: Transforming partial differential equations to a linear system of equations using the Finite Volume Method. The treatment of each term in the system of equations is specified in the fvSchemes dictionary. This enables fine-grain control of e.g. temporal, gradient, divergence and interpolation schemes. Additional run-time selectable physical modelling and general finite terms are prescribed in the fvOptions dictionary, targeting e.g. acoustics, heat transfer, momentum sources, multi-region coupling, linearised sources/sinks and many more
    - Solution methods: Case solution parameters are specified in the fvSolution dictionary. These include choice of linear equation solver per field variable, algorithm controls e.g. number of inner and outer iterations and under-relaxation.


Numerical schemes
-----------------

OpenFOAM applications are designed for use with unstructured meshes, offering up
to second order accuracy, predominantly using collocated variable arrangements.
Most focus on the Finite Volume Method, for which the conservative form
of the general scalar transport equation for the property $$ \phi $$ takes the
form:

.. math::
      \underbrace{\ddt{\rho \phi}}_{\mathrm{unsteady}}
    + \underbrace{\div \left(\rho \phi \u \right)}_{\mathrm{convection}}
    = \underbrace{\div \left(\Gamma \grad \phi \right)}_{\mathrm{diffusion}}
    + \underbrace{S_\phi}_{\mathrm{source}}


The Finite Volume Method requires the integration over a 3-D control volume,
such that:

.. math::
      \int_V \ddt{\rho \phi} dV
    + \int_V \div \left(\rho \phi \u \right) dV
    = \int_V \div \left(\Gamma \grad \phi \right) dV
    + \int_V S_\phi dV


This equation is discretised to produce a system of algebraic equations of the
form

.. math::
    \begin{bmatrix}
        a_{11} & a_{12} & \dots  & a_{1n}  \\
        a_{21} & a_{22} & \dots  & a_{2n}  \\
        \vdots & \vdots & \ddots & \vdots  \\
        a_{n1} & a_{n2} & \dots  & a_{nn}
    \end{bmatrix}
    \begin{bmatrix}
        x_{1}  \\
        x_{2}  \\
        \vdots \\
        x_{n}
    \end{bmatrix}
    =
    \begin{bmatrix}
        b_{1}  \\
        b_{2}  \\
        \vdots \\
        b_{n}
    \end{bmatrix}


or more concisely:

.. math::
    \mat{A} \vec{x} = \vec{b}


where:

$$\mat{A}$$
: coefficient matrix

$$\vec{x}$$
: vector of unknowns

$$\vec{b}$$
: source vector

The discretisation process employs user selected schemes to build the
$$\mat{A}$$ matrix and $$ \vec{b}$$ vector, described in the following
sections.  Choice of schemes are set in the
<%= link_to_menuid "fvschemes" %> dictionary.

## Temporal schemes {#time}

OpenFOAM includes a variety of schemes to integrate fields with respect to time:

- <%= link_to_menuid "schemes-time" %>

## Spatial schemes {#spatial}

At their core, spatial schemes rely heavily on
[interpolation schemes](ref_id://schemes-interpolation) to transform
cell-based quantities to cell faces, in combination with
[Gauss Theorem](ref_id://schemes-gausstheorem) to
convert volume integrals to surface integrals.

- <%= link_to_menuid "schemes-gradient" %>
- <%= link_to_menuid "schemes-divergence" %>
- <%= link_to_menuid "schemes-laplacian" %>
- <%= link_to_menuid "schemes-sngrad" %>

## Wall distance calculation method {#wall-distance}

Distance to the nearest wall is required, e.g. for a number of turbulence
models.  Several calculation methods are available:








Summary
- fvOptions and functionObject practically remove the need for
modifying the solver, as long as it captures your physics.
- Lot’s of fvOptions and functionObjects out there. Try and play with
them during the hands on!
There is a coded type of fvOption and functionObject, which
allows you to simply write you own C++ to be executed! Will be
compiled when the case runs, with no involvment from your side.







OpenFOAM executables
• Unlike many other software, OpenFOAM does not have a unique
executable. For every solver, mesh generation etc. there is a separate
executable!
• You should run the right executable according to the solver you are
using!
• ‘simpleFoam’: if you use SIMPLE algorithm
• ‘icoFoam’: if you use PISO algorithm for laminar flow
• ...
• Check the documentation to see recommended solvers for different cases


OpenFOAM output files
• Similar to the input files, the output files are also in plain text
dictionary format



