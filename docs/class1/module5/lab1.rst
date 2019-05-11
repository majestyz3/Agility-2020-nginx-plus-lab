Lab – Service Discovery Setup
-----------------------------------------

NGINX Plus can perform service discovery in several different ways. 
.. TODO:: provide link/more info
In the this demo we're using Hashicorp's Consul to capture service state (with the help of Registrator).
NGINX Plus will then query Consule for a DNS SRV record to determine appropiate upstream servers.

Docker Configuration
~~~~~~~~~~~~~~~~~~~~

All necessary components for Consul and Registrator are already configured on the **Docker Host**.

View Consul Services
~~~~~~~~~~~~~~~~~~~~

http://docker.nginx-udf.internal:8500


NGINX Plus Configuration
~~~~~~~~~~~~~~~~~~~~~~~~

Recall that /dashboard.html was already exposed in an earlier lab. We'll use the dashboard to monitor the state and number of discovered upstreams.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx/conf.d/app.conf' <<EOF
    resolver docker.nginx-udf.internal:8600 valid=2s;
    resolver_timeout 2s;

    upstream sd_demo {
        zone upstream_sd_demo 64k;
        server service.consul service=http resolve;
    }

    match 200_status {
        status 200;
    }

    server {
        listen 8008;
        server_name sd-demo.nginx-udf.internal;
        status_zone sd-demo;

        location / {
            proxy_pass http://sd_demo;
            health_check interval=2s match=200_status;
        }
    }
    EOF

View Upstream in Dashboard
~~~~~~~~~~~~~~~~~~~~~~~~~~

