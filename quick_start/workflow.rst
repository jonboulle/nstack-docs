.. _workflow:

Building your Workflow
=========================

In the previous tutorial, we built and published a Python module `Demo` using NStack.
This module had a single method on it, ``numChars``, which counted the number of characters in some text. Although it has been published, we cannot talk to it until we connect it to a `source` and a `sink`.
In this tutorial, we're going to do this, and we'll be using an HTTP endpoint as a `source`, and NStack's built-in log as a `sink`. When we're finished, we will be able to send some text to an HTTP endpoint, and see the length of the text in our log. 

.. note:: Sources generate data which gets sent to your method, and sinks receive the data which your method outputs. Learn more in :ref:`Concepts<concepts>`

Let's first refresh ourselves on what the input and output types of our method were by asking NStack:

.. code:: bash
  
  > nstack list methods
  Demo.numChars : Text -> Integer

This means that our method can be connected to any source which generates ``Text``, and can write to any sink which can take an ``Integer`` as input. 

One of the sources that NStack provides is ``http``;
if you use this source, NStack sets up an HTTP endpoint which you can send JSON-encoded data to.
As a sink, we are going to use the NStack ``log``,
which is a simple sink for seeing the output from your method.

.. note:: See a list of available sources and sinks in :ref:`Supported Integrations <supported_integrations>`

In full, our workflow is going to look like this:

.. code:: bash
  import Demo:0.0.1 as Demo;
  Sources.http : Text { http_path = "/demo" } | Demo.numChars | Sinks.log : Integer


NStack uses the ``|`` operator to connect statements together, just like in a shell such as ``bash``. We use it to connect together the parts to form our workflow.

Let's break these parts to see what we're doing:

===============================================  ===========
Part                                             Description
===============================================  ===========
``Sources.http : Text { http_path = "/demo" }``  Use ``http`` as a source, which creates an endpoint on ``/demo``. The ``Text`` statement means it can only accept and pass on Text.

``Demo.numChars``                                The name of the method which we built.

``Sinks.log : Integer``                          Use NStack's log as a sink. The ``Integer`` statement means it can only accept Integers.
===============================================  ===========

To start this workflow with NStack, we use NStack's ``start`` command. This opens a command prompt where you can type a snippet and press Ctrl+D to submit it. Alternatively, you can pipe this in from a file.

.. code:: bash

 > nstack start 
 import Demo:0.0.1 as Demo;
 'Sources.http : Text { http_path = "/demo" } | Demo.numChars | Sinks.log : Integer'
 [Ctrl + D]
 Started Sources.http : Text { http_path = "/demo" } | Demo.numChars | Sinks.log : Integer as process 1

We now have a live HTTP endpoint on ``localhost:8080/demo``, running as process ``1`` on NStack. The HTTP endpoint is configured to accept JSON-encoded values. We defined it to use an input schema of ``Text``, so we will be able to send it any JSON ``string``. In our JSON, we put ``params`` as the key, and our input as the value:

We can call it using ``curl``:

.. code:: bash

 > curl -X PUT -d '{ "params" : "Foo" }' localhost:8080/demo 
 Success

And if we look at the log of our process, which we configured as the sink, we will be able to see the result. Because our process was started with an id of ``1``, we run the following:

.. code:: bash

 > nstack log 1
 Feb 17 09:59:26 nostromo nstack-server[8925]: OUTPUT: 3

Great!
