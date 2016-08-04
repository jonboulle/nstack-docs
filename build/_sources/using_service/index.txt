.. _using_index:

***************
Using a Service
***************

In this section, we focus on how to make use of your service once you deploy it to nstack.

Once deployed, your service can be attached to an event-source. This means that when an event comes in, your service runs. nstack handles the orchestration of this execution and tunnels the event into your code (to a function which you exposed).

There are various event-sources which you can bind your service to. Some of them are third-party event-sources such as an existing Kafka or AMQP queue, whilst others are internal, nstack specific event-sources such as our time-based scheduler or HTTP Gateway. Either way, setting up your service to respond to an event is relatively simple, as nstack automates all messaging/middleware patterns.

[event] --(e)--> [nstack-service.yourFunction(e)]

.. toctree::
   

   client_libs
   scheduler
   http_gateway

