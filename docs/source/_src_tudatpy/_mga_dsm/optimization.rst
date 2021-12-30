.. _`mga_dsm_optimization`:

MGA-DSM Optimization
========================================

In the following section the optimization of MGA-DSM trajectories is explained. If you are unfamiliar with optimizations
within Tudat(py) and/or `PyGMO`_, please refer to `Using Tudat(Py) with PyGMO`_ first.

.. _`PyGMO`: https://esa.github.io/pygmo2/index.html
.. _`Using Tudat(Py) with PyGMO`: https://tudat-space.readthedocs.io/en/latest/_src_resources/pygmo_basics.html

The script
---------------------------------------

The optimizer will search for optimal solutions in terms of two objectives: :math:`\Delta V` and time of flight. The `Pareto
fronts`_ that result from the optimization are always saved and can be plotted in order to identify specific solutions that
are to be analyzed further, in terms of available communication time, incident solar flux and other quantities.

.. _`Pareto fronts`: https://en.wikipedia.org/wiki/Pareto_front

The optimization can be performed, with the settings as defined in the input file, without any further modifications or other
settings to be defined, with ``transfer_trajectory/transfer_trajectory_optimization.py``. This script imports the required
packages, modules, functions and variables and performs the optimization. A user defined problem (UDP) class is defined in
``src/TransferTrajectoryOptimizer.py`` as required by PyGMO. This class contains an additional function that runs the
actual optimization. The Non-dominated Sorting Genetic Algorithm 2 (NSGA2) has been implemented with default settings.
This algorithm was chosen based on its performance in multiple case studies, where it outperformed the other Multi-Objective
optimizers available in PyGMO.

During the optimization, the parameters and fitness values of every 100th generation are saved to text files, which can
be used for reference later on, for example to check convergence of the optimization. Afterwards, the Pareto front is
obtained from the final generation, plotted and optionally saved in PDF format. The corresponding parameters and fitness
values are also saved to text files. If multiple planet sequences are given in ``transfer_body_orders``, the next planet
sequence is optimized subsequently. Otherwise, the script terminates and the Pareto front(s)
can be analyzed in order to choose solutions that are interesting for further analysis.

Creating the UDP and performing the optimization are done with the code snippet below. This also illustrates that the
departure and arrival orbit characteristics and seed are optional inputs, as explained in :ref:`mga_dsm_inputs`.

.. code-block:: python

    from src.TransferTrajectoryOptimizer import TransferTrajectoryOptimizer

    optimizer = TransferTrajectoryOptimizer(number_of_evolutions,
                                            population_size,
                                            departure_date,
                                            departure_date_margin,
                                            maximum_delta_v,
                                            leg_type,
                                            transfer_body_order,
                                            departure_semi_major_axis=departure_semi_major_axis,
                                            departure_eccentricity=departure_eccentricity,
                                            arrival_semi_major_axis=arrival_semi_major_axis,
                                            arrival_eccentricity=arrival_eccentricity,
                                            seed=optimization_seed)

    pareto_fitness, results_directory = optimizer.perform_optimization(curren_dir)

.. End of code block

Tips & Tricks to help the optimization
-------------------------------------------------

It may occur that the optimization does not yield expected, or even satisfactory, results. This may be because the problem
definition is not ideal, but it may also be that the optimal solutions are simply not found. In the latter case there are a
few things that can be done to 'manipulate' or 'tweak' the optimization:

* *Pick a different*  ``optimization_seed``
    The seed is used for two purposes: to create the initial population and to initialize the optimization algorithm. By
    specifying a different seed, the starting point for the optimization is different and the final results may be better
    (or worse).

* *Set* ``maximum_delta_v``
    It is possible to try and force the optimization towards solutions with lower :math:`\Delta V`. This can be done by specifying
    a maximum :math:`\Delta V`. This value is used in the optimization to apply a penalty to all solutions that exceed it,
    thereby forcing it to continue to search for better solutions. There is a risk involved here, if this maximum
    :math:`\Delta V` is too low, the optimization may not find any solutions satisfying it at all and won't give you any
    solutions that are not penalized. The default is set to :math:`2e8` m/s, so that it is practically ineffective.

* *Increase* ``population_size``
    Increasing the population size (even only slightly) will yield a different initial population, thereby affecting
    the entire evolution and may therefore yield better solutions (maybe even in less evolutions).

* *Reduce number of parameters*
    It may be the case that a problem *with* DSMs is simply too complex to be optimized efficiently. In that case it may
    be better to reduce the number of parameters, either by not using DSMs or by reducing the number of planets that are
    visited.

Lastly, during the analysis of a few case studies, it was noted that the time of flight range in the Pareto front is
rather limited in some cases. In this case it may be that the optimization has found optimal solutions for this range,
but that these are not acceptable and that larger times of flight need to be explored. This may be achieved with the
above tricks (e.g. it is advised to try setting a maximum :math:`\Delta V` first), but there is one more trick:

* *Increase values in* ``MINIMUM_TIME_OF_FLIGHT_DICT`` *and* ``MAXIMUM_TIME_OF_FLIGHT_DICT`` *in* ``transfer_trajectory/transfer_trajectory_inputs.py``
    In this file the boundaries as used in the optimization, regarding time of flight, are defined, depending on the planet
    that defines the end of a leg. One can increase the minima (and possibly increase the maxima) to move (and extend) the
    time of flight range of the Pareto front.

Saved files
---------------------

The data files and Pareto fronts are saved to a subdirectory of the directory where the executable python scripts are
located per planet sequence that was optimized. The subdirectory's name starts either with ```MGA_noDSM_`` or ``MGA_DSM_``
and ends with an abbreviation of the planet sequence. This subdirectory now only contains an ``optimization`` subdirectory,
but will be extended with subdirectories with further analysis results later on. The ``optimization`` subdirectory contains
the Pareto front data and a ``generations`` subdirectory containing the data of ever 100th generation. The file structure
is depicted below.

.. figure:: _static/file_structure_optimization.png