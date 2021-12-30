.. _`mga_dsm_inputs`:

MGA-DSM Inputs
========================================

To start the design of an MGA-DSM transfer trajectory, it is required to specify all input parameters in
``transfer_trajectory/transfer_trajectory_inputs.py``. These inputs are explained in this section by first presenting
the code to define them and subsequently providing some additional remarks.

Trajectory inputs
----------------------------------------

First off, parameters defining the transfer trajectory are required.

.. code-block:: python

    transfer_body_orders = [['Earth', 'Venus', 'Venus', 'Earth', 'Jupiter', 'Saturn'],
                            ['Earth', 'Jupiter', 'Saturn']]

.. End of code block

``transfer_body_orders`` specifies the sequence in which planets are visited. Each planet is denoted with a string (with
capital first letter) in a list of lists. This enables the subsequent and automatic optimization of multiple planet
sequences later on.

.. note::

    If you only want to optimize one planet sequence, do make sure to keep the outer list: ``transfer_body_orders = [['Earth',
    'Venus', 'Venus', 'Earth', 'Jupiter', 'Saturn']]``

.. End of note

.. code-block:: python

    leg_type = transfer_trajectory.unpowered_unperturbed_leg_type

.. End of code block

``leg_type`` specifies whether the transfer will utilize DSMs or not. This is defined by a ``trajectory_design`` attribute
imported from Tudat.

| To not use DSMs: ``leg_type = trajectory_design.unpowered_unperturbed_leg_type``.
| To use DSMs: ``leg_type = trajectory_design.dsm_velocity_based_leg_type``.

.. warning::

    Even though Tudat(Py) has the ``DSM Position based`` leg type implemented, this option was not implemented in the CDL
    scripts. That is, the automatic generation of transfer parameter boundaries, as used in the optimization, was not
    implemented for this type.

.. End of warning

.. code-block:: python

    departure_date = (1171.64503236 - 0.5) * constants.JULIAN_DAY
    departure_date_margin = 180.0 * constants.JULIAN_DAY

.. End of code block

The ``departure_date`` is to be specified in J2000 (Julian seconds since 1 January 2000, 12:00 UTC). ``departure_date_margin``
indicates the time added and subtracted from the nominal departure date to define the departure date boundaries for the
optimization. In the above example this means that the departure will occur within half a year before and half a
year after the nominal date.

.. code-block:: python

    departure_semi_major_axis = np.inf              # Optional in Tudat(Py)
    departure_eccentricity = 0                      # Optional in Tudat(Py)
    arrival_semi_major_axis = 1.0895e8 / 0.02       # Optional in Tudat(Py)
    arrival_eccentricity = 0.98                     # Optional in Tudat(Py)

.. End of code block

Specifying the semi-major axis and eccentricity of the departure and insertion orbits is optional in Tudat(Py). In the
CDL code, default values are set to :math:`a=\infty` and :math:`e=0` through keyword arguments in the applicable
optimization and analysis functions. These values correspond to departure from / arrival at the edge of the sphere of
influence (SOI) of the departure / arrival  planet. In the above example, departure is defined from Earth's SOI and
arrival is in a highly eccentric orbit at Saturn.

.. code-block:: python

    spacecraft_name = "42"

Besides that it can be fun to personalise your analyses, a unique identifier is needed to define the spacecraft ephemeris
that are generated and used in the analysis of solar flux, link budget and available communications time. This is done
through ``spacecraft_name``.

Optimization settings
----------------------------------------

Secondly, some settings have to be specified to guide te optimization.


.. code-block:: python

    number_of_evolutions = 3000
    population_size = 500

.. End of code block

Good values for ``number_of_evolutions`` and ``population_size`` are heavily dependent on your problem definition,
i.e.  mostly on ``transfer_body_orders`` and ``leg_type``. In general, it is important to consider the number of parameters
in your problem before choosing these values. The more parameters there are to be optimized, the more computationally
expensive it is to do so efficiently and the larger the population size and the more evolutions are required to obtain
good results efficiently.

The number of parameters within a problem *without* DSMs is equal to the number of planets that are flown by (including the
departure and destination planet). In contrast, the number of parameters within a problem *with* DSMs is equal to the number
of planets that are flown by (including the departure and destination planet) *plus* four times the number of legs that are
flown. The number of parameters for a problem *with* DSMs is thus significantly larger than for one *without*. This more
elaborate problem definition makes it computationally more expensive to optimize.

The following table presents a good choice of optimization settings for two case studies along with their problem characteristics:

