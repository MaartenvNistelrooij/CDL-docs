.. _`orbiter_general`:

Orbiter general information
========================================

Approach
----------------------------------------

The approach to analysing the orbital phase of a mission is similar to that of analysing MGA-DSM transfers, except for
not having an optimization. The user provides input parameters and analyzes the corresponding orbits. This is done with
as many different orbits as desired. When having analysed sufficient orbits, the user can select one case of which the
results are saved to Excel such that these can be used in the spacecraft design in CDP4.

.. Implemented bodies
.. ----------------------------------------

.. Even though Tudat(Py) has many planets and moons implemented with default environment models, the CDL scripts were written
.. to facilitate working with the bodies specified below. These bodies have characteristics included in ``src/SolarSystemConstants.py``
.. for analysis purposes. Users are encouraged to add additional bodies and their characteristics.

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