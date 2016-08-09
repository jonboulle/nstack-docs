.. _getting_started_installation:

=====================
Platform Installation
=====================

Introduction
^^^^^

nstack is comprised of two parts: 

- **nstack engine** is a virtual machine which can be hosted locally or on your cloud provider. It is where your services are deployed to, compiled, and where they run.
- **nstack toolkit** is a local binary which controls the nstack engine. It is used to deploy microservices from your machine to your engine.

.. _machine_installation:

Engine Installation
^^^^^^

To install the machine, browse to nstack's `release page <http://nstack.com/install>`_. We have helpers to let you install it to AWS, or you can download the .iso and host it elsewhere. As it is a VM, it can run on your local machine (using something like KVM or VirtualBox) 

 * :ref:`AWS Installation <aws_installation>`
 * :ref:`Manual Installation <manual_installation>`

.. _aws_installation:

**AWS Installation**

nstack provides a CloudFormation template to install nstack to AWS. 

.. _manual_installation:

**Manual Installation**

**TODO**

nstack provides an .iso which you can host on your virtual machine of choice.

.. _toolk_installation:

Toolkit Installation
^^^^^

To install the nstack CLI, you will need to download it from the website. To do this:

.. code-block:: bash

	$ curl https://nstack.com/install/cli | sh

This will take you through the install process. 