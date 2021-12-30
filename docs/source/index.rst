
Welcome to Collaborative Design Lab documentation!
=======================================================

Welcome to the Collaborative Design Lab (CDL) of the Aerospace Engineering (AE) Faculty of Delft University of Technology
(TU Delft). The CDL has been specifically set up and designed to ease collaborative work on multidisciplinary projects.
Good examples of such project within the AE curriculum are the Design Synthesis Exercise (DSE) and the Joint
Interdisciplinary Project (JIP). The CDL mimicks the concurrent design approach taken in industry.

This documentation will present sample scripts that have been developed specifically for multidisciplinary work within
the spaceflight domain. The documentation consists of two parts, which relate to two different software packages: CDP4
and TudatPy.

CDP4
-------

The Concurrent Design Platform 4 (CDP4) is a tool that is used widely in industry to work concurrently on multidisciplinary
projects, such as space missions. It allows the easy integration of various subsystems of a spacecraft. A CDP4 Excel template
was developed that can be used for a wide variety of space missions. This chapter explains how to use this template:

.. toctree::
   :maxdepth: 1

   _src_cdp4/general

Tudat(Py)
-----------

The TU Delft Astrodynamics Toolbox (Tudat) is a powerful set of libraries that support astrodynamics and space research.
One of the key strengths within Tudat is its ability to combine such libraries in a powerful simulator framework. Such
framework can be used for a wide variety of purposes, ranging from the study of reentry dynamics to interplanetary missions.
The functionality of Tudat is implemented in C++, but a Python interface, called Tudatpy, is available, through
which the core simulation functionality can be accessed. Tudat and Tudatpy are disseminated as conda packages, to get
started on them, have a look at the Tudat(Py) `documentation`_  and `installation`_ guide.

.. _`documentation`: https://tudat-space.readthedocs.io/en/latest/index.html#
.. _`installation`: https://tudat-space.readthedocs.io/en/latest/_src_getting_started/installation.html

.. note::

    A CDL specific :download:`environment.yaml <_static/environment_cdl.yaml>` file was created, which contains all the packages required for the TudatPy scripts
    developed for the CDL. For beginner Tudat(Py) users, it is advised to use this environment file, instead of the one that comes with
    the Tudat(Py) installation.

.. toctree::
   :maxdepth: 1

   _src_tudatpy/tudatpy


Templates and scripts
---------------------------

Both for CDP4 and Tudat(Py), templates and example scripts have been developed that are ready for use. These templates
and script can  be found on the download page:

.. toctree::
   :maxdepth: 1

   _src_downloads/downloads
