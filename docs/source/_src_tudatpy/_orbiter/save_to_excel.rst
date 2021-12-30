.. _`orbiter_save`:

Orbiter save to Excel
========================

The results from the orbiter analyses should be used to design a spacecraft in CDP4 that can
actually fly the mission. To do so, one needs to write the results of one orbiter to Excel. This is done with
``orbiter/orbiter_save_excel.py``. As this script executes, it asks the user which case is to be saved. This is done by
providing the number in the subdirectory name (``i`` in ``Orbiter_i``), as depicted below.

.. figure:: _static/console_orbiter_saving.png
