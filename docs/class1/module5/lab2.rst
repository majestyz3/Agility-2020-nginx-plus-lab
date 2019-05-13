Scaling the Example Service
-----------------------------------------

Service discovery is configured for the ``SD-demo`` App. This lab tests service discovery by scaling the demo service.

Service Scaling
~~~~~~~~~~~~~~~~~~~~

.. note:: Execute these steps on the Docker Host Instance.

.. code:: shell

    $ cd ~/udf-nginx-plus-service-discovery
    $ docker-compose up --scale http=5 -d

Watch the ``sd-demo`` upstream in the dashboard. Scale the ``http`` service up or down as desired.

Query the SRV Record
~~~~~~~~~~~~~~~~~~~~

In this example, Nginx Plus performs service discovery by querying the following records.

.. code:: shell

    $ dig @docker.nginx-udf.internal -p 8600 http.service.consul ANY
    $ dig @docker.nginx-udf.internal -p 8600 consul.node.dc1.consul

