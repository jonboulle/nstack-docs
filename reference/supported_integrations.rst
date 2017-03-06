.. _supported_integrations:

Supported Integrations
======================

NStack is built to integrate with existing infrastructure, event, and data-sources. Typically, this is by using them as *sources* and *sinks* in the NStack Workflow Language.

.. seealso:: Learn more about *sources* and *sinks* in :ref:`Concepts<concepts>` 

**Sources**
 - Postgres ::

    sources.postgres : Text {
      pg_host = "localhost", pg_port = "5432",
      pg_user = "user", pg_password = "123456",
      pg_database = "db", pg_query = "SELECT * FROM tbl;" }

   ``pg_port`` defaults to 5432, ``pg_user`` defaults to ``postgres``, and
   ``pg_password`` defaults to the empty string. The other parameters are mandatory.

 - HTTP ::

    sources.http : Text { http_path = "/foo" }

 - RabbitMQ (AMQP) ::

    sources.amqp : Text {
      amqp_host = "localhost", amqp_port = "5672",
      amqp_vhost = "/", amqp_exchange = "ex",
      amqp_key = "key"
    }

   ``amqp_port`` defaults to 5672 and ``amqp_vhost`` defaults to ``/``.
   The other parameters are mandatory.

**Sinks**
 - Postgres ::

    sinks.postgres : Text {
      pg_host = "localhost", pg_port = "5432",
      pg_user = "user", pg_password = "123456",
      pg_database = "db", pg_table = "tbl" }

   Like for Postgres source,
   ``pg_port`` defaults to 5432, ``pg_user`` defaults to ``postgres``, and
   ``pg_password`` defaults to the empty string. The other parameters are mandatory.

 - NStack Log ::

    sinks.log : Text

   The Log sink takes no parameters.

 - RabbitMQ (AMQP) ::

    sinks.amqp : Text {
      amqp_host = "localhost", amqp_port = "5672",
      amqp_vhost = "/", amqp_exchange = "ex",
      amqp_key = "key"
    }

   Like for AMQP source,
   ``amqp_port`` defaults to 5672 and ``amqp_vhost`` defaults to ``/``.
   The other parameters are mandatory.

 - Firebase ::

    sinks.firebase {
      firebase_host = "localhost",
      firebase_port = "111",
      firebase_path = "..."
    }

   All parameters are mandatory.
