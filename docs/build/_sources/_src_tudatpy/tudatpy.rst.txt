.. _`tudatpy`:

Tudat(Py)
================

MGA-DSM transfers
-----------------------
Multiple Gravity Assist and Deep Space Maneuver (MGA-DSM) transfers are high thrust transfer that can be applied for
interplanetary travel. These transfers are defined by a series a nodes (planets), where gravity assists (GA) are applied,
connected by legs, the trajectories between the planets. Tudat(Py) provides the possibility to apply a maximum of 1 Deep
Space Maneuver (DSM), i.e. an impulsive :math:`\Delta V`, per leg.

The MGA-DSM scripts developed for the CDL are divided in an input script and three executables, which should be adapted and
executed in order and and are explained in the referenced pages below. In addition, there are source files, which
contain helpful functions that are used in the executables. Thus, the user does not need to modify these, but can do so
to add functionality to the scripts. All these files can be downloaded from the :ref:`downloads` section.

Important background information about the analysis and design of MGA-DSM transfer trajectories within Tudat(Py) is
available on the Tudat documentation. Relevant pages are:

* `Tudat(Py) MGA-DSM User Guide`_

* `Tudat(Py) MGA-DSM API`_

* `Tudat(Py) MGA-DSM Analysis example`_


.. _`Tudat(Py) MGA-DSM User Guide`: https://tudat-space.readthedocs.io/en/latest/_src_user_guide/astrodynamics.html
.. _`Tudat(Py) MGA-DSM API`: https://tudatpy.readthedocs.io/en/latest/transfer_trajectory.html
.. _`Tudat(Py) MGA-DSM Analysis example`: https://tudat-space.readthedocs.io/en/latest/_src_getting_started/examples.html


Only the critical parts considered in the above sources will be repeated here, but in general it will be assumed that
the user is familiar with their contents. The CDL scripts written for MGA-DSM transfer trajectories are explained in
the following sections:

.. toctree::
   :maxdepth: 3

   _mga_dsm/general
   _mga_dsm/inputs
   _mga_dsm/optimization
   _mga_dsm/analysis
   _mga_dsm/save_to_excel



Orbital phase
------------------

Work in progress, expected soon

.. toctree::
   :maxdepth: 3

   _orbiter/general
   _orbiter/inputs
   _orbiter/analysis
   _orbiter/save_to_excel

