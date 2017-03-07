.. _more:

Writing Your Own Module
========================

So far, we have built and published a Python module with a single method on it, ``numChars``, and built a workflow which connects our method to an HTTP endpoint. This in itself isn't particularly useful, so, now that you've got the gist of how NStack works, it's time to build something more realistic!

In this tutorial, we're going to create and productionise a simple classifier which uses the famous `iris dataset <https://en.wikipedia.org/wiki/Iris_flower_data_set>`_.

We're going to train our classifier to classify which species an iris is, given measurements of its sepals and petals. You can find the dataset we're using to train our model `here  <https://raw.githubusercontent.com/nstackcom/nstack-examples/master/iris/irisclassify/train.csv>`_.

First, let's look at the the format of our data to see how we should approach the problem. We see that we have five fields: 

================  =======================  ===========
Field Name        Description              Type
================  =======================  ===========
``species``         The species of iris      Text

``sepal_width``   The width of the sepal   Double

``sepal_length``  The length of the sepal  Double

``petal_width``   The width of the petal   Double

``petal_length``  The length of the petal  Double
================  =======================  ===========

If we are trying to find the species based on the sepal and petal measurements, this means these measurements are going to be the input to our classifier module, with text being the output. This means we need to write a method in Python which takes four ``Double``\s and returns ``Text``.

Creating your module
*******************

To begin, let's make a new directory called ``irisclassify``, ``cd`` into it, and initialise a new module:

.. code:: bash
    
    ~/ $ mkdir irisclassify; cd irisclassify
    ~/irisclassify/ $ nstack init python
    python module 'irisclassify' successfully initialised at ~/irisclassify

Next, let's download our training data into this so we can use it in our module.

.. code:: bash

    ~/irisclassify/ $ curl -O https://raw.githubusercontent.com/nstackcom/nstack-examples/master/iris/irisclassify/train.csv


Defining our API
****************

As we know what the input and output of our classifier is going to look like, let's edit the ``api`` section of ``nstack.yaml`` to define our API (i.e. the entry-point into our module). By default a new module contains a sample method ``numChars``, which we can replace with our definition. We're going to call the method we write in Python ``predict``, which means we can fill in the ``api`` section of ``nstack.yaml`` as follows:

.. code :: java

    api : |
        interface Default {
          predict : (Double, Double, Double, Double) -> Text
        }

This means we are exposing a single method ``predict``, which takes a record of four ``Double``\s (the measurements) and returns ``Text`` (the iris species).


Writing our classifier
**********************
 
Now that we've defined our API, let's jump into our Python module, which lives in ``service.py``.
We see that NStack has created a class ``Service``. This is where we add the methods for our module. Right now it also has a sample method in it, ``numChars``, which we can remove. 


Firstly, let's import the libaries we're using.

.. code :: python

    import nstack
    import pandas as pd

    from sklearn.ensemble import RandomForestClassifier

.. note :: Python modules must also import ``nstack``

Before we add our ``predict`` method, we're going to add ``__init__``, the Python contructor method which runs upon the creation of our module. It's going to load our data from ``train.csv``, and use it to train our Random Forest classifier:

.. code :: python

    def __init__(self):
        train = pd.read_csv("train.csv")
        
        self.cols = ['petal_length', 'petal_width', 'sepal_length', 'sepal_width'] 
        colsRes = ['class']
        trainArr = train.as_matrix(self.cols) 
        trainRes = train.as_matrix(colsRes) 
        
        rf = RandomForestClassifier(n_estimators=100)
        rf.fit(trainArr, trainRes)
        self.rf = rf

Now we can write our ``predict`` method. The second argument, ``inputArr``, is the input -- in this case, our four ``Double``\s. To return text, we simply return from the method in Python.

.. code :: python

    def predict(self, inputArr):
        points = [inputArr]
        df = pd.DataFrame(points, columns=self.cols)

        results = self.rf.predict(df)
        return results.item()

Configuration
*************

When your module is started, it is run in a Linux container on the NStack server. Because our module uses libraries like ``pandas`` and ``sklearn``, we have to tell NStack to install some extra operating system libraries inside your module's container. NStack lets us specify these in our ``nstack.yaml``` configuration file in the ``packages`` section. Let's add the following packages:

