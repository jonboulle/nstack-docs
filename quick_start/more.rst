.. _more:

Example Part #1: Productionising a Classifier
=============================================

So far, we have built and published a Python module with a single method on it, ``numChars``, and built a workflow which connects our method to an HTTP endpoint. This in itself isn't particularly useful, so, now that you've got the gist of how NStack works, it's time to build something more realistic!

In this tutorial, we're going to create and productionise a simple classifier which uses the famous `iris dataset <https://en.wikipedia.org/wiki/Iris_flower_data_set>`_.

We're going to train our classifier to classify which species an iris is, given measurements of its sepals and petals. You can find the dataset we're using to train our model `here  <https://raw.githubusercontent.com/nstackcom/nstack-examples/master/iris/irisclassify/train.csv>`_.

First, let's look at the the format of our data to see how we should approach the problem. We see that we have five fields: 

================  =======================  ===========
Field Name        Description              Type
================  =======================  ===========
``species``       The species of iris      Text

``sepal_width``   The width of the sepal   Double

``sepal_length``  The length of the sepal  Double

``petal_width``   The width of the petal   Double

``petal_length``  The length of the petal  Double
================  =======================  ===========

If we are trying to find the species based on the sepal and petal measurements, this means these measurements are going to be the input to our classifier module, with text being the output. This means we need to write a method in Python which takes four ``Double``\s and returns ``Text``.

Creating your classifier module
******************************

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

Configuring your module
***********************

When your module is started, it is run in a Linux container on the NStack server. Because our module uses libraries like ``pandas`` and ``sklearn``, we have to tell NStack to install some extra operating system libraries inside your module's container. NStack lets us specify these in our ``nstack.yaml``` configuration file in the ``packages`` section. Let's add the following packages:

.. code :: yaml

    packages: ['numpy', 'python3-scikit-learn.x86_64', 'scipy', 'python3-scikit-image.x86_64', 'python3-pandas.x86_64']

Additionally, we want to tell NStack to copy our ``train.csv`` file into our module, so we can use it to train our data. ``nstack.yaml`` also has a section for specifying files you'd like to include:

.. code :: yaml

    files: ['train.csv']


Publishing and starting
***********************

Now we're ready to build and publish our classifier. Remember, even though we run this command locally, our module gets built and published to your NStack Server.

.. code :: bash

    ~/irisclassify/ $ nstack build
    Building NStack Container module irisclassify. Please wait. This may take some time.
    Module irisclassify built successfully. Use `nstack list methods` to see all available methods.

We can now see ``irisclassify.predict`` in the list of existing methods (along with previously built methods like demo.numChars) by running the suggested command nstack list methods

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
   Msg Accepted
   ~/irisclassify/ $ nstack log 2  
   Feb 17 10:32:30 nostromo nstack-server[8925]: OUTPUT: "Iris-versicolor"

Great! Our classifier is now productionised. Next, we're going to connect our classifier to a database, and explore some of the more sophisticated workflows you can build using NStack.
 




