.. _`tudatpy`:

Tudat(Py)
================

MGA-DSM transfers
-----------------------
Multiple Gravity Assist and Deep Space Maneuver (MGA-DSM) transfers are high thrust transfer that can be applied for
interplanetary travel. These transfers are defined by a series a nodes (planets), where gravity assists (GA) are applied,
connected by legs, the trajectories between the planets. Tudat(Py) provides the possibility to apply a maximum of 1 Deep
Space Maneuver (DSM), i.e. a high :math:`\Delta V`, per leg. The MGA-DSM scripts developed for the CDL are divided in
input script and 3 executables, which should be adapted and executed in order and and are explained in the referenced
pages below, and a couple of source files. All these files can be downloaded from the :ref:`downloads` section. However,
before diving into this code, it is advised to familiarize yourself first with the content of the `MGA-DSM User Guide`_.

.. _`MGA-DSM User Guide`: https://tudat-space.readthedocs.io/en/latest/_src_user_guide.html


*   :ref:`mga_dsm_inputs`

*   :ref:`mga_dsm_optimization`

*   :ref:`mga_dsm_analysis`

*   :ref:`mga_dsm_save_to_excel`


Orbital phase
------------------
