.. _workflow_power: 

Example Part #2: More Powerful Workflows
*****************************************

Now that we have published our iris classifier to NStack as a module, we can use it to demonstrate some of the more powerful features of the workflow engine.

Workflows as modules
====================

In the previous examples, we composed workflows directly on the command line using the following format:

.. code :: bash

  $ nstack start "source | module.function | sink"


This is useful for testing, but is limited in a few ways:

- Workflows can't be shared or versioned 
- Writing long configuration parameters on the command line is tedious
- It doesn't allow workflows to be composed together (more on this next) 

One of NStack's most powerful features is that it supports creating workflow `module`\s, which can host one or more workflows. When you write workflow modules, workflows can be composed together, combined, versioned, updated, and shared -- just like functions. In fact, a workflow `is` a function on NStack.

To create a workflow module, we create a new directory and use the ``init`` command.

.. code :: bash
  
    ~/ $ mkdir irisworkflow; cd irisworkflow
    ~/irisworkflow/ $ nstack init workflow 
    Workflow module 'irisworkflow' successfully initialised at /var/home/fedora/irisworkflow

NStack creates a single file, ``workflow.nml``, which contains a sample workflow. 

.. code :: java

  module irisworkflow {                                                                                                                           
    def w = sources.http : Text { http_path = "/foo" } | Module1.numChars | sinks.log : Integer;
  }                                                                             

In this sample, we have a module called ``irisworkflow`` which has a single workflow on it, ``w``. If we replace ``Module.numChars`` with the function from our previous tutorial, ``demo.numChars``, we can then build this workflow with ``nstack build``.


.. code :: bash
 
  ~/irisworkflow/ $ nstack build
  Building NStack Workflow module irisworkflow.
  Workflow module irisworkflow built successfully.

And now start this directly with the start command:

.. code :: bash

  ~/irisworkflow/ $ nstack start irisworkflow.w

Multiple steps
==============

Workflows can contain as many steps as you like, as long as the output type of one matches the input type of the other. For instance, let's say we wanted to create the following workflow:

- Expose an HTTP endpoint which takes four ``Double``\s
- Send these ``Double``\s to our classifier, ``irisclassify``, which will tell us the species of the iris
- Count the number of characters in the species of the iris
- Write the result to the log

We could write the following workflow:

.. code :: bash
   
  module irsiworkflow {
    def multipleSteps = sources.http (Double, Double, Double, Double) { http_path = "/irisendpoint" } | irisclassify.predict | demo.numChars | sinks.log : Integer;
  }

.. note :: ``numChars`` and ``predict`` can be `composed` together because their types -- or schemas -- match. If ``predict`` wasn't configured to output ``Text``, or ``numChars`` wasn't configured to take ``Text`` as input, NStack would not let you build the following workflow.

Partial workflows
================

All of the workflows that we have written so far have been `fully composed`, which means that they contain a source, one or more functions, and a sink. Many times, you want to split up sources, sinks, and functions into separate pieces you can share and reuse. In this case, we say that a workflow is `partially composed`, which just means it does not contain a source, one or more functions, and a sink. These workflows cannot be ``start``\ed by themselves, but can be shared and attached to other sources, sinks, or functions to become `fully composed`. 

For instance, we could combine ``irisclassify.predict`` and ``demo.numChars`` from the previous example to form a new workflow ``speciesLength`` like so:

.. code :: java
  
  module irisworkflow {
    // A sample workflow
    def speciesLength = irisclassify.predict | demo.numChars
  } 

Because our workflow ``irisworkflow.speciesLength`` has not been connected to a source or a sink, is is technically a function. If we build this workflow, we can see ``speciesLength`` alongside our other functions by using the ``list`` command:

.. code :: bash
  
  ~/irisworkflow/ $ nstack list functions
  irisclassify.predict : (Double, Double, Double, Double) -> Text
  demo.numChars : Text -> Integer
  irisworkflow.speciesLength : (Double, Double, Double, Double) -> Integer

As we would expect, the input type of the workflow is the input type of ``irisclassify.predict``, and the output type is the output type of ``demo.numChars``. Like other functions, this must be connected to a source and a sink to make it `fully composed` -- and thus something that can be started with the ``start`` command:

.. code :: bash

  ~/irisworkflow/ $ nstack start 'sources.http : (Double, Double, Double, Double) { http_path = "speciesLength" } | irisworkflow.speciesLength | sink.log : Integer'

Often times you want to re-use a source or a sink without reconfiguring them. To do this, we can similarly separate the sources and sinks into separate workflows, like so:

.. code :: java
  
  module irisworkflow {
    def httpEndpoint = sources.http : (Double, Double, Double, Double) { http_path = "speciesLength" };
    def logSink = sinks.log : Text;
    def speciesWf = httpEndpoint | irisclassify.predict | logSink;
  }

Separating sources and sinks becomes useful when you're connecting to more complex middleware which you don't want to configure each time you use it -- many times you want to reuse a source or sink in multiple workflows. So far we have used HTTP as a source, and the log as a sink, but NStack supports many other integrations. 

.. code :: java

  module irisDatabases {
    def petalsAndSepals = sources.postgres : (Double, Double, Double, Double) {
      pg_database = "flowers",
      pg_query = "SELECT * FROM iris"
    };

    def irisSpecies = sinks.postgres : Text {
      pg_database = "flowers",
      pg_table = "iris"
    };
  }   

If we built, this module, ``irisDatabases.petalsAndSepals`` and ``irisDatbases.irisSpecies`` could be used other modules as sources and sinks.

We may also want to add a module to do some pre- or post- processing to a source or sink. For instance:

.. code :: java

  module irisDatabases {
    def petalsAndSepals = sources.postgres : (Double, Double, Double, Double) {
      pg_database = "flowers",
      pg_query = "SELECT * FROM iris"
    };

    def irisSpecies = sinks.postgres : Text {
      pg_database = "flowers",
      pg_table = "iris"
    };
  
    def roundedPetalsSource = petalsAndSepals | PetalsTools.roundPetalLengths;
    def irisSpeciesUppercase = TextTools.toUppercase | irisSpecies; 
  }   

Because ``roundedPetalsSource`` is a combination of a source and a function, it is still a valid source. Similarly, ``irisSpeciesUppercase`` is a combination of a function and a sink, so it is still a valid sink.

.. note :: Composition rules: a function combined with another function is still a function, a source combined with a function is still a source, and a function combined with a sink is still a sink.

This means you can find them in your list of sources and sinks using ``list``.
