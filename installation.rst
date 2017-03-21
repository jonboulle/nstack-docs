Installation & Upgrading
************************

.. _installation:

Installation
=============

NStack is platform-agnostic and can run out-of-the-box wherever you can run a virtual machine, including:

 - your cloud provider of choice
 - your internal cloud
 - locally using VirtualBox, VMWare, or your operating system's native virtualisation 

For Proof of Concepts, NStack offers a hosted solution. If this is required, please reach out to info@nstack.com.

The virtual appliance can be found on the NStack's `GitHub Releases page <http://github.com/nstackcom/nstack-releases/releases>`_, where it is provided as a ``raw`` or ``qcow2`` image. We also provide an AWS AMI, which can be found under the id of ``ami-53a47245``

To launch this AMI to an EC2 instance on your AWS account, you can `click here <https://console.aws.amazon.com/ec2/v2/home?region=us-east-1#LaunchInstanceWizard:ami=ami-53a47245>`_. 

The `NStack Server` is configured using ``cloud-init``, which is supported by major cloud providers, so it will work out of the box with your credentials. Please note that the first time you boot NStack it may take a few minutes to initialise. 

Upgrading
==========

The `NStack Server` can be updated atomically using `rpm-ostree`. To upgrade to a new release, you can simply run:

.. code:: bash

    > sudo rpm-ostree upgrade

Following the upgrade, you should reboot your machine with:

.. code:: bash

   > sudo reboot

NStack releases follow a monthly cadence. 

