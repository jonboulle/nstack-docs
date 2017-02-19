Installation & Upgrading
************

.. _installation:

Installation
===========

NStack can run out of the box, either on:
 - your cloud provider of choice
 - your internal cloud
 - locally using VirtualBox, VMWare, or your operating system's native virtualisation 

For Proof of Concepts, NStack offers a hosted solution. If this is required, please `get in touch <help@nstack.com>`_.

.. note:: For more information on NStack's architecture, see :ref:`Architectural Overview<_architecture>` 

The virtual appliance can be found on the NStack's `GitHub Releases page <http://github.com/nstackcom/nstack-releases/releases>`_, where is is provided as a ``raw`` or ``qcow2`` image. 

The `NStack Server` is configured using ``cloud-init``, which is supported by major cloud providers. 

Upgrading
========

The `NStack Server` can be update atomically using `rpm-ostree`. To upgrade to a new release, you can simply run:

.. code:: bash

    > sudo rpm-ostree upgrade

Following the upgrade, you should reboot your machine with:

.. code:: bash

   > sudo reboot

NStack releases follow a monthly cadence. 

