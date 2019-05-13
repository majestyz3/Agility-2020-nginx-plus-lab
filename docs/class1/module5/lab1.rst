Lab – Service Discovery Setup
-----------------------------------------

NGINX Plus can perform service discovery in several different ways. 

- `DNS based`_

- `Consul APIs`_

- `etcd`_

- `ZooKeeper`_

This lab uses Hashicorp's Consul to capture service state (with the help of Registrator).
NGINX Plus will then queries Consul for a DNS SRV record to determine appropiate upstream servers.

Docker Configuration
~~~~~~~~~~~~~~~~~~~~

All necessary components for Consul and Registrator are already configured on the ``Docker Host``. The Registrator container is listening to the docker unix socket for service changes.
Service state is then reported to Consul. 

On the ``Windows Jump Host`` use the Chrome bookmark to view the Consul web interface.

.. image:: /_static/consul.png
   :width: 600pt

The service used in this demo is named ``http``.

.. image:: /_static/consul_service.png
   :width: 350pt

NGINX Plus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

**Define the upsteam, health check, and server block needed for the demo.**

.. note:: Execute this command on the NGINX Plus Master instance.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx/conf.d/sdDemo.conf' <<EOF
    resolver docker.nginx-udf.internal:53 valid=2s;
    resolver_timeout 2s;

    upstream sd-demo {
        zone upstream_sd_demo 64k;
        server service.consul service=http resolve;
    }

    match hello {
        status 200;
        header Content-Type = text/html;
        body ~ "Hello";
    }

    server {
        listen 80;
        server_name sd-demo.nginx-udf.internal;
        status_zone sd-demo;

        location / {
            proxy_pass http://sd-demo;
            health_check interval=2s match=hello;
        }
    }
    EOF

.. note:: Reload the Nginx Configuration (``sudo nginx -t && sudo nginx -s reload``)

View Upstream in Dashboard
~~~~~~~~~~~~~~~~~~~~~~~~~~

**Find the Upstream in the Dashboard named "sd-demo".**

.. todo:: insert pic once names are updated.

There are no upstream servers defined at this time.

.. _`Consul APIs`: https://www.nginx.com/blog/service-discovery-with-nginx-plus-and-consul/
.. _`etcd`: https://www.nginx.com/blog/service-discovery-nginx-plus-etcd/
.. _`ZooKeeper`: https://www.nginx.com/blog/service-discovery-nginx-plus-zookeeper/

.. todo:: add link for generic DNS based upstream