.. _idl_language:

IDL Language
============

An IDL block describes the interface of an external module
(which could be written in Python, for instance).
It is embedded in :ref:`nstack.yaml<creating_structure_yaml>`

The IDL block consists of two sections:
the first section declares types,
the second section declares functions.

Types are declared using the ``type`` keyword: ::

  type PlantInfo = { petalLength : Double
                   , petalWidth : Double
                   , sepalLength : Double
                   , sepalWidth : Double
                   }
  type PlantSpecies = Text

The left-hand side of a type declaration is the new type name;
the right-hand side must be an :ref:`existing type<supported_types>`.
Type declarations don't need to be delimited in any way.

Function declarations have form ``name : Type`` and
must be delimited by commas, for instance ::

  gotham : MovieRecordImage -> MovieRecordImage,
  kelvin : MovieRecordImage -> MovieRecordImage,
  lomo : MovieRecordImage -> MovieRecordImage

Thus, the full IDL block might look like this: ::

  type PlantInfo = { petalLength : Double
                   , petalWidth : Double
                   , sepalLength : Double
                   , sepalWidth : Double
                   }
  type PlantSpecies = Text

  gotham : MovieRecordImage -> MovieRecordImage,
  kelvin : MovieRecordImage -> MovieRecordImage,
  lomo : MovieRecordImage -> MovieRecordImage
