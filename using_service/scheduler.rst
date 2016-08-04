.. _scheduler:

nstack Time-Based Scheduling
=====================

Perhaps the simplest way to run an nstack service is to schedule it to run at a certain time. For instance, you can tell your service to run at 5 O'Clock on Mondays, or to run every second. This functionality is aimed at replacing other scheduling systems such as cron.

To schedule an existing service to run at a certain time, the format is:

**TODO-ed**

.. code:: bash

 $ nstack trigger my-service schedule=0::0::1 entry=myFunction
 [Success] Successfully scheduled service, id is [a3bd1a] 

This would invoke myFunction with no arguments. If you want to invoke a function with arguments (say a basic adding function), you can:

.. code:: bash

 $ nstack trigger my-service schedule=0::0::1 entry=add args=(1, 3)
 [Success] Successfully scheduled service, id is [a3bd1a] 

To list existing scheduled tasks:

.. code:: bash

 $ nstack ps | grep schedule
 [a3bd1a] [schedule (:0::0::1)], my-service, Leo Anthias

To get logs from your service, you can use the logs function:

.. code:: bash

 $ nstack logs | grep a3bd1a
 [a3bd1a:1 logs] 29-Apr-2016 20:01 Successfully ran my-service.myFunction
 [a3bd1a:2 logs] 29-Apr-2016 20:02 Successfully ran my-service.add
 [Logs] Service invoked at 20:01 with arguments (1,3)
 [Logs] STDOUT: 'Result is 4'
 [Logs] Service exited with code 0 at 20:01


And to remove an existing scheduled task:

.. code:: bash

 $ nstack kill a3bd1a
 [Success] Successfully killed service [a3bd1a] 
