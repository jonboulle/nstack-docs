.. _nstack_cli:

nstack CLI
================


.. Introduction
.. ------------

The nstack CLI is used to build modules and workflows on the NStack server.

Usage
-----

Having installed the CLI, make sure it's accessible from your path

.. code:: bash
    
    $ nstack --version
    > nstack 0.5.4

You can find the list of commands and options available by running

.. code:: bash

    $ nstack --help

Commands
--------

This section explains the commands supported by the CLI toolkit.

``info``
^^^^^^^^
.. code:: bash

    $ nstack info

Displays information regarding the entire current state of NStack, including:

 - Modules 
 - Sources 
 - Sinks 
 - Running processes 
 - Base images

``init``
^^^^^^^^
.. code:: bash

    $ nstack init <stack>

============    ===========
Option          Description
============    ===========
``stack``       The default stack to use to build your service, e.g. python or NStack Workflow Language.
============    ===========

Initialises a new nstack module in the current directory using the specified base language stack. This creates a working skeleton project which you can use to write your module.

If you are creating a module in an existing programming language, such as Python, ``init`` creates a module with a single ``numChars`` function already created. The initial project is comprised of the following files,

* ``nstack.yaml``, your service's configuration file  (see :ref:`module_structure`),
* ``service.py``, an application file (or service.js, etc.), where your business-logic lives
* an empty packages file (e.g. ``requirements.txt`` for Python, or ``package.json`` for Node, etc.).

``init`` is the command used to create a new workflow. In this case, NStack creates a skeleton ``workflow.nml`` file.

``build`` 
^^^^^^^^^

.. code:: bash

    $ nstack build 

Builds a module on your hosted nstack instance.  

.. note:: ``build`` is also used to build workflows. Remember, workflows are modules too!


``start``
^^^^^^^^^
.. code:: bash

    $ nstack start <workflow>


==============  ===========
Option          Description
==============  ===========
``<workflow>``  The workflow to start, in NStack Workflow Language
==============  ===========


Used to start a workflow as a process. Workflows can either be provided as an argument such as:

.. code:: bash
    $ nstack start "mySource | myModule.myMethod | sink(mySink)"

Or, if you have built a workflow as a module, you can start it with:

.. code:: bash
    $ nstack start myWorkflow


``ps`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack ps


Shows a list of all processes, which are workflows that are running on your your nstack server.

``stop`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack stop <process-id>

Stop a running process.

``list`` 
^^^^^^^^^^

.. code:: bash

    $ nstack list <primitive>

===============    ===========
Option             Description
===============    ===========
``<primitive>``    The primitive you want to list.
===============    ===========

Shows a list of available primitives. Support primitives are modules, workflows, methods, sources, and sinks.

``delete`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack delete <module>

============    ===========
Option          Description
============    ===========
``<module>``    The module's name.
============    ============

Deletes a module (and thus its methods) from NStack.


``logs`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack logs <process>

=============    ===========
Option           Description
=============    ===========
``<process>``    The id of the process.
=============    ===========
    
View the logs of a running process.

``server-logs`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack server-logs
   
View the full logs of the NStack server.

``gc`` 
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack gc

Expert: Garbage-collect unused images to free up space on the server.

