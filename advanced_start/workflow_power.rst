.. _workflow_power: 

More Powerful Workflows
=======================

Now that we have published our classifier to NStack as a module, we can use it to demonstrate some of the more powerful features of the workflow engine.

Multiple Steps
---------------

Workflows can contain as many steps as you like, as long as the output type of one matches the input type of the other. For instance, let's say we wanted to create the following workflow:

- Expose an HTTP endpoint which takes four ``Double``\s
- Send these ``Double``\s to our classifier, ``Iris.Classify``, which will tell us the species of the iris
- Count the number of characters in the species of the iris using our ``Demo.numChars`` function
- Write the result to the log

We could write the following workflow:

.. code :: bash
   
  module Iris.Workflow:0.0.1-SNAPSHOT {
    import Iris.Classify:0.0.1-SNAPSHOT as Classifier;
    import Demo:0.0.1-SNAPSHOT as Demo;

    def multipleSteps = Sources.http<(Double, Double, Double, Double> { http_path = "/irisendpoint" } | Classifier.predict | Demo.numChars | sinks.log<Integer>;
  }

.. note :: ``numChars`` and ``predict`` can be `composed` together because their types -- or schemas -- match. If ``predict`` wasn't configured to output ``Text``, or ``numChars`` wasn't configured to take ``Text`` as input, NStack would not let you build the following workflow.

Partial workflows
-----------------

All of the workflows that we have written so far have been `fully composed`, which means that they contain a source and a sink. Many times, you want to split up sources, sinks, and functions into separate pieces you can share and reuse. In this case, we say that a workflow is `partially composed`, which just means it does not contain a source and a sink. These workflows cannot be ``start``\ed by themselves, but can be shared and attached to other sources and/or sinks to become `fully composed`. 

For instance, we could combine ``Iris.Classify.predict`` and ``demo.numChars`` from the previous example to form a new workflow ``speciesLength`` like so:

.. code :: java
  
  module Iris.Workflow:0.0.1-SNAPSHOT {
    import Iris.Classify:0.0.1-SNAPSHOT as Classifier;
    import Demo:0.0.1-SNAPSHOT as Demo;

    def speciesLength = Classifier.predict | Demo.numChars
  } 

Because our workflow ``Iris.Workflow.speciesLength`` has not been connected to a source or a sink, it in itself is still a function. If we build this workflow, we can see ``speciesLength`` alongside our other functions by using the ``list`` command:

.. code :: bash
  
  ~/Iris.Workflow/ $ nstack list functions
  Iris.Classify:0.0.1-SNAPSHOT
    predict : (Double, Double, Double, Double) -> Text
  Demo:0.0.1
    numChars : Text -> Integer
  Iris.Workflow:0.0.1-SNAPSHOT
    speciesLength : (Double, Double, Double, Double) -> Integer

As we would expect, the input type of the workflow is the input type of ``Iris.Classify.predict``, and the output type is the output type of ``demo.numChars``. Like other functions, this must be connected to a source and a sink to make it `fully composed`, which means we could use this workflow it in *another* workflow.

.. code :: bash

  module Iris.Endpoint:0.0.1-SNAPSHOT {
    import Iris.Workflow:0.0.1-SNAPSHOT as IrisWF;
    def http = Sources.http<(Double, Double, Double, Double) | IrisWF.speciesLength | Sinks.log<Integer>;
  } 

Often times you want to re-use a source or a sink without reconfiguring them. To do this, we can similarly separate the sources and sinks into separate workflows, like so:

.. code :: java
  
  module Iris.Workflow:0.0.1-SNAPSHOT {
    import Iris.Classify:0.0.1-SNAPSHOT as Classifier

    def httpEndpoint = sources.http<(Double, Double, Double, Double)> { http_path = "speciesLength" };
    def logSink = sinks.log<Text>

    def speciesWf = httpEndpoint | Classifier.predict | logSink;
  }

Separating sources and sinks becomes useful when you're connecting to more complex integrations which you don't want to configure each time you use it -- many times you want to reuse a source or sink in multiple workflows. In the following example, we are defining a module which provides a source and a sink which both sit ontop of Postgres. 

.. code :: java

  module Iris.DB:0.0.1-SNAPSHOT {
    def petalsAndSepals = Sources.postgres<(Double, Double, Double, Double)> {
      pg_database = "flowers",
      pg_query = "SELECT * FROM iris"
    };

    def irisSpecies = Sinks.postgres<Text> {
      pg_database = "flowers",
      pg_table = "iris"
    };
  }   

If we built this module, ``petalsAndSepals`` and ``irisSpecies`` could be used in other modules as sources and sinks, themselves.

We may also want to add a functions to do some pre- or post- processing to a source or sink. For instance:

.. code :: java

  module IrisCleanDbs:0.0.1-SNAPSHOT {

    import PetalTools:1.0.0 as PetalTools;
    import TextTools:1.1.2 as TextTools;
    import Iris.DB:0.0.1-SNAPSHOT as DB;

    def roundedPetalsSource = DB.petalsAndSepals | PetalsTools.roundPetalLengths;
    def irisSpeciesUppercase = TextTools.toUppercase | DB.irisSpecies; 
  }   

Because ``roundedPetalsSource`` is a combination of a source and a function, it is still a valid source. Similarly, ``irisSpeciesUppercase`` is a combination of a function and a sink, so it is still a valid sink.

Because NStack functions, source, and sinks can be composed and reused, this lets you build powerful abstractions over infrastructure.

