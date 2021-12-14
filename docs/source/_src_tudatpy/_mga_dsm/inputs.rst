.. _`mga_dsm_inputs`:

MGA-DSM Inputs
========================================

Trajectory inputs
--------------------------

.. code-block:: python
    transfer_body_orders = [['Earth', 'Venus', 'Venus', 'Earth', 'Jupiter', 'Saturn'],
                            ['Earth', 'Jupiter', 'Saturn']]

``transfer_body_orders`` specifies the sequence in which planets are visited. Each planet is denoted with a string (with
capital first letter) in a list of lists. This enables the subsequent and automatic optimization of multiple planet
sequences later on.

.. note::
    If you only want to optimize one planet sequence, do make sure to keep the outer list: ``transfer_body_orders = [['Earth',
    'Venus', 'Venus', 'Earth', 'Jupiter', 'Saturn']]``
.. End of note

.. code-block:: python

    leg_type = transfer_trajectory.unpowered_unperturbed_leg_type

``leg_type`` specifies whether the transfer will utilize DSMs or not. This is denoted by a ``trajectory_design`` attribute
imported from Tudat.

| To not use DSMs: ``leg_type = trajectory_design.unpowered_unperturbed_leg_type``.
| To use DSMs: ``leg_type = trajectory_design.dsm_velocity_based_leg_type``.

.. warning::

    Even though Tudat(Py) has the DSM Position based leg type implemented, this option was not implemented in the CDL
    scripts. That is, the automatic generation of transfer parameter boundaries, as used in the optimization, was not
    implemented for this type.

.. code-block:: python

    departure_date = (1171.64503236 - 0.5) * constants.JULIAN_DAY
    departure_date_margin = 180.0 * constants.JULIAN_DAY

The ``departure_date`` is to be specified in J2000 (Julian days since 1 January 2000, 12:00 UTC). ``departure_date_margin``
indicates the time added and subtracted from the nominal departure date to define the departure time boundaries for the
optimization. In the above example this means that the departure will occur within half a year before and half a
year after the nominal date.

.. code-block:: python

    departure_semi_major_axis = np.inf              # Optional in Tudat(Py)
    departure_eccentricity = 0                      # Optional in Tudat(Py)
    arrival_semi_major_axis = 1.0895e8 / 0.02       # Optional in Tudat(Py)
    arrival_eccentricity = 0.98                     # Optional in Tudat(Py)

Specifying the semi-major axis and eccentricity of the departure and insertion orbits is optional in Tudat(Py). These
values correspond to departure from / arrival at the sphere of influence (SOI) of the departure / arrival  planet. This
approach was also implemented in the CDL scripts. That is, these variables are defined in the input files, but they are
only used as keyword arguments in the optimization and analysis.

Optimization settings
-----------------------------

.. code-block:: python

    number_of_evolutions = 3000
    population_size = 500
    optimization_seed = 4444                        # Optional
    maximum_delta_v = 2e8  # [m/s]


The optimization settings include the ``number_of_evolutions``, ``population_size``, ``optimization_seed`` and ``maximum_delta_v``,
of which the first two are most crucial. Good values for these parameters are heavily dependent on your problem definition,
i.e.  mostly on ``transfer_body_orders`` and ``leg_type``. Moreover, it is important to consider the number of parameters
in your problem before choosing these values. In general, the more parameters in your problem, the larger the population
size and the more evolutions are required to obtain good results.

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