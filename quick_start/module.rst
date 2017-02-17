.. _module:

Writing your module
=========================

:ref:`NStack modules <module>` contain the methods that can be used on the NStack platform. They are the building blocks which can be used to build workflows and applications.

Modules are comprised of a configuration file and your business-logic, such as Python code. When we `build` a module, NStack deploys the code to the cloud and expose the functions in your business-logic which you specified in your configuration.

After this tutorial, we will have a live Python classifier deployed to our NStack instance which can be hooked up to event and data-sources. 

.. note:: Before starting, check that NStack is installed by running `nstack --version` in your terminal If you got information about the version of NStack you have, you're good to go. If that didn't work, check out :doc:`Installation </installation>` again.


Step 1: ``init``
-------------

Create a directory called ``demo`` and `cd` into that directory using your terminal. NStack uses the name of the directory as the default name of the module.

We want to create a new Python module. To do so, type `nstack init python` in the terminal.
You should see the following output confirming that this operation was successful.

.. code:: bash

  lanthias ~/demo> nstack init python
  python module 'demo' successfully initialised at /home/lanthias/demo


(If not, and you can't figure out why, :doc:`try this </troubleshooting>`).

A successful ``init`` will have created some files in the directory. This is the skeleton of an NStack module. 

``ls`` shows us:

.. code:: bash

 nstack.yaml  requirements.txt  service.py  setup.py

We're going to be concerned with ``nstack.yaml`` and ``service.py``.
(For a more in-depth look at all these files, refer to :doc:`Module Structure </reference/module_structure>`)

`service.py` is where the business-logic of your Python module lives. This is a mostly regular old Python, NStack prefills it with a sample method, `numChars`, that counts the number of characters in a some text.

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


`nstack.yaml` is where the configuration for this module lives. NStack fills in the service, stack, and parent for you, so we don't need to worry about them for now.

.. code:: yaml

  # Service name (a combination of lower case letters, numbers, and dashes)
  name: demo

  # The language stack to use
  stack: python

  # Parent Image
  parent: com.nstack.python:24.0

  api: |
    interface Default {
      numChars : Text -> Integer
    }

  # (Optional) System-level packages needed
  packages: []

  # (Optional) Commands to run when building the service (Bash-compatible)
  commands: []

  # (Optional) Files/Dir to copy across into the service (can use regex/glob syntax)
  files: []

We're going to focus on the `api` section, where you tell NStack which of the methods in your `service.py` you want to expose, and the input and output types (also known as schemas) of these methods. In this instance, we are telling NStack that we want to expose one method, `numChars`, which takes `Text` and returns an `Integer`. 

.. note:: NStack's type -- or schema -- system lets you define the sort of data your function can take as input, and produce as output, which helps you ensure that your module can be reused and works as intended in production.

Step 2: ``build``
-------------

To build and deploy our module on NStack, we use the `build` command. 

.. code:: bash
  lanthias ~/demo> nstack build
  Building NStack Container module demo. Please wait. This may take some time.
  Module demo built successfully. Use `nstack list methods` to see all available methods

When we run this, the code in the directory is packaged up and send to the server, where NStack transforms it into a your module.

.. note:: Learn more about how NStack packages and runs your module using containers in the :ref:`Architecture<_architecture>` section.

We can check that our `numChars` method is live by running the suggested command:

.. code:: bash
  lanthias ~/demo> nstack list methods
  demo.numChars : Text -> Integer

Now that our method is live and productionised on NStack, we can attach it to a event *source* and an event *sink* using NStack's Workflow Language. 