========  ================================================  ============================ ========================= ===================== =====================
  DSM?     Planet sequence                                   Number of parameters         Number of evolutions      Population size       Optimization runtime
========  ================================================  ============================ ========================= ===================== =====================
   No       Earth, Venus, Venus, Earth, Jupiter, Saturn      6                            3000                      500                    ~3 minutes
   Yes      Earth, Earth, Venus, Venus, Mercury              21                           2000                      2000                   ~15 minutes
========  ================================================  ============================ ========================= ===================== =====================

This shows that it is significantly more effective to increase the population size to optimize a more complex problem, than to perform
more evolutions.

Do note, for a problem *without* DSMs it is recommended to pick a large value for the number of generations and be
on the 'safe side' for the optimization, as the cost in runtime is not too large. This is not the case for a problem *with*
DSMs, where an evolution costs significantly more time.

.. code-block:: python

    optimization_seed = 4444                        # Optional
    maximum_delta_v = 2e8  # [m/s]                  # Such a high value makes it practically ineffective

.. End of code block

``optimization_seed`` and ``maximum_delta_v`` are helpful parameters in tweaking the optimization if it is doubted that
the best solutions are found. It is advised to change these parameters only after a few optimizations have been performed.
Therefore, more information on what these parameters do is presented in :ref:`mga_dsm_optimization`.

Communication system properties
----------------------------------------

Satellite antenna and ground station properties are required for the calculation of the link budget and available
communication time in the analysis of a transfer trajectory.

.. code-block:: python

    transmitted_power = 27                         # W
    transmitter_antenna_gain = 10                  # -
    receiver_antenna_gain = 1                      # -
    frequency = 1.57542e9                          # Hz
    minimum_elevation = 10 * np.pi / 180           # rad
    gs_latitude = 52.0115769 * np.pi / 180         # rad
    gs_longitude = 4.3570677 * np.pi / 180         # rad
    station_name = 'Delft'

.. End of code block

``transmitted_power`` and ``transmitter_antenna_gain`` are characteristics of the satellite antenna.
``receiver_antenna_gain`` is a characteristic of the ground station antenna. ``frequency`` indicates the frequency at
which the satellite and ground station communicate, which affects the propagation of the radio waves. ``minimum_elevation``
defines the minimum elevation at which the satellite must be with respect to the ground station to enable communication.
``gs_latitude`` and ``gs_longitude`` define the geographical location of the ground station on Earth.


Save results
------------------------------

.. code-block:: python

    save_optimization_plots = True
    save_analysis_plots = True

.. End of code block

Data of the optimization and analysis is automatically saved to text files. The user does have the option to choose
whether plots are saved as well. For an optimization there is only the Pareto front that can be saved. For the analysis
there is plots of communication time per day, link budget and solar flux as a function of time, and the trajectory in x-y
plane. Of course, the user is free to add plots to his/her needs.


Time of flight boundaries
---------------------------------

Lastly, the user can define the boundaries for the time of flight parameters in the optimization. It is advised to only
change these if the optimization is not providing good results. That is, tuning these parameters is meant to tweak the
optimization. As such, more information on how to best change these boundaries is provided in :ref:`mga_dsm_optimization`.

.. code-block:: python

    MINIMUM_TIME_OF_FLIGHT_DICT = {
        'Mercury': 100,
        'Venus': 100,
        'Earth': 50,
        'Moon': 0.1,
        'Mars': 100,
        'Jupiter': 800,
        'Saturn': 3000,
        'Uranus': 1500,
        'Neptune': 3000,
        'Pluto': 6000
    }                                           # days

    MAXIMUM_TIME_OF_FLIGHT_DICT = {
        'Mercury': 1000,
        'Venus': 1000,
        'Earth': 1000,
        'Moon': 1000,
        'Mars': 1000,
        'Jupiter': 4000,
        'Saturn': 8000,
        'Uranus': 15000,
        'Neptune': 30000,
        'Pluto': 50000
    }                                           # days

.. End of code block

The ``MINIMUM_TIME_OF_FLIGHT_DICT`` and ``MAXIMUM_TIME_OF_FLIGHT_DICT`` dictionaries provide the lower and upper
boundaries, respectively, for the time of flight *towards* the body that is provided by the *key* of a *value*.
As an examle, if the spacecraft is to go from Venus to Earth, the lower and upper bounds are given by the entry
``'Earth'`` in the dictionary and are 50 and 1000 days respectively. The example above has been set up for trajectories
away from Earth and changes are definitely required when a return mission is considered.