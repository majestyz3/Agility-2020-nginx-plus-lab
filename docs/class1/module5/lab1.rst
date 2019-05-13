Lab – Service Discovery Setup
-----------------------------------------

NGINX Plus can perform service discovery in several different ways. 

Service Discovery for NGINX Plus Using Consul APIs
Service Discovery for NGINX Plus with etcd
Service Discovery for NGINX Plus with ZooKeeper


.. TODO:: provide link/more info
In the this demo we're using Hashicorp's Consul to capture service state (with the help of Registrator).
NGINX Plus will then query Consule for a DNS SRV record to determine appropiate upstream servers.

Docker Configuration
~~~~~~~~~~~~~~~~~~~~

All necessary components for Consul and Registrator are already configured on the ``Docker Host``. The Registrator container is listening to the docker unix socket for service changes.
Service state is then reported to Consul. 

On the ``Windows Jump Host`` use the Chrome bookmark to view the Consul web interface.

.. image:: /_static/consul.png

.. image:: /_static/consul_service.png

The service used in this demo is named ``http``.
 

NGINX Plus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: Execute these steps on the NGINX Plus Master Instance.

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

.. note:: Reload the Nginx Configuration (```sudo nginx -t && sudo nginx -s reload```)

View Upstream in Dashboard
~~~~~~~~~~~~~~~~~~~~~~~~~~


