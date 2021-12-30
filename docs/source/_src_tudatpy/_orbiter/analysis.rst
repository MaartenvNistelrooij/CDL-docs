.. _`orbiter_analysis`:

Orbiter analysis
====================

In the following sections the analysis of an orbiter's trajectory is explained. The code (``orbiter/orbiter_analysis.py``)
is structures as follows: (1) selection of the propagation settings and execution of propagation, and (2) generation of
data for analysis of trajectory.

Orbit propagation
---------------------------------------------------------------------------

The orbit propagation set up and propagation itself are executed using functions designed specifically for the CDL,
so as to simplify this process as much as possible. Nevertheless, these follow the same procedure described in
`Tudat(Py) State propagation user guide <https://tudat-space.readthedocs.io/en/latest/_src_user_guide/state_propagation.html>`_.

First, the system of bodies to be used in the propagation is created through

.. code-block:: python

    # Create system of bodies
    bodies = Orbiter.create_system_of_bodies(
        central_body,
        vehicle_mass,
        spacecraft_name,
        radiation_pressure_coefficient,      # Optional: only needed when using solar radiation pressure as perturbation
        reference_area,                      # Optional: only needed when using solar radiation pressure as perturbation
        acceleration_settings_on_vehicle,    # Optional: as default no perturbations are used (two-body problem)
        rs_name,                             # Optional: only needed to assess line of sight to a relay satellite
        rs_central_body                      # Optional: only needed to assess line of sight to a relay satellite
    )
.. End of code block

This function creates the necessary bodies according to the perturbations specified in the input file
(see :ref:`orbiter_inputs`). To use the solar radiation pressure as perturbation, it is necessary to provide the
radiation pressure coefficient and reference area as inputs to the function. To include a relay satellite and its
central body in the system of bodies (for later checking the line of sight), it is necessary to provide the relay
satellite name and name of its central body as inputs to the function.

Next, the integrator and propagator settings are created through

.. code-block:: python

    # Create integrator and propagator settings
    integrator_settings, propagator_settings = Orbiter.create_integrator_and_propagator_settings(
        bodies,
        central_body,
        simulation_start_epoch,
        simulation_end_epoch,
        initial_state_cartesian,
        spacecraft_name,
        acceleration_settings_on_vehicle,    # Optional: as default no perturbations are used (two-body problem)
        dependent_variables_to_save          # Optional: as default no dependent variables are returned
    )
.. End of code block

The previous function creates the integrator and propagator settings according to the specified inputs. A Cowell propagator is
used, and RKDP8(7) as integrator with 10^-8 tolerance (both absolute and relative).

Finally, one can propagate the spacecraft's trajectory through

.. code-block:: python

    # Create ephemeris for spacecraft
    environment_setup.add_empty_tabulated_ephemeris(bodies, spacecraft_name)
    # Propagate trajectory of spacecraft
    dynamics_simulator = numerical_simulation.SingleArcSimulator(
        bodies,
        integrator_settings,
        propagator_settings,
        set_integrated_result=True,
        print_dependent_variable_data=True)
.. End of code block

.. note::

    Spacecraft's ephemeris are created with ``add_empty_tabulated_ephemeris(...)`` and then its value is defined
    after the propagation, by setting ``set_integrated_result=True``. This is done because the functions to analyse the
    trajectory assume that the set of ``bodies`` includes a body corresponding to the spacecraft, for which ephemeris
    are defined. For this purpose it is required to set the ``spacecraft_name`` as a unique identifier.

Calculation of analysis data
---------------------------------------------------------------------------

Having propagated the orbiter's trajectory, it is possible to run various functions to further analyse it.
All the generated data is saved to text files. As mentioned in :ref:`orbiter_inputs`, the
user can select whether the data is also plotted.

State and dependent variables history
****************************************************************

The state history and dependent variables history can be retrieved from

.. code-block:: python

    # Retrieve state history and dependent variables history as numpy arrays
    state_history = result2array(dynamics_simulator.state_history)
    dependent_variables_history = result2array(dynamics_simulator.dependent_variable_history)

    # Rename variables history for simplicity
    time_history = state_history[:, 0]
    state_values_history = state_history[:, 1:]
    dependent_variables_values_history = dependent_variables_history[:, 1:]
