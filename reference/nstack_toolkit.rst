.. _nstack_cli:

nstack CLI
================


.. Introduction
.. ------------

The nstack CLI is used to create, test, deploy, and maintain your services hosted on nstack.
It provides a range of commands used to interact with your code and the nstack servers.

Getting Started
^^^^^^^^^^^^^^^

Firstly, install the CLI and requirements by following the instructions in :ref:`CLI installation <CLI_installation>`. 

Usage
-----

Having installed the CLI, make sure it's accessible from your path

.. code:: bash
    
    $ nstack -V
    > nstack 0.5.4

Now that's done, you may wish to run through the tutorial at :ref:`tutorial_create`.

You can find the list of commands and options available by running,

.. code:: bash

    $ nstack --help

.. note:: Enable verbose mode to view more debug output using ``nstack -v``.

Commands
--------

In this section we'll go over the main commands supported by the Toolkit and explain their use in helping you to build, test, deploy and maintain your services locally and in the cloud.

Help for any command can be displayed by running,

.. code:: bash

    $ nstack command --help


``info``
^^^^^^^^

.. code:: bash

    $ nstack info

Displays information regarding the CLI version

``remote``
^^^^^^^^^
**TODO-mandeep**
.. code:: bash

    $ nstack remote nstack-host

Set the hosted nstack server which you would like to use. 

``init``
^^^^^^^^
.. code:: bash

    $ nstack init stack

============    ===========
Option          Description
============    ===========
``stack``       The default language stack to use, e.g. python, nodejs, etc.
============    ===========

Initialises a new nstack project in the current directory using the specified base language stack. This creates a working skeleton project which you can modify to rapidly build your own service. 

By default it creates a service in your stack that has a single ``add`` function already specified. The initial project is comprised of the following files,
**TODO-mandeep**

* ``nstack.yaml``, your service's config file  (see :ref:`creating_structure_hutfile`),
* an ``api.idl`` Interface Definition File, which is where you choose which parts of your code you want to expose (see :ref:`creating_app_idl`),
* an ``app.py`` application file (or app.js, etc.),
* a ``README.md`` markdown file,
* an empty packages file for your chosen language stack (e.g. ``requirements.txt`` for Python, or ``package.json`` for Node, etc.).

The ``init`` command also creates a git repo and commits the files be default.

``build`` [building/deploying]
^^^^^^^^^
**TODO-mandeep**

.. code:: bash

    $ nstack build [--force]

============    ===========
Option          Description
============    ===========
``--force``     Forces the build to occur even if no file changes 
============    ===========

Builds the image on your hosted nstack instance. This command is usually unneeded as the ``boot`` command runs a build if needed.

``run/start`` [run/starting]
^^^^^^^^^
**TODO-mandeep**

.. code:: bash

    $ nstack run service [event-source] [entry=]

============    ===========
Option          Description
============    ===========
``entry=``      The function to send the event into
============    ===========

Attaches your service to an event-source. When an event is recieved, your service runs. 

``ps`` [listing running service+event]
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack ps


Shows a list of all services which are *active* on your nstack server (i.e. they are been bound to an event-source). You can think of these as 'processes'. This is distincy from ``ls``, which shows you the services which have been deployed and are available to run. 

``kill`` [stop a running service+event]
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack kill process


Stop a running process.

``ls`` [listing available/built services]
^^^^^^^^^^

.. code:: bash

    $ nstack ls


Shows a list of all available services on your nstack server. These may or may not be running processes (i.e. attached to event-sources).

``rm`` [remove service]
^^^^^^^^^^^^^^^^

.. code:: bash

    $ nstack rm service


Deletes a service which from your nstack host. 


``status`` [logs/status of a service]


