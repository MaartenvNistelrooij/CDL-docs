.. _`mga_dsm_analysis`:

MGA-DSM Analysis
========================================

In the following section the analysis of MGA-DSM trajectories is explained. The code is structures as follows:
(1) selection of the individuals from the generated Pareto front (see :ref:`mga_dsm_optimization`) to be further analysed;
(2) creation of transfer trajectory object; (3) analysis of transfer trajectory.

Selection of trajectory settings and creations of transfer trajectory
---------------------------------------------------------------------------

The selection of the transfer trajectories to be analysed is done through the
``get_user_input_for_analysis(...)`` and ``get_trajectory_parameters_of_desired_solutions(...)`` functions.
These functions first prompt the user to select which of the of the optimized transfer body orders will be
analysed. Second, for the selected transfer body order, the user is asked to select
the transfer trajectory or trajectories to analyse; this can be done by selecting either the :math:`\Delta V`
or time of flight of the solutions of interest. Based on these inputs, the script retrieves the set of trajectory
parameters that define each of the trajectories to be further analysed.

Next the transfer trajectory needs to be created. This is done following exactly the same logic as described in
`Tudat(Py) MGA-DSM Analysis example <https://tudat-space.readthedocs.io/en/latest/_src_getting_started/_src_examples/mga_dsm_examples/mga_dsm_analysis.html>`_.
First, the system of bodies to be used in the transfer trajectory is created:

.. code-block:: python

    # Create system of bodies
    bodies = environment_setup.create_simplified_system_of_bodies()
.. End of code block

Second, the transfer trajectory is created and evaluated. Here, that step is done by simply calling the following
function:

.. code-block:: python

    # Create transfer trajectory and evaluate it
    transfer_trajectory = TransferTrajectoryAnalysis.create_and_evaluate_transfer_trajectory(
        bodies,
        leg_type,
        transfer_body_order,
        trajectory_parameters,
        departure_semi_major_axis,
        departure_eccentricity,
        arrival_semi_major_axis,
        arrival_eccentricity)
.. End of code block

All the inputs to the ``create_and_evaluate_transfer_trajectory(...)`` function are either retrieved
by the script based on the selection of the transfer trajectories to analyse, or manually specified
by the user in the input file (see :ref:`mga_dsm_inputs`).

Calculation of analysis data
--------------------------------------------------------

Having selected the trajectory parameters, it is possible to run various functions to further analyse the
trajectory. All the generated data is saved to text files, located in directories named according to
the :math:`\Delta V` and time of flight of the analysed trajectory. As mentioned in :ref:`mga_dsm_inputs`, the
user can select whether the data should also be plotted.

Retrieving :math:`\Delta V` and time of flight
****************************************************************

The :math:`\Delta V`, :math:`\Delta V` per node (i.e. departure :math:`\Delta V`, :math:`\Delta V` per
gravity-assist and insertion :math:`\Delta V`), :math:`\Delta V` per leg
(i.e. per DSM) and time of flight are retrieved via:

.. code-block:: python

    # Delta V [m/s]
    delta_v = transfer_trajectory.delta_v
    # Delta V per node [m/s]
    delta_v_per_node = transfer_trajectory.delta_v_per_node
    # Delta V per leg [m/s]
    delta_v_per_leg = transfer_trajectory.delta_v_per_leg
    # Time of flight [s]
    time_of_flight = transfer_trajectory.time_of_flight
.. End of code block

State history
****************************************************************

The state history and time history can be retrieved using the ``get_state_history()`` function, through

.. code-block:: python

    # State history with respect to the Sun
    state_history_wrt_sun, time_history = TransferTrajectoryAnalysis.get_state_history(
        transfer_trajectory,
        bodies,
        reference_body = 'Sun',               # Optional: default value is 'Sun'
        states_per_leg = STATES_PER_LEG       # Optional: default value is STATES_PER_LEG = 500
    )
.. End of code block

The ``reference_body`` argument allows selecting the body with respect to which the state history will be
retrieved; by default it is assumed to be the central body (i.e. the Sun).

.. note::
    The functions to analyse the state history assume that the used set of ``bodies`` includes a body corresponding
    to the spacecraft, for which ephemeris are defined. As such, after retrieving the state history, one needs
    to create the spacecraft's ephemeris, which can be done through

    .. code-block:: python

        # Create spacecraft object in bodies, and define its ephemeris
        bodies.create_empty_body(spacecraft_name)
        TrajectoryAnalysis.define_vehicle_ephemeris(
            bodies,
            state_history_wrt_sun,
            time_history,
            spacecraft_name)
    .. End of code block

Solar flux
****************************************************************

The total incident solar flux is retrieved through:

.. code-block:: python

    # Retrieve total solar flux
    total_solar_flux_history = TransferTrajectoryAnalysis.total_solar_flux(
        bodies,
        time_history,
        spacecraft_name)
.. End of code block

Do note that the total solar flux does not take into account the angle of the incident solar radiation, only the
distance between the Sun and the spacecraft.
The values of the solar flux are retrieved at the time stamps selected through the ``time_history`` argument.

Link budget
****************************************************************

The link budget can be retrieved using the ``link_budget()`` function. It requires the definition of the
frequency of the signal,
power of the transmitter antenna,
gain of the transmitter antenna and
gain of the receiver antenna.

.. code-block:: python

    # Retrieve link budget
    link_budget_history = TransferTrajectoryAnalysis.link_budget(
        bodies,
        time_history,
        spacecraft_name,
        frequency,
        transmitted_power,
        transmitter_antenna_gain,
        receiver_antenna_gain,
        reference_body = 'Earth'         # Optional: default value is 'Earth'
    )
.. End of code block

The values of the link budget are retrieved at the time stamps selected through the ``time_history`` argument.
The link budget is calculated with respect to the body specified through the ``reference_body`` argument.

Communications time per day
****************************************************************

To calculate the time available for communications per day it is first necessary to define a
ground station, through the ``add_ground_station_simple()`` function.
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

Next, one can retrieve the time available for communications per day using the ``communications_time_per_day()``
function.
This function requires as input the name of the ground station being used and the minimum elevation from which
communications with the spacecraft are possible.
When checking the line of sight to the ground station, only the minimum elevation as seen from the ground
station is taken into account (i.e. occultations produced by bodies other than the ``ground_station_body`` are
not verified).

.. code-block:: python

    # Retrieve communications time per day
    comms_time_per_day = TransferTrajectoryAnalysis.communications_time_per_day(
            bodies,
            time_history,
            spacecraft_name,
            station_name,
            minimum_elevation,
            ground_station_body = 'Earth'         # Optional: default value is 'Earth'
    )
.. End of code block

The values of the communications time are retrieved at the time stamps selected through the ``time_history``
argument.