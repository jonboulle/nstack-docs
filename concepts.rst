.. _concepts:

NStack Concepts
************

.. _module:
**Modules**

A *module* is a piece of code that has been deployed to NStack, either by you or someone else. It has an input schema and an output schema, which defines what kind of data it can receive, and the kind of data that it returns.

.. _sink_source:

**Sources & Sinks**

* A *source* is something which emits a stream of data. 
* A *sink* is something which can receive a stream of data.

Example sources and sinks are databases, files, message-queues, and HTTP endpoints. Like modules, you can define the input and output schemas for your sources and sinks.

.. _workflows:

**Workflows**

Modules, sources, and sinks can be combined together to build *workflows*. This is accomplished using the *NStack Workflow Language*, a simple, high-level language for connecting things together on the *NStack Platform*.
