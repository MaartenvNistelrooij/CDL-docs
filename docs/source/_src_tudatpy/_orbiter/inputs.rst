.. _`orbiter_inputs`:

Orbiter inputs
====================

The inputs required for the analysis of an orbiter are defined in ``orbiter/orbiter_inputs.py``. These inputs are
explained in this section by first presenting the code to define them and subsequently providing some additional remarks.

.. code-block:: python

    save_analysis_plots == True

.. End of code-block

Data of the analysis is automatically saved to text files. The user does have the option to choose whether plots are
saved as well. For the orbiter analysis, there is plots of the Line of Sight to both a Ground Station and Relay Satellite,
Day-or-Night side, Eclipses, Ground Track and Revisit time that can be saved. Of course, the user is free to add plots
to his/her needs.

.. code-block:: python

    central_body = 'Mars'
    spacecraft_name = 'If found, please return to Earth'
    vehicle_mass = 2000                                         # kg
    radiation_pressure_coefficient = 1.2                                # set to None if not applicable
    reference_area = 50                                         # m^2   # set to None if not applicable

.. End of code-block

``central_body`` defines the body around which the spacecraft is to orbit. ``spacecraft_name`` is used as a unique
identifier to save the spacecraft ephemeris after propagation, such that they can be easily retrieved for the analysis
of various characteristics. ``vehicle_mass`` is specified in kilograms and affects how the spacecraft is perturbed due
to various interactions. ``radiation_pressure_coefficient`` and ``reference_area`` are optional inputs if one wants to
simulate the solar radiation pressure acceleration on the spacecraft. If not, they should be set to ``None``.

.. code-block:: python

    simulation_start_epoch = 5000.35 * constants.JULIAN_DAY
    simulation_time = 1.5 * constants.JULIAN_DAY

.. End of code-block

``simulation_start_epoch`` is to be specified in J2000 (Julian seconds since 1 January 2000, 12:00 UTC).
``simulation_time`` defines the propagation time in seconds.

.. code-block:: python

    semi_major_axis = 4000e3                # m
    eccentricity = 0.01
    inclination = 0.0 * np.pi/180.          # radians
    raan = 0.0 * np.pi/180.                 # radians
    aop = 0.0 * np.pi/180.                  # radians
    tr_anom = 0.0 * np.pi/180.              # radians

.. End of code-block

The Kepler elements are used to define the initial state of the spacecraft with respect to the central body. Note that
this is with respect to the ecliptic plane and not with respect to the equatorial plane of the planet.

.. code-block:: python

    minimum_elevation = 10 * np.pi / 180
    gs_latitude = 52.0115769 * np.pi / 180
    gs_longitude = 4.3570677 * np.pi / 180
    gs_name = 'Delft'

.. End of code-block

A ground station should be defined for which the Line of Sight to the spacecraft is computed. ``minimum_elevation``
defines the minimum elevation at which the satellite must be with respect to the ground station to enable communication.
``gs_latitude`` and ``gs_longitude`` define the geographical location of the ground station.

.. code-block:: python

    rs_central_body = "Earth"
    rs_name = "RelaySat"

    rs_semi_major_axis = 42164.0e3          # m
    rs_eccentricity = 0.0
    rs_inclination = 0.0 * np.pi/180.       # radians
    rs_raan = 0.0 * np.pi/180.              # radians
    rs_aop = 0.0 * np.pi/180.               # radians
    rs_tr_anom = 0.0 * np.pi/180.           # radians

.. End of code-block

A Relay Satellite (RS) can be used for communication purposes. This satellite is propagated with the same integrator and
propagator settings as the main spacecraft. The RS Kepler Elements define its initial state with respect to
``rs_central_body``. ``rs_name`` is used to define the ephemeris and allows for their retrieval when computing the Line
of Sight.

.. code-block:: python

    field_of_view = 5.                      # degrees

.. End of code-block

The spacecraft instrument field of view determines the swath width on the target planet that can be observed. This angle
is used in the computation of the ground coverage characteristics.

.. code-block:: python

    acceleration_settings_on_vehicle = {"Mars": [propagation_setup.acceleration.spherical_harmonic_gravity(4,0)],
                                        "Earth": [propagation_setup.acceleration.point_mass_gravity()],
                                        "Jupiter": [propagation_setup.acceleration.point_mass_gravity()],
                                        "Saturn": [propagation_setup.acceleration.point_mass_gravity()],
                                        "Sun": [propagation_setup.acceleration.point_mass_gravity(),
                                                propagation_setup.acceleration.cannonball_radiation_pressure()]
                                        }

.. End of code-block

The user can select the perturbations that affect the spacecrafts dynamics. This is done through the
``accelerations_settings_on_vehicle`` dictionary, which is used in the scripts to create the acceleration models. Each
body exerting one or more perturbations should be a key in the dictionary. The perturbations exerted by this body are
included as a list corresponding to the body. The perturbations have to be retrieved from the Tudat's propagation setup
module. More information on defining perturbations can be found in `Acceleration Model Setup`_. When no acceleration
settings are defined (i.e. ``acceleration_settings_on_vehicle = None``), the default is that only the point mass gravity
acceleration of the central body is exerted on the spacecraft.

.. _`Acceleration Model Setup`: https://tudat-space.readthedocs.io/en/latest/_src_user_guide/state_propagation/propagation_setup/acceleration_models/setup.html?highlight=acceleration_settings

.. code-block:: python

    dependent_variables_to_save = [propagation_setup.dependent_variable.altitude(spacecraft_name, central_body),                                # DO NOT CHANGE
                                   propagation_setup.dependent_variable.central_body_fixed_spherical_position(spacecraft_name, central_body),   # DO NOT CHANGE
                                   propagation_setup.dependent_variable.keplerian_state(spacecraft_name, central_body),
                                   propagation_setup.dependent_variable.relative_distance(spacecraft_name, central_body),
                                   propagation_setup.dependent_variable.relative_distance(spacecraft_name, "Earth"),
                                   propagation_setup.dependent_variable.relative_distance(spacecraft_name, "Sun")
                                   ]

.. End of code-block

Lastly, variables can be defined that are saved during the propagations. Again, these should be retrieved from Tudat's
propagation setup module. These so called dependent variables are written to text files after the propagation and can be
used for any analysis. Here, it is important to note that the current first two entries should *NOT* be changed. That is,
``altitude`` and ``central_body_fixed_spherical_positions`` are variables used in the computation of ground coverage
parameters in the analysis of the orbiter's ground coverage characteristics. All dependent variables that can be saved
can be found in `Dependent Variable API`_.

.. _`Dependent Variable API`: https://tudatpy.readthedocs.io/en/latest/dependent_variable.html#functions