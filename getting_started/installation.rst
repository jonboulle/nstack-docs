.. _getting_started_installation:

Platform Installation
=====================


****
Introduction
****
nstack is comprised of two parts: your nstack machine, which lives as a virtual appliance on your local network or cloud provider, and the nstack toolkit.

The nstack machine is where your services are built, compiled, and executed, whilst the nstack toolkit is used to push services and administer the machine.

As the machine is an appliance, you can run it locally (using something like kvm or virtualbox), or it can be hosted on your cloud provider of choice. You are not limited to one machine: many users have a machine on their cloud provider for production services, and a local machine for testing or staging.

***
Installation
***

..Note: All releases can be found on nstack's `release page <http://nstack.com/install>`_. 

To install the machine, browse to nstack's `release page <http://nstack.com/install>`_. We have helpers to let you install it to AWS, or you can download the .iso and host it where you please. 

 * :ref:`_aws_installation`
 * :ref:`_manual_installation`

.. _aws_installation:

***
AWS Installation
***

**TODO**

nstack provides a CloudFormation template to install to your AWS vpc. 

***
Manual Installation
***

**TODO**

nstack provides an .iso which you can host on your virtual machine of choice.



