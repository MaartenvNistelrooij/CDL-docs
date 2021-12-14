
Welcome to Concurrent Design Lab documentation!
=================================================

Welcome to the Collaborative Design Lab (CDL) of the Aerospace Engineering (AE) Faculty of Delft University of Technology
(TU Delft). The CDL has been specifically set up and designed to ease collaborative work on multidisciplinary projects.
Good examples of such project within the AE curriculum are the Design Synthesis Exercise (DSE) and the Joint
Interdisciplinary Project (JIP). The CDL mimicks the concurrent design approach taken in industry.

This documentation will present sample scripts that have been developed specifically for multidisciplinary work within
the spacefligh domain. The documentation consists of two parts, which relate to two different software packages: CDP4
and TudatPy.

CDP4
-------
.. warning::

    TO DO: Short introduction to the Concurrent Design Platform 4
    TO DO: include CDP4 manual transcript


Tudat(Py)
-----------

The TU Delft Astrodynamics Toolbox (Tudat) is a powerful set of libraries that support astrodynamics and space research.
One of the key strengths within Tudat is its ability to combine such libraries in a powerful simulator framework. Such
framework can be used for a wide variety of purposes, ranging from the study of reentry dynamic to interplanetary missions.
The functionality of Tudat is implemented in C++, but a Python interface, called Tudatpy, is available, through
which the core simulation functionality can be accessed. Tudat and Tudatpy are disseminated as conda packages, to get
started on them, have a look at the Tudat(Py) `documentation`_  and `installation`_ guide.

.. _`documentation`: https://tudat-space.readthedocs.io/en/latest/index.html#
.. _`installation`: https://tudat-space.readthedocs.io/en/latest/_src_getting_started/installation.html

.. note::

    A CDL specific :download:`environment.yaml <_static/environment_cdl.yaml>` file was created, which contains all the packages required for the TudatPy scripts
    developed for the CLD. For beginners, it is advised to use this environment file, instead of the one that comes with
    the Tudat(Py) installation.

.. toctree::
   :maxdepth: 3
   :caption: CDP4
   :hidden:

   _src_cdp4/manual

.. toctree::
   :maxdepth: 3
   :caption: Tudat(Py)
   :hidden:

   _src_tudatpy/tudatpy

.. toctree::
   :maxdepth: 2
   :caption: Downloads
   :hidden:

   _src_downloads/downloads
