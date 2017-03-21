.. _architecture:

Architectural Overview
======================


Overview
********

NStack is composed of a local CLI toolkit (client) which runs on a user's machine, and a virtual appliance (server) which can run in a number of places:

* On your or your company's cloud environment (e.g. AWS, Azure, or a private cloud)
* The NStack Cloud, which the NStack team hosts and administers for you on our servers
* Your local machine for testing

Alternatively, NStack can provide an `.rpm` that can run on your own server. If this is a requirement, please contact us for instructions.


Technologies
**********

The NStack server is an RPM-based virtual appliance built on Red Hat’s `Project Atomic <https://www.projectatomic.io/>`_ and configured through `cloud-init <https://cloudinit.readthedocs.io/en/latest/>`. When a user builds a module with NStack, NStack sends the code to the virtual appliance, packages it, and turns it into a Linux container. When a workflow is written, this is also sent to the server, which takes care of all message routing and orchestration.

NStack modules can be thought of as serverless microservices which can be used to build cloud dataflows. 

NStack is built using best-of-class technologies, such as:
 - Haskell 
 - rpm-ostree
 - btrfs 
 - systemd-nspawn
 - d-bus  


Diagrams
********

.. image:: arch.png

.. image:: arch2.png
