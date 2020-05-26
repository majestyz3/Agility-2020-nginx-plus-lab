Scaling the Example Service
-----------------------------------------

This lab tests service discovery by scaling the sd-demo service on the Docker Host.

Service Scaling
~~~~~~~~~~~~~~~~~~~~

.. note:: Execute this command from the Docker Host instance.

.. code:: shell

    docker-compose -f ~/udf-nginx-plus-service-discovery/docker-compose.yml up -d --scale http=5

The ``docker-compose`` command instructs docker to scale the ``http`` service to 5 instances.

**Watch the "sd-demo" upstream in the dashboard.**

.. image:: /_static/sd-upstream-full.png
   :width: 500pt

Scale the ``http`` service up or down as desired.

Query the SRV Record
~~~~~~~~~~~~~~~~~~~~

In this example, NGINX Plus performs service discovery by querying the following records.

**Query the SRV records manually.**

.. note:: Execute these commands on the Docker Host or NGINX Plus Master instance.

.. code:: shell

    $ dig @docker.nginx-udf.internal -p 8600 http.service.consul ANY
    $ dig @docker.nginx-udf.internal -p 8600 consul.node.dc1.consul

