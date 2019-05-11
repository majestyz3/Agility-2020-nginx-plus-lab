Lab – Scaling the Example Service
-----------------------------------------

Now that service discovery is configured for our example service, let's scale the service and monitor the changes to the upstream.

Service Scaling
~~~~~~~~~~~~~~~~~~~~

SSH to **Docker host** to perform this step.

.. code:: shell

    $ cd ~/udf-nginx-plus-service-discovery
    $ docker-compose up --scale http=5

Monitor Service Scaling
~~~~~~~~~~~~~~~~~~~~~~~~

Watch the ``sd_demo`` upstream in the dashboard.

Query the SRV Record
~~~~~~~~~~~~~~~~~~~~

Here are the DNS recrods being returned which NGINX Plus parsed into upstream configuration.

.. code:: shell

    $ dig @docker.nginx-udf.internal -p 8600 http.service.consul ANY
    $ dig @docker.nginx-udf.internal -p 8600 consul.node.dc1.consul

