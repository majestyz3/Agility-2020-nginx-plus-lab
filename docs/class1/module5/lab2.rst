Scaling the Example Service
-----------------------------------------

This lab tests service discovery by scaling the sd-demo service.

Service Scaling
~~~~~~~~~~~~~~~~~~~~

.. note:: Execute these steps on the Docker Host instance.

.. code:: shell

    $ cd ~/udf-nginx-plus-service-discovery
    $ docker-compose up --scale http=5 -d

The ``docker-compose`` command instructs docker to scale the ``http`` service to 5 instances.

**Watch the ``sd-demo`` upstream in the dashboard.**

.. todo:: insert screenshot once names are updated.

 Scale the ``http`` service up or down as desired.

Query the SRV Record
~~~~~~~~~~~~~~~~~~~~

In this example, Nginx Plus performs service discovery by querying the following records.

**Query the SRV records manually.**

.. note:: Execute these commands on the Docker Host or Nginx Plus Master instance.

.. code:: shell

    $ dig @docker.nginx-udf.internal -p 8600 http.service.consul ANY
    $ dig @docker.nginx-udf.internal -p 8600 consul.node.dc1.consul