.. End of code block

For simplicity, in the provided scripts, the state and dependent variables history are converted to numpy arrays and sliced.

Line of sight to ground station
****************************************************************

To check the line of sight, one first defines a ground station, through the ``add_ground_station_simple(...)`` function.
The ground station is defined by the body it is located on, by its latitude and by its longitude; the body is
assumed to be spherical.
By default, the body where the ground station is located is considered to be the Earth, but other options
may be selected.

.. code-block:: python

    # Add ground station
    TrajectoryAnalysis.add_ground_station_simple(
            bodies,
            station_name,
            gs_latitude,
            gs_longitude,
            ground_station_body = 'Earth'         # Optional: default value is 'Earth'
    )
.. End of code block

Next, it is possible to retrieve the line of sight history with respect to the ground station, through the
``line_of_sight_to_ground_station(...)`` function:

.. code-block:: python

    # Retrieve line of sight
    los_to_gs_history = Orbiter.line_of_sight_to_ground_station(
        bodies,
        time_history,
        spacecraft_name,
        central_body,
        gs_name,
        minimum_elevation,
        ground_station_body = 'Earth'         # Optional: default value is 'Earth'
    )
.. End of code block

To evaluate whether there is line of sight, this function assesses
whether the elevation as seen from the ground station is larger than its minimum allowed value, and whether there are
occultations produced by the body where the ground station is located or by the body being orbited by the spacecraft.
The function returns ``1`` when there is line of sight, and ``0`` when there is no line of sight.

The values of the line of sight are retrieved at the time stamps selected through the ``time_history``
argument.

Line of sight to relay satellite
****************************************************************

To check the line of sight to a relay satellite it is first necessary to propagate its orbit, which follows the
same logic as the propagation of the orbiter (presented above). The following steps are required:

* Creation of integrator and propagator settings, with ``create_integrator_and_propagator_settings(...)``
* Creation of empty ephemeris for relay satellite, with ``add_empty_tabulated_ephemeris(...)``
* Propagation of relay satellite's orbit, with ``SingleArcSimulator(...)``

Next, it is possible to retrieve the line of sight history with respect to the relay satellite through

.. code-block:: python

    # Retrieve line of sight
    los_to_rs_history = Orbiter.line_of_sight_to_relay_satellite(
        bodies,
        time_history,
        central_body,
        spacecraft_name,
        rs_central_body,
        rs_name
    )
.. End of code block

To evaluate whether there is line of sight, this function assesses
whether there are occultations produced by the body orbited by the spacecraft or by the body orbited by the relay satellite.
The function returns ``1`` when there is line of sight, and ``0`` when there is no line of sight.

The values of the line of sight are retrieved at the time stamps selected through the ``time_history``
argument.

Day or night side of central body
****************************************************************

It is possible to check whether the sub-satellite point is in the day or night side of the central planet
(useful for the calculation of the albedo) through

.. code-block:: python

    # Day or night side
    day_night_values_history = Orbiter.planet_day_night_side(
        bodies,
        time_history,
        spacecraft_name)

.. End of code block

It is assumed that there is day in half the planet, and night in the other half. The function returns ``1`` when the
spacecraft is in the day side and ``0`` when the spacecraft is in the night side. These values are retrieved
at the time stamps selected through the ``time_history`` argument.

Eclipsing with respect to the Sun
****************************************************************

The occurrence of eclipses with respect to the Sun can be checked using

.. code-block:: python

    # Eclipses
    eclipses_history = Orbiter.eclipses_wrt_sun(
        bodies,
        time_history,
        spacecraft_name,
        central_body)
.. End of code block

The function returns ``1`` when the spacecraft is in full light, ``0`` when in umbra and a value between ``0`` and
``1`` when in penumbra. These values are retrieved at the time stamps selected through the ``time_history``
argument.

Analysis of ground coverage
****************************************************************

