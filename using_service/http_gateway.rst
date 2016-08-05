.. _http_gateway:

nstack HTTP Gateway
=====================

This section will explore attaching your service to the 'http' event, which is the nstack HTTP Gateway. This lets you call your service over HTTP.

Say we have a service which we have deployed which is called gateway-test:
**TODO: build a demo HTTP service too**
**TODO: all how do i specify remote vs. local vm?**

.. code:: bash

	$ nstack ls | grep gateway-test
	[nstack] gateway-test, leo-anthias, Mon April 29 14:03

I can attach this service to an HTTP Gateway as follows:

.. code :: bash

 $ nstack trigger gateway-test http
 [Success] Successfully attached service gateway-test to event 'http', id is [a2aa1b] 
 [Success] Your service is now live at http://nstack-vm/services/gateway-test

.. note:: by default this exposes all of the functions which you specified in your IDL. To expose a single function, you can pass the argument 'entry=myFunc'

If I wanted to double-check it is running 

.. code :: bash

 $ nstack ps | grep http
 [a2aa1b] [http (http://nstack-vm/services/gateway-test)], gateway-test, Leo Anthias

Now I can call a function via HTTP PUT.

.. code:: bash

    $ curl -H 'Content-Type: application/json' -XPUT http://nstack-vm/services/gateway-test -d '{ "method" : "Add", "params" : [1,2] }'
    { result : 3 }

To remove the HTTP Gateway, we can simply:

.. code :: bash

    $ nstack untrigger gateway-test http
    [nstack] Success: removed trigger 'http' for gateway-test 
    $ curl -H 'Content-Type: application/json' -XPUT http://nstack-vm/services/gateway-test -d '{ "method" : "Add", "params" : [1,2] }'
    { error : 'gateway-test does not exist or has not exposed an HTTP interface'}

****
Client Libraries
****

The http trigger is special in nstack because it's also the default way to accomplish RPC with the :ref:`client_libs`. The client libraries allow you to easily call your function in various languages by dynamically mapping your functions. To learn more, please see the client library documentation.

You may also want to refer to the :ref:`http-api`. 