.. _mesh:

Mesh
====

.. questions::

   - What is 
   - What problem do 

.. objectives::

   - Explain 

.. instructor-note::

   - 15 min teaching
   - 0 min exercises


Meshing
-------

Mesh generation
~~~~~~~~~~~~~~~

There are a couple of mesher available:

- blockMesh – Block-structured hexahedral mesher
- snappyHexMesh – Unstructured hexa-dominated mesher
- cfMesh – Unstructured mesher with different available meshing strategies
- makeFaMesh - Create finite-area meshes from volume-mesh patches
- Other commercial mesh generation

blockMesh
+++++++++

blockMesh is a structured hexahedral mesh generator.

Key features:

    structured hex mesh
    built using blocks
    supports cell size grading
    supports curved block edges

Constraints:

    requires consistent block-to-block connectivity
    ordering of points is important

Well suited to simple geometries that can be described by a few blocks, but challenging to apply to cases with a large number of blocks due to book-keeping requirements, i.e. the need to manage point connectivity and ordering.

Command line usage:

blockMesh [OPTIONS]

The utility is controlled using a blockMeshDict dictionary, located in the case system directory, split into the following sections:

    points
    edges
    blocks
    patches


Mesh fully defined in one dictionary: blockMeshDict. Lives in system.
Manually define everything: vertices, blocks, curved edges, boundaries.

an example dictionary file ? 
e,g, file:///home/qiang/Downloads/day2_meshing.pdf


snappyHexMesh
+++++++++++++

snappyHexMesh is a fully parallel, split hex, mesh generator that guarantees a minimum mesh quality. Controlled using OpenFOAM dictionaries, it is particularly well suited to batch driven operation.

Key features:

    starts from any pure hex mesh (structured or unstructured)
    reads geometry in triangulated formats, e.g. in stl, obj, vtk
    no limit on the number of input surfaces
    can use simple analytically-defined geometry, e.g. box, sphere, cone
    generates prismatic layers
    scales well when meshing in parallel
    can work with dirty surfaces, i.e. non-watertight surfaces

Meshing controls are set in the snappyHexMeshDict located in the case system directory. This has five main sections, described by the following:

    Geometry: specification of the input surfaces
    Castellation: starting from any pure hex mesh, refine and optionally load balance when running in parallel. The refinement is specified both according to surfaces, volumes and gaps
    Snapping: guaranteed mesh quality whilst morphing to geometric surfaces and features
    Layers: prismatic layers are inserted by shrinking an existing mesh and creating an infill, subject to the same mesh quality constraints
    Mesh quality: mesh quality settings enforced during the snapping and layer addition phases
    Global


Creating the mesh is a 3-step process.
1. Castellation
2. Snapping
3. Adding layers


The overall meshing process is summarised by the figure below:
https://doc.openfoam.com/2312/tools/pre-processing/mesh/generation/snappyhexmesh/figures/snappyHexMesh-overview-small.png

This includes:

    creation of the background mesh using the blockMesh utility (or any other hexahedral mesh generator)
    extraction of features on the surfaces with surfaceFeatureExtract utility
    setting up the snappyHexMeshDict input dictionary
    running snappyHexMesh in serial or parallel


Running snappyHexMesh will produce a separate directory for each step of the meshing process. The mesh in constant will be intact.
Run snappyHexMesh –overwrite to write only the final mesh directly to constant


Mesh manipulation
~~~~~~~~~~~~~~~~~

The following tools are useful when manipulating the mesh, e.g. scaling the geometry, identifying patches and creating sets and zones for physical models and post-processing.

    surfaceTransformPoints
    topoSet


Mesh conversion
~~~~~~~~~~~~~~~


Conversion

    ccmToFoam
    fireToFoam
    fluentMeshToFoam, fluent3DMeshToFoam
    gmshToFoam
  

Conclusions
• OpenFOAM has several meshing tools, suitable for both simple
and complex geometries.
• It’s possible to do a lot with snappy, including industrial flows.
• That being said, it seems to take a lot of parameter tweeking and
one has to know the tool well.
• I have heard from many that cfMesh is less painful to work with.
Try that as well.
• Generally, speciallized commercial meshers are still quite a bit
better in my opinion.
