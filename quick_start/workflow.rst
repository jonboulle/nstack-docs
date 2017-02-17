.. _workflow:

Building your Workflow
=========================

In the previous tutorial, we built and deployed a Python module using NStack.
This module had a single method on it, ``numChars``, which counted the number of characters in some text.
In this tutorial, we're going to connect this method to a `source` and a `sink`.
Sources generate data which gets sent to your method, and a sink receives data which your method outputs.

Let's first refresh ourselves on what the input and output type of our method was by asking NStack:

.. code:: bash
  
  > nstack list methods
  demo.numChars : Text -> Integer

This means that our method can be connected to any `source` which generates ``Text``, and can write to any sink which can receive an ``Integer``. 

One of the sources that NStack provides is `http`;
if you use this source, NStack sets up an HTTP endpoint which you can send JSON-encoded data to.
As a sink, we are going to use the NStack `log`,
which is a simple sink for seeing the output from your method.

In this example, we're going to connect ``demo.numChars`` to input from an HTTP endpoint (the `source`), and write results to the NStack `log` (the `sink`). We write this in NStack's Workflow Language and use the ``start`` command to deploy the workflow. 

Using NStack, we can build this workflow using the following statements:

=======================================  ===========
Statement                                Description
=======================================  ===========
``source(http:///demo : Text)``          Create an HTTP source on ``/demo``. The ``Text`` statement means it can only create accept and pass on Text.

``demo.numChars``                        The name of the method which we built.

``sink(log:// : Integer)``               Use NStack's log as a sink. The ``Integer`` statement means it can only accept Integers.
=======================================  ===========

.. note:: for a full reference on the NStack Workflow language see `here`

NStack uses the ``|`` operator to connect statements together, just like in a shell such as **bash**. We can connect our source, module, and sink together like so:

.. code:: bash

  source(http:///demo : Text) | demo.numChars | sink(log:// : Integer)

To start this workflow with NStack, we use NStack's ``start`` command:

.. code:: bash

 > nstack start "source(http:///demo : Text) | demo.numChars | sink(log:// : Integer)"
 Started source(http:///demo : Text) | demo.numChars | sink(log:// : Integer) as process 1

We now have a live HTTP endpoint on ``localhost:8080/demo``, running as process **1** on NStack.
We can call it using ``curl`` - we defined it with an input schema of ``Text`` so the parameter (the ``params`` field) must be text.

.. ::note See running processes with ``nstack ps``. Your process number may be different.

.. code:: bash

 > curl -X PUT -d '{ "params" : "Foo" }' localhost:8080/demo 
 Success

If we look at the log of our process, we will be able to see the result.

.. code:: bash

 > nstack log 1
 Feb 17 09:59:26 nostromo nstack-server[8925]: OUTPUT: 3