.. code :: yaml

    packages: ['numpy', 'python3-scikit-learn.x86_64', 'scipy', 'python3-scikit-image.x86_64', 'python3-pandas.x86_64']

Additionally, we want to tell NStack to copy our ``train.csv`` file into our module, so we can use it to train our data. ``nstack.yaml`` also has a section for specifying files you'd like to include:

.. code :: yaml

    files: ['train.csv']


Publishing and Starting
***********************

Now we're ready to build and publish our classifier. Remember, even though we run this command locally, our module gets built and published to your NStack Server.

.. code :: bash

    ~/irisclassify/ $ nstack build
    Building NStack Container module irisclassify. Please wait. This may take some time.
    Module irisclassify built successfully. Use `nstack list methods` to see all available methods.

We can see our method, ``irisclassify.predict``. Including our ``demo.numChars`` method from the previous tutorial, we should now have two:

.. code :: bash
 
   ~/irisclassify/ $ nstack list methods
    irisclassify.predict : (Double, Double, Double, Double) -> Text
    demo.numChars : Text -> Integer

Our classifier is now published, but to use it we need to connect it to an event-source and sink. In the previous tutorial, we used HTTP as a source, and the NStack log as a sink. We can do the same here by starting the following workflow.

.. code :: bash
   
    ~/irisclassify/ $ nstack start "sources.http : (Double, Double, Double, Double) { http_path : "/irisendpoint" } | irisclassify.predict | sinks.log : Text"

This creates an HTTP endpoint on ``http://localhost:8080/irisendpoint`` which can receive four ``Double``\s, and writes the results to the log as ``Text``. We can test our classifier by sending it some of the sample data from ``train.csv``:

.. code :: bash

   ~/irisclassify/ $ curl -X PUT -d '{ "params" : [4.7, 1.4, 6.1, 2.9] }' localhost:8080/irisendpoint 
   Success
   ~/irisclassify/ $ nstack log 2  
   Feb 17 10:32:30 nostromo nstack-server[8925]: OUTPUT: "Iris-versicolor"

Great! Our classifiier is now productionised.

Other Sources and Sinks
***********************

So far we have used HTTP as a source, and the log as a sink, but NStack supports many other integrations. For instance, we can connect our classifier to use a database as a source and/or a sink by using the ``postgresql`` intergration:

.. code :: bash

  ~/irisclassify/ $ nstack start "source(postgresql://foo:bar@database.contoso.com/flowers?query=SELECT%20*%20FROM%20iris : (Double, Double, Double, Double) | irisclassify.predict | sink(postgresql://foo:bar@database.contoso.com?table=flowers : Text)"

In this case, NStack will ensure that the database is of the correct schema.

.. note :: See all available integrations at :ref:`Supported Integrations <supported_integrations>`

More Powerful Workflows
***********************

So far, we've composed workflows out of a source, a sink, and a single method, but workflows can contain as many steps as you like, as long as the output type of one matches the input type of the other. For instance, let's add our ``demo.numChars`` method from the previous tutorial to our workflow. From listing the available methods above, we see that it takes ``Text`` and returns ``Integer``. Because our ``irisclassify.predict`` method returns ``Text``, this means we can connect -- or `compose` -- them together.

.. note :: ``numChars`` and ``predict`` can be `composed` together because their types -- or schemas -- match. If ``predict`` wasn't configured to output ``Text``, or ``numChars`` wasn't configured to take ``Text`` as input, NStack would not let you build the following workflow.

.. code :: bash
   
    ~/irisclassify/ $ nstack start "sources.http (Double, Double, Double, Double) { http_path = "/irisendpoint" } | irisclassify.predict | demo.numChars | sink : Integer"

Although you can write workflows directly in the ``start`` command, as we have above, NStack provides a more powerful way to build workflows that allows them to be re-used, shared, and composed together. 
All of the workflows that are started with the ``start`` command have to be `fully composed`, which means that they contain a source, one or more modules, and a sink. Many times, you may want to write a workflow which is only `partially composed`; for instance, it contains only modules, is a combination of a source and a module, or is a combination of a module and a sink. These workflows cannot be run by themselves, but can be shared and attached to other sources, sinks, or modules when they are started.

