.. _module:

Writing your Module
=========================

NStack Modules contain the methods that can be used on the NStack platform. They are the building blocks which can be used to build workflows and applications.

After this tutorial, we will have a simple Python module deployed to our NStack instance, where it can be hooked up to event and data-sources. This module has a single method in it which simply counts the number of characters in some text. 

.. note:: Before starting, check that NStack is installed by running ``nstack --version`` in your terminal. If you got information about the version of NStack you have, you're good to go. If that didn't work, check out :doc:`Installation </installation>` again.


Step 1: ``init``
----------------

We want to create a new Python module.

Create a directory called ``demo`` where you would like to build your module (NStack uses the name of the directory as the default name of the module) and ``cd`` into that directory using your terminal.

To create a new module, run ``nstack init python``.
You should see the following output confirming that this operation was successful.

.. code:: bash

  ~> mkdir Demo
  ~> cd Demo
  ~/Demo> nstack init python
  python module 'Demo:0.0.1' successfully initialised at ~/Demo

A successful ``init`` will have created some files in the directory.

.. code:: bash

 > ls
 nstack.yaml  requirements.txt  service.py  setup.py

This is the skeleton of an NStack module. ``nstack.yaml`` is the configuration file for your module, and ``service.py`` is where the code of your module lives (in this case, it's a Python class). ``requirements.txt`` and ``setup.py`` are both standard files for configuring Python. 

We're going to be concerned with ``nstack.yaml`` and ``service.py``. For a more in-depth look at all these files, refer to :doc:`Module Structure </reference/module_structure>`

In ``service.py``, there is a ``Service`` class. This is where we would write the methods we want to use on NStack. It is prefilled it with a sample method, ``numChars``, that counts the number of characters in some text.

.. code:: python

  #!/usr/bin/env python3
  # -*- coding: utf-8 -*-
  """
  demo Service
  """
  import nstack

  class Service(nstack.BaseService):
      def numChars(self, x):
          return len(x)


``nstack.yaml`` is where the configuration for this module lives. NStack fills in the ``service``, ``stack``, and ``parent`` for you, so we don't need to worry about them for now.

.. code:: yaml

  # Service name (a combination of lower case letters, numbers, and dashes)
  name: Demo:0.0.1

  # The language stack to use
  stack: python

  # Parent Image
  parent: NStack.Python:0.24.0

  api: |
    interface Default {
      numChars : Text -> Integer
    }

We're going to focus on the ``api`` section, where you tell NStack which of the methods in your ``service.py`` you want to turn into methods on NStack,
and their input and output schemas (also known as types).

.. note:: The schema -- or type -- system is a key feature of NStack that lets you define the sort of data your method can take as input, and produce as output. This helps you ensure that your module can be reused and works as intended in production.

In this instance, we want to expose one method, ``numChars``, which takes ``Text`` and returns an ``Integer``.


Step 2: ``build``
-------------

To build and publish our module on NStack, we use the ``build`` command. 

.. code:: bash

  ~/demo> nstack build
  Building NStack Container module demo. Please wait. This may take some time.
  Module demo built successfully. Use `nstack list methods` to see all available methods

When we run this, the code in the directory is packaged up and sent to the server, where NStack transforms it into a module.

.. note:: Learn more about how NStack packages and runs your module using containers in the :ref:`Architecture <architecture>` section.

We can check that our ``numChars`` method is live by running the suggested ``nstack list methods`` command:

.. code:: bash

  ~/Demo> nstack list methods
  Demo.numChars : Text -> Integer

Now that our ``numChars`` method is live on NStack, we can productionise it by connecting it to input and output data. We do this by attaching it to a event *source* and an event *sink* using NStack's Workflow Language. 

Advanced: Framework Modules
---------------------------

You may want to create a common parent module that has lots of complex dependencies already installed, either to save time or for standardisation. NStack supports this with _Framework Modules_. Simply create a new module similar to above, `nstack init framework [parent]`, and modify the resulting `nstack.yaml` as needed.

You can then build this module using `nstack build`, and refer to it within your future modules within the `parent` field of their `nstack.yaml` config file.





