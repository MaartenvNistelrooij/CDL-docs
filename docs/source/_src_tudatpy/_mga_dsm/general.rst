.. _`mga_dsm_general`:

MGA-DSM general information
========================================

Approach
----------------------------------------

The general approach taken in the design/analysis of MGA-DSM trajectories is to specify inputs and perform optimizations to
find good cases. These good cases are then analysed in more detail to finally choose one case and use its results for the
spacecraft design. This approach comes to light in the form of four files that can be handled in order and iteratively
to find good transfer trajectories, in terms of :math:`\Delta V` and time of flight (optimization) and communication time,
link budget and solar flux (analysis).

.. Implemented bodies
.. ----------------------------------------

.. Even though Tudat(Py) has many planets and moons implemented with default environment models, the CDL scripts were written
.. to facilitate working with the bodies specified below. These bodies have characteristics included in ``src/SolarSystemConstants.py``
.. and ``transfer_trajectory/transfer_trajectory_inputs.py`` for analysis purposes. Users are encouraged to add additional
.. bodies and their characteristics.

.. Implemented bodies:
.. * Sun
.. * Mercury
.. * Venus
.. * Earth
.. * Moon
.. * Mars
.. * Jupiter
.. * Saturn
.. * Uranus
.. * Neptune
.. * Pluto

