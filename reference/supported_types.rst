.. _supported_types:

Supported Types
===============

Primitive types
---------------

NStack supports the following primitive types:

* ``Integer``
* ``Double``
* ``Boolean``
* ``Text``

.. ByteArray

More complex types can be built out of primitive ones:

* Optional types: ``type1 optional``
* Tuples: ``(type1, type2, ...)``. A tuple must have at least two fields.
* Structs: ``{ name1: type1, name2: type2, ... }``
* Arrays: ``[type1]``
* `Sums <https://en.wikipedia.org/wiki/Algebraic_data_type>`_: ``Name1 type1a ... | Name2 type2a ... | ...``

A user can define their own type in the :ref:`idl_language`.
