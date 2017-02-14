.. _creating_structure:

Module Structure
=========================

Introduction
------------
In this section we describe the files created when you initialise an nstack project using ``nstack init language`` (e.g. ``nstack init python``).

.. _creating_structure_yaml:

nstack.yaml 
-------

The ``nstack.yaml`` file describes the configuration of your nstack service. It has several required fields that describe the project and let you control the packaging of your service.

Here's a sample,

.. code-block:: yaml

    # Name for the service - we recommend using snake-case
    name: demo-python

    # Service description
    description: Awesome service in python

    # GitHub source repo link (optional)
    github_url: www.github.com/StackHut/demo-python

    # The service dependencies, in terms of the base OS and language runtime
    stack: python

Let's go through all the fields.

``name``
^^^^^^^^

*Required*

The name of the service - this does not need to be unique. We recommend keeping this the same as your project-s name in source control (e.g. on GitHub) and using snake-case.


``github_url``
^^^^^^^^^^^^^^

*Optional*

A link to the repository on GitHub - we use this to create links on the service homepage to the issue tracker and source, if available.

``description``
^^^^^^^^^^^^^^^

*Optional*

A short text description of the service.


``stack``
^^^^^^^^^

*Required*


The base language stack to use when creating an image. Currently we support,

=======     ===========
Name        Description    
=======     ===========
python      `Python 3 <http://python.org/>`_ 
=======     ===========

.. note:: Currently we only support one language stack per service (although you could use this to call anothor language you've bundled into the image)

.. note:: If your chosen language stack has a package manager, e.g. ``pip``, ``npm``, etc., you can fill out the package file, e.g. ``requirements.txt``, ``package.json``, etc., and it will be installed automatically within your image.


``files``
^^^^^^^^^

*Optional*

A list of files and directories within the project directory to include and bundle alongside the image. Useful for specifying resource files and binaries, for instance.

``os_deps``
^^^^^^^^^^^

*Optional*

A list of OS packages you wish to bundle with your service, i.e. those installable via ``yum``. You may need to check with your choosing base OS repository to find the names of the packages and their versions.

.. note:: you can also install language specific packages using your language package manager
