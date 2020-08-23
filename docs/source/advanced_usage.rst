Advanced Usage
==============

Both command line usage and inline usage have optional arguments to make VizTracer better suit your requirements. 

Filter
------

Sometimes, you will have too many data entries and you want to filter them out to make your overhead and output file smaller. This is very useful when you are tracing a long program with a lot of function entries/exits.

You can limit maximum stack depth to trace by

.. code-block::

    python3 -m viztracer --max_stack_depth 10 my_script.py

OR

.. code-block:: python

    tracer = VizTracer(max_stack_depth=10)

You can include only certain files/folders to trace by

.. code-block::

    python3 -m viztracer --include_files ./src --run my_script.py

OR

.. code-block:: python

    tracer = VizTracer(include_files=["./src"])

Similarly, you can exclude certain files/folders to trace by

.. code-block::

    python3 -m viztracer --exclude_files ./not_interested.py --run my_script.py

OR

.. code-block:: python

    tracer = VizTracer(include_files=["./not_interested.py"])

By default, VizTracer will trace both python and C functions. You can turn off tracing C functions by

.. code-block:: 

    python3 -m viztracer --ignore_c_function my_script.py

OR

.. code-block:: python
    
    tracer = VizTracer(ignore_c_function=True)

It's possible that you want to ignore some arbitrary functions and their descendants. You can do it using ``@ignore_function`` decorator

.. code-block:: python

    from viztracer import ignore_function
    @ignore_function
    def some_function():
        # nothing inside will be traced

For detailed usage of filters, please refer to :doc:`viztracer`

Custom Events
-------------

You may want to insert custom events to the report while you are tracing the program. This is helpful to debug your code and understand what is going on at a specific time point.

VizTracer supports three kinds of custom events:

* Instant Event
* Counter Event
* Object Event

You can refer to :doc:`custom_event` for how to use these.

Log Print
---------

A very common usage of custom events is to intercept ``print()`` function and record the stuff it prints to the final report. This is like doing print debug on timeline.

You can do this simply by:

.. code-block:: 

    python -m viztracer --log_print my_script.py

OR

.. code-block:: python

    tracer = VizTracer(log_print=True)

Memory Clean Up
---------------

To achieve lower overhead, some optimization is applied to the tracer so it will withhold the memory it allocates for future use to reduce the time it calls ``malloc()``. If you want the tracer to free all the memory it allocates while collecting trace, use

.. code-block:: python

    tracer.cleanup()

Multi-Thread and Multi-Process
------------------------------

VizTracer supports both multi-thread and multi-process tracing. 

VizTracer supports python native ``threading`` module without the need to do any modification to your code. Just start ``VizTracer`` before you create threads and it will just work.

It's a little bit more complicated to do multi processing. You basically need to trace each process separately and generate ``json`` files for each process, then combine them with 

.. code-block:: 

    python -m viztracer --combine <json_files>

For detailed usage, please refer to :doc:`multi_process`