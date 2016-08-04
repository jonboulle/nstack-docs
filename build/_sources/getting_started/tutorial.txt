.. _getting_started_tutorial:

Tutorial
========

This tutorial briefly describes how you can develop, test and deploy a simple service on nstack. This one will only take a few minutes, but services can be as complex as you like. Firstly, check you've installed nstack as described in :ref:`getting_started_installation`. 

Creating a Service
------------------

Initialise a Project
^^^^^^^^^^^^^^^^^^^^

We start by initialising a nstack service, let's call this one ``demo-python``. 

.. code-block:: bash

    # create and cd into the project directory
    [~]$ mkdir demo-python
    [~]$ cd demo-python
    # run nstack init to initialise the project
    [demo-python]$ nstack init python

The ``nstack init`` command takes one parameter, which is the language you'd like to build your service in. Here it is Python (short for Python 3). When you run init, nstack will create a working skeleton project for you to quickly get started with, including an initial Git commit.

This contains all the files a nstack service needs, already configured using sensible defaults for the chosen system.

.. code-block:: bash

    [demo-python]$ ls
    api.idl  app.py  nstack.yaml requirements.txt README.md

There are several files here - and we'll cover the important ones in the following sections. They are all discussed further in :ref:`creating_structure_yaml`.
The ``nstack.yaml`` is a *YAML* file containing configuration regarding dependencies. More information regarding its parameters can be found in :ref:`creating_structure_hutfile`.

.. There is a README.md markdown file to further describe your service.


Signature
^^^^^^^^^

The ``api.idl`` interface-definition (IDL) file describes our service interface. This is a place to specify the inputs into your code; or, to think of it the other way around, the parts of your code you want to expose. 

One of the features of nstack is that your inputs can (optionally) be type-checked. This means you can make sure that your code is getting the right input. Additionally, knowing the inputs and outputs of a service allows you to compose them together intelligently. We like to think of it as bringing the power of statically-typed languages to infrastructure.

**TODO**


Code
^^^^

Having defined the interface into our service, we can now write the code. Your app code lives in ``app.py`` (or ``app.js`` for JS, and so on), as follows:

**TODO**


Hosting your Service
--------------------

Now you've developed your service you can host it on your local nstack install to test it, or you can go straight ahead and deploy live to your nstack server.

Hosting locally
^^^^^^^^^^^^^^^

**TODO**


Hosting on nstack-cloud
^^^^^^^^^^^^^^^^^^^

**TODO**


Using your Service
------------------

Once your nstack service is live, it can react to events.

Events can be third-party event-sources (such as a Kafka stream or a RabbitMQ queue) or internal nstack events, such as our HTTP Gateway or Scheduler. 

When an event comes in, the whole nstack infrastructure is abstracted away from your service code. From this point of view, it's as simple as executing a function call.

Read more about using services in :ref:`using_index`

Calling a service
^^^^^^^^^^^^^^^^^

**TODO**

Further Information
-------------------

This was a simple example, but you can build anything you can in a normal server: you can even package up existing legacy infrastructure into event-driven services. See the :ref:`examples_index` to see how services have been built.