For instance, we could combine ``irisclassify.predict`` and ``demo.numChars`` to form a new workflow ``speciesLength`` like so:

.. code :: java
  
    def speciesLength = irisclassify.predict | demo.numChars

To build workflows like this, we create them as modules in a similar way we created a Python module -- with ``init``. Let's create a new directory called ``irisworkflow``, ``cd`` into it, and create a new workflow module.

.. code :: bash
  
    ~/ $ mkdir irisworkflow; cd irisworkflow
    ~/irisworkflow/ $ nstack init workflow 
    Workflow module 'irisworkflow' successfully initialised at /var/home/fedora/irisworkflow

Instead of creating an ``nstack.yaml``, this creates a single file, ``workflow.nml``, which contains our workflow module.

.. code :: java
  
  module irisworkflow {
    // A sample workflow
    def w = sources.http : Text { http_path = "/s" } | Module1.numChars | sinks.log : Integer
  }

You will notice that the module itself is named ``irisworkflow`` after the directory name, and has an example workflow in it, ``w``. We're going to replace this with our ``speciesLength`` workflow above.

.. code :: java
  
  module irisworkflow {
    // A sample workflow
    def speciesLength = irisclassify.predict | demo.numChars
  } 

As with others modules, we can now build ``irisworkflow`` with the ``build`` command:

.. code :: bash
 
  ~/irisworkflow/ $ nstack build
  Building NStack Workflow module irisworkflow.
  Workflow module irisworkflow built successfully.

Because our workflow ``irisworkflow.speciesLength`` has not been connected to a source or a sink, is is technically a method and is treated as such. This means we can see it in alongside our other methods:

.. code :: bash
  
  ~/irisworkflow/ $ nstack list methods
  irisclassify.predict : (Double, Double, Double, Double) -> Text
  demo.numChars : Text -> Integer
  irisworkflow.speciesLength : (Double, Double, Double, Double) -> Integer

Note that the input type of the workflow is the input type of ``irisclassify.predict``, and the output type is the output type of ``demo.numChars``. Like other methods, this can be connected to a source and a sink to make it `fully composed`:

.. code :: bash

  ~/irisworkflow/ $ nstack start 'src.http : (Double, Double, Double, Double) { http_path = "speciesLength" } | irisworkflow.speciesLength | sink.log : Integer'

Alternatively, you can move the source and sink into the ``workflow.nml`` file:

.. code :: java

  module irisworkflow {
    def completeWorkflow = src.http : (Double, Double, Double, Double) { http_path = "speciesLength" } | irisworkflow.speciesLength | sink.log : Integer;
  }

If you ``build`` this, you can then start it by itself with the ``start`` command, because it's a fully composed:

.. code :: bash

  ~/irisworkflow/ $ nstack start irisworkflow.completeWorkflow

This paradigm can be helpful when we apply it to sources and sinks. Oftentimes, you -- or someone else in your company -- will want to create sources and sinks which are combined with modules, for instance in the following fictional example:

.. code :: java
  
  module customerRecords {
    def cleanSource = source.postgres { postgres_username = "foo, postgres_password = "bar" @database.contoso.com/customers?query=SELECT * FROM customer_records : CustomerRecord) | DataTools.cleanCustomerRecord; 
    def cleanSink = DataTools.ensureValidCustomer | sink(postgresql://foo:bar@database.contoso.com/customers?table=customer_records : CustomerRecord);
  }

Preconfigured sources and sinks can be used in workflows without requiring the user to be familiar with the configuration of the source and sink. 
This becomes useful when you are connecting to more complex middleware (such as streams and message queues), which those building modules and workflows may not need to understand or want to configure. Additionally, it allows sources and sinks to be created securely, without the need to share credentials with those building workflows. The user will simply recieve a stream of ``CustomerRecord``, or be able to output a ``CustomerRecord``.
In this example, we are also adding a module to each to do some processing before and after.

NStack knows that ``cleanSource`` is still a source because is doesn't have a sink attached. Similarly, NStack knows that ``cleanSink`` is a sink, because it doesn't have a source. This means you can find them in your list of sources and sinks using ``list``, and they can be used like any other source and sink, for instance:

.. code :: bash

  ~/ $ nstack start "customerRecords.cleanSource | customerClassifier.predict | customerRecords.cleanSink"

 