Ground coverage characteristics are useful to judge the potential science return of a mission. The general approach
taken here is to divide the central body's surface into a grid of pixels, defined by there longitude and latitude. Then,
the geographical track of the propagated spacecraft is used to identify observed pixels on a regular longitude-latitude
grid. In addition, it will identify the revisit time if a pixel is observed multiple times throughout the propagation.
The swath width is considered for each observed pixel by combining the field of view angle and the spacecraft's instantaneous altitude.

First, the altitude and geographical location of the spacecraft are taken from the dependent variables:

.. code-block:: python

    altitude = dependent_variables_values_history[:, 0]
    latitude = dependent_variables_values_history[:, 2] * 180 / np.pi
    longitude = dependent_variables_values_history[:, 3] * 180 / np.pi

.. End of code-block

The altitude and field of view are used to compute the swath width, which in turns is converted to a longitude range
of observed pixels, which we called ``swath_angle``. In the same function these arrays are split in subsections at the
minimum and maximum observed latitudes as well as at the 180th Meridian:

.. code-block:: python

    longitude_split, latitude_split, time_history_split, swath_angle_split = \
            Orbiter.split_arrays_at_min_max_latitude_and_180_meridian(central_body, altitude, longitude,
                                                                                 latitude, time_history, field_of_view)

.. End of code-block

Each section of ``longitude_split``, ``time_history_split`` and ``swath_angle_split`` is subsequently interpolated to a
regular latitude grid, such that each observation is separated by 0.5 degrees latitude. Here, the minimum and maximum
latitudes are used to define the end points for the regular latitude grid:

.. code-block:: python

    lat_min = latitude.min()
    lat_max = latitude.max()
    latitude_interpolated_split, longitude_interpolated_split, time_history_interpolated_split, swath_angle_interpolated_split = \
            Orbiter.interpolate_lonlat_sections(latitude_split, longitude_split, time_history_split, swath_angle_split,
                                                lat_min, lat_max)

.. End of code-block

Finally, the interpolated data is used to compute which  pixels are observed and what the revisit times are. This is done
by looping over each entry in the interpolated data, such that each sub-satellite point is considered, one by one.
The swath angle corresponding to a specific sub-satellite point is used to identify the range of longitudes that is
observed along with each sub-satellite point. These pixels are then updated in terms of number of visits and the revisit time.
Having walked through all sub-satellite points, the mean revisit time is computed per pixel from the total time between
visits and the total number of visits. Finally, the number of pixels that are observed at least once is used to compute
the percentage of the surface that is observed:

.. code-block:: python

   lon_grid, lat_grid, ground_track_grid, number_of_visitations_grid, shortest_revisit_time_grid, \
        mean_revisit_time_grid, longest_revisit_time_grid, percentage_surface_covered = \
        Orbiter.ground_coverage_parameters(latitude_interpolated_split, longitude_interpolated_split,
                                           time_history_interpolated_split, swath_angle_interpolated_split,
                                           simulation_time, semi_major_axis, central_body_gravitational_parameter)

.. End of code-block

The minimum and maximum observed latitudes, as well as the percentage of the surface that is observed, are written to
the console. In addition, these, along with the other computed ground coverage characteristics, are saved in text files.
By default, if plots are saved, plots are made of the ground track (pixels that are visited at least once) and the mean
revisit time. Note, if a pixel is not observed throughout the complete propagation, its revisit time will equal the
``simulation_time``. Of course, the user free to add their own plots.

By default, each pixel in the grid has a resolution of 0.5x0.5 degrees. This can be improved without too much computational
expense, except for plotting, which may become much slower.

Saved files
---------------------

The results of this analysis are saved to a subdirectory within the directory where the executable scripts are located.
The name of this subdirectory is ``Orbiter_i``, where ``i`` is a counter and increases by 1 for every orbiter that is analysed.
Each subdirectory contains a file containing the inputs that were used. The file structure is shown below. Users are
encouraged to design and implement their own ways of structuring these files. For example, if one is analysing the effect
of different initial states, it could be insightful to put the values of the Kepler elements in the subdirectory names.

.. figure:: _static/file_structure_orbiter_analysis.png
