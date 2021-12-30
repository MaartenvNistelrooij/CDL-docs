.. _`tudatpy`:

Tudat(Py)
================

MGA-DSM transfers
-----------------------
Multiple Gravity Assist and Deep Space Maneuver (MGA-DSM) transfers are high thrust transfers that can be applied for
interplanetary travel. These transfers are defined by a series a nodes (planets), where gravity assists (GA) are applied,
connected by legs, the trajectories between the planets. Tudat(Py) provides the possibility to apply a maximum of 1 Deep
Space Maneuver (DSM), i.e. an impulsive :math:`\Delta V`, per leg.

The MGA-DSM scripts developed for the CDL are divided in an input script and three executables, which should be adapted and
executed in order and are explained in the referenced pages below. In addition, there are source files, which
contain helpful functions that are used in the executables. Thus, the user does not need to modify these, but can do so
to add functionality to the scripts. All these files can be downloaded from :ref:`downloads`.

Important background information about the analysis and design of MGA-DSM transfer trajectories within Tudat(Py) is
available on the Tudat-space documentation. Relevant pages are:

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
   :maxdepth: 1

   _mga_dsm/general
   _mga_dsm/inputs
   _mga_dsm/optimization
   _mga_dsm/analysis
   _mga_dsm/save_to_excel

Orbital phase
------------------

In many space missions, the orbital phase, where a spacecraft orbits a body many times, is when most of the science
return is obtained. The CDL Orbiter scipts can be used to analyse the applicability of different orbiter characteristics
and generate useful data that can be used to design a spacecraft capable of flying such missions. Relevant background
information for simulation the orbital dynamics of a spacecraft can be found in
`Tudat(Py) State propagation user guide <https://tudat-space.readthedocs.io/en/latest/_src_user_guide/state_propagation.html>`_.

.. toctree::
   :maxdepth: 1

   _orbiter/general
   _orbiter/inputs
   _orbiter/analysis
   _orbiter/save_to_excel
