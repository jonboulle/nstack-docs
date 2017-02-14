.. _module:

Writing your module
=========================

Remember that :ref:`NStack modules <module>` contain definitions of methods that can be used throughout our project.
These can be combinations of existing methods, or they can tell NStack about some code in another language.
NStack manages the building and deployments of our methods for us.

We want NStack to manage some code for us that takes a name, and returns a greeting for that name.
When we want to create a new directory, the first step is to ``init`` a new project.

Step 1: ``init``
-------------

Create a directory called "nstack.greeter" somewhere convenient
(nstack uses the name of the directory as the default name of the module)
and `cd` to that directory in a terminal.

At this point, let's check that NStack is installed.
Try `nstack --version` in your terminal.
If you got information about the version of NStack you have, you're good to go.
If that didn't work, check out :doc:`installation </installation>` again.

OK, we want to create a python module.
To do so, we type `nstack init python` in the terminal.
You should see some input confirming that this operation was successful.
(If not, and you can't figure out why, :doc:`try this </troubleshooting>`).

A successful ``init`` will have created some files in the directory.
``ls`` shows us::
  nstack.yaml  requirements.txt  service.py  setup.py
We're going to be concerned with ``nstack.yaml`` and ``service.py``.
(For a more in-depth look at all these files, I refer you :doc:`here </reference/module_structure>`)

NStack has helpfully generated a barebones ``service.py`` for us.
Let's crack it open and make some changes.
We see a ``numChars`` method has been defined.
