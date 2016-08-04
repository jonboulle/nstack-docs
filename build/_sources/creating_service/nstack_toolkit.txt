.. _nstack_cli:

nstack CLI
================


.. Introduction
.. ------------

The nstack CLI is used to create, test, deploy, and maintain your services hosted on nstack.
It provides a range of commands used to interact with your code and the nstack servers.

Getting Started
^^^^^^^^^^^^^^^

Firstly, install the CLI and requirements by following the instructions in :ref:`getting_started_installation`. 

**Quick Install**

.. code:: bash

    curl https://nstack.com/install/install.sh | sh
    
**Update**

.. code:: bash

    nstack --update
    **TODO**

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

``build``
^^^^^^^^^

.. code:: bash

    $ nstack build [--force]

============    ===========
Option          Description
============    ===========
``--force``     Forces the build to occur even if no file changes 
============    ===========

Builds the image so that it may be tested locally or deployed to the your hosted nstack instance. This command is usually unneeded as both the ``run`` and ``deploy`` commands run a build if needed.

Building a service involves, 
* setting up the base OS and the language stack,
* installing all OS and language packages as specified in the `Hutfile`,
* copying across all files referenced in the `Hutfile`,
* installing the StackHut control runner,
* running any auxiliary commands as specified in the `Hutfile`.

Building can be time-consuming so is performed on an as-needed basis by detecting changes to the files referenced from the `Hutfile`. If this fails, or perhaps you're installing software from across the network as part of the build, you may wish to force the build to occur by passing the ``--force`` flag.


``run``
^^^^^^^^^^^^^^^^

.. code:: bash

    $ stackhut runcontainer [--force]

================    ===========    
Option              Description
================    ===========
``--force``         Forces build before run 
================    ===========


Builds the image and and hosts the service locally on ``http://localhost:4001``. You can test the service either using the client-libaries or by ``curl``-ing the ``test_request.json`` file to the local server, as described in :ref:`using_index`.

Upon running this command the Toolkit will build the image (if required) and run the service within the container. This is exactly the same code as will be run on the hosted StackHut platform so you can be sure that if it works locally it will work in the cloud. Output from running this request is placed in the ``run_result`` directory, with the JSON response object in ``run_result\response.json``.


``deploy``
^^^^^^^^^^

.. code:: bash

    $ stackhut deploy [--force]

================    ===========
Option              Description
================    ===========
``--force``         Forces build before deploy
================    ===========

.. ``--no-build``      Deploy only, do not build or push image first

The deploy command packages and uploads your project to the StackHut platform where it's build remotely and then deployed live under the service address ``username/servicename`` and can be called from ``https://api.stackhut.com/run``. 
Deployment requires that you have an account at StackHut and are logged in using the command line tool.

.. If you've already deployed the image and just want to update the service metadata, e.g. the description, README, API docs, etc., you can run ``deploy`` with the ``--no-build`` flag and it will skip the full deploy - a much quicker operation.

