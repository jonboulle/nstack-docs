.. _workflow_language:

Workflow Language
=================

A module consists of a module header, import statements, and definitions,
for instance: ::

  module ModuleB:0.1.0 {
    import ModuleA:0.1.0 as A;
    def x = A.y | A.z;
  }

An import statement includes the module to be imported (``MyModule:0.1.0``)
and its alias (``A``).
The alias is used to qualify functions imported from the module,
e.g. ``A.y``.

Definitions bind function names (``x``) to expressions (``A.y | A.z``).
Any function ``x`` defined in a module ``ModuleB`` can be used in
any other module: ::

    import ModuleB:0.1.0 as B;
    def z = filter B.x;

If a name is not prefixed by a module alias, it refers to a function defined in
the current module.

Expressions combine already defined functions through the following operations:

* Pipe: ``A.y | A.z``

  Every value produced by ``A.y`` is passed to ``A.z``.

  The output type of ``A.y`` must match the input type of ``A.z``.

* Concat: ``concat A.y`` or ``A.y*``

  ``A.y`` must be a function that produces lists of values,
  in which case ``concat A.y`` is a function that "unpacks" the lists
  and yields the same values one by one.

* Filter: ``filter A.y`` or ``A.y?``

  ``A.y`` must be a function that produces "optional" (potentially missing) values,
  in which case ``filter A.y`` is a function that filters out missing values.

* Type application. Some functions (notably, most sources and sinks) can be specialized
  to multiple input or output types.
  This is done with type application: ``Sources.http<Text>`` specializes
  ``Sources.http`` to the type ``Text``.

* Parameter application: ``A.y { one = "...", two = "..." }``.

  Parameters are analogous to UNIX environment variables in the following ways:

  1. Parameters are inherited. E.g. in ::

      y = x;
      z = y { foo = "bar" };

    both functions ``x`` and ``y`` will have access to ``foo`` when ``z`` is
    called.

  2. Parameters can be overridden. E.g. in ::

      y = x { foo = "baz" };
      z = y { foo = "bar" };

    ``y`` overrides the value of ``foo`` that is passed to ``x``.
    Therefore, ``x`` will see the value of ``foo`` as ``baz``, not ``bar``.

  Parameters are used to configure sources and sinks —
  for instance, to specify how to connect to a PostgreSQL database.

  Parameters can also be used to configure user-defined modules.
  Inside a Python nstack method, the value of parameter ``foo`` can be accessed as
  ``self.args["foo"]``.

The workflow language supports line and block comments.
Line comments start with ``//`` and extend until the end of line.
Block comments are enclosed in ``/*`` and ``*/`` and cannot be nested.

EBNF grammar
------------

The syntax is defined in EBNF (ISO/IEC 14977) in terms of tokens.

.. highlight:: ebnf

::

  module = 'module', module name, '{', {import}, {definition}, '}';
  import = 'import', module name, 'as', module alias, ';';
  definition = 'def', name, '=', expression, ';';
  expression = expression1, {'|', expression1};
  expression1 = application, '*'
              | application, '?'
              | 'concat', application
              | 'filter', application
              ;
  application = term [arguments];
  arguments = '{', argument binding, {',', argument binding}, '}';
  argument binding = name, '=', literal;
  term = '(', expression, ')'
       | qualified name ['<', type, '>']
       ;
