.. _tutorial1:

Tutorial #1 -- Deploying a Service
========

This tutorial walks you through deploying a simple service on nstack; as a first example, we're using a demo service which squares a number (don't worry, we'll be adding to it in due course!)

Before we begin, check you've installed nstack as described in :ref:`getting_started_installation`, and attached your toolkit to your nstack-engine.

Creating a Service
------------------

Initialise a Project
^^^^^^^^^^^^^^^^^^^^

We start by initialising a nstack service, with the `--remote` flag. This simply pulls a service from a GitHub repository. 

.. code-block:: bash

    # create and cd into the project directory
    [~]$ nstack init --remote=https://github.com/nstack-oss/example-service
    [~]$ cd example-service

This already contains all the files a nstack service needs.

.. code-block:: bash

    [example-service]$ ls
    api.idl  app.py  nstack.yaml requirements.txt 

Service structure
^^^^^^^^^^^^^^^^^^^^
Let's walk through these files.

``app.py``
^^^^^^^^^^

This is a simple python class which comprises our service. As you can see, we're not doing anything particularly exciting here, but this could be as complex as you please.

.. code-block:: python

	#!/usr/bin/env python3
	# -*- coding: utf-8 -*-
	import nstack	

	class ExampleService(nstack.Service):
		def square(number):
			return number * number


``nstack.yaml``
^^^^^^^^^
The ``nstack.yaml`` is a *YAML* file containing configuration. Here, it includes any dependencies your service has, the stack (or runtime) for your service, and any operating system dependencies (which are *yum* packages). Because our service is boring at the moment, we don't have any dependencies (yet!)

.. code-block:: yaml
	
	# Service name (a combination of lower case letters, numbers, and dashes)
	name: image-process

	# The language runtime
	stack: python

``api.idl``
^^^^^^^^^

The ``api.idl`` is an interface-definition (IDL) file which describes our service interface. This is a place to specify the inputs into your code; or, to think of it the other way around, the parts of your code you want to expose. 

.. code-block:: java

	ExampleService {
		sqrt(int) : int
	}

One of the features of nstack is that your inputs is type-checked. Here, we are saying we want to expose our function ``sqrt`` which takes an ``int`` and returns an ``int``.

``requirements.txt``
^^^^^^^^


This is the dependencies for Python's package manager. There aren't any yet, so this file is empty.


Building your service with nstack-engine
--------------------

When you compile or run a service, it is run on the nstack engine (which is a Linux virtual machine). Make sure you've installed nstack as described in :ref:`getting_started_installation`. To compile it with the engine, run ``nstack build``

.. code-block:: bash

	$ nstack build
	[Success] Your service is live at 1.1.1.1

Once a service is live, it can be run or run in response to various event sources. To simply run our ``square`` function we can: 

.. code-block:: bash

	$ nstack run square("3")
	[Error] Service expected 'int' but received 'string' 

Oops! As we specified in our ``api.idl``, our ``square`` function should take an int and return an int. We accidentally passed a string ("3" instead of 3), so it errored. 

Let's try again:

.. code-block:: bash

	$ nstack run square(3)
	[Success] Service successfully ran with result `9 : Int`

Great! 

Next steps
-------------------

Great! Now we're ready to add some dependencies and build something a bit more 'real-world' 

:ref:`tutorial2`





