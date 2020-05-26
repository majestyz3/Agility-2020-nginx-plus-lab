Lab – Service Discovery Setup
-----------------------------------------

NGINX Plus can perform service discovery in several different ways. 

- `DNS based`_

- `Consul APIs`_

- `etcd`_

- `ZooKeeper`_

This lab uses Hashicorp's `Consul`_ to capture service state (with the help of `Registrator`_).
NGINX Plus queries Consul for DNS SRV records to determine appropriate upstream servers for a specified service.

Docker Configuration
~~~~~~~~~~~~~~~~~~~~

All necessary components for Consul, Registrator, and an example app are defined in a docker-compose file on the ``Docker Host``.
The Registrator container is listening to the docker unix socket for service changes.
Service state is then reported to Consul.

**Create the Consult and Registrator Containers**

.. note:: Execute this command from the Docker Host instance.

.. code:: shell

    docker-compose -f ~/udf-nginx-plus-service-discovery/docker-compose.yml up -d --scale http=0

On the ``Windows Jump Host`` use the Chrome bookmark to view the Consul web interface.

.. image:: /_static/consul1.png
   :width: 600pt

The service used in this demo is named ``http``. There are currently no instances of that service instantiated -- thus it does not appear in the service list.

NGINX Plus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

**Define the upstream, health check, and server block needed for the demo.**

.. note:: Execute this command on the NGINX Plus Master instance.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx/conf.d/sdDemo.conf' <<EOF
    resolver docker.nginx-udf.internal:8600 valid=2s;
    resolver_timeout 2s;

    upstream sd-demo {
        zone upstream_sd_demo 64k;
        server service.consul service=http resolve;
    }

    match 200OK {
        status 200;
    }

    server {
        listen 80;
        server_name sd-demo.nginx-udf.internal;
        status_zone sd-demo;

        location / {
            proxy_pass http://sd-demo;
            health_check interval=2s match=200OK;
        }
    }
    EOF

.. note:: Reload the NGINX Configuration (``sudo nginx -t && sudo nginx -s reload``)

View Upstream in Dashboard
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Find the Upstream in the Dashboard named "sd-demo".**

.. image:: /_static/sd-demo-upstream.png

There are no upstream servers defined at this time (as there are no service instances).

.. _`DNS based`: https://www.nginx.com/blog/dns-service-discovery-nginx-plus/
.. _`Consul APIs`: https://www.nginx.com/blog/service-discovery-with-nginx-plus-and-consul/
.. _`etcd`: https://www.nginx.com/blog/service-discovery-nginx-plus-etcd/
.. _`ZooKeeper`: https://www.nginx.com/blog/service-discovery-nginx-plus-zookeeper/
.. _`Consul`: https://hub.docker.com/_/consul
.. _`Registrator`: https://hub.docker.com/r/gliderlabs/registrator