Lab – Share Zone information Across the Cluster
-----------------------------------------------

Nginx Plus can perform runtime state sharing within a cluster for the following directives:

- `sticky learn`_
- `request limit`_
- `key value storage`_

Configure Sticky Learn 
~~~~~~~~~~~~~~~~~~~~~~

Nginx Plus offers a persistence method where cookies are learned from Set-Cookie headers in HTTP responses. 
The F5 demo app sets a session cookie called ``_nginxPlusLab`` with a 30 second expiry. Using ``sticky learn`` Nginx will persist this client to the container where the original request (with no cookie) was load balanced.
Using runtime state sharing, this persistence information can be shared across the cluster.


.. note:: Execute these steps on the NGINX Plus Master Instance.

.. code:: 

    sudo bash -c 'cat > /etc/nginx/conf.d/labUpstream.conf' <<EOF
    upstream f5App { 
        least_conn;
        zone f5App 64k;
        server docker.nginx-udf.internal:8080;  
        server docker.nginx-udf.internal:8081;  
        server docker.nginx-udf.internal:8082;

        sticky learn
        create=$upstream_cookie__nginxPlusLab
        lookup=$cookie__nginxPlusLab
        timeout=1h
        zone=client_sessions:1m sync;
    }

    upstream nginxApp { 
        least_conn;
        zone nginxApp 64k;
        server docker.nginx-udf.internal:8083;  
        server docker.nginx-udf.internal:8084;  
        server docker.nginx-udf.internal:8085;
    }

    upstream nginxApp-text {
        least_conn;
        zone nginxApp 64k;
        server docker.nginx-udf.internal:8086;  
        server docker.nginx-udf.internal:8087;  
        server docker.nginx-udf.internal:8088;
    }
    EOF

Verify Runtime State Sharing
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

From the Windows Jump Host, open a new browser tab then open Chrome Developer tools.

.. image:: /_static/developer.png

Click on the ``BIG-IP App`` bookmark. This requests hits a BIG-IP virtual server. A round robin pool configured with the 3 Nginx Plus instances is attached.
This response should have the Set-Cookie header for the ``_nginxPlusLab`` cookie. Take note of the ``X-Lab-NGINX`` and ``X-Lab-Origin`` headers.
These headers show which Nginx instance the BIG-IP load balanced the request to and which docker container Nginx Plus selected from the upstream.

.. image:: /_static/request1.png

Refresh the page.

You should notice the Nginx Plus instance (``X-Lab-NGINX``) changing while the Origin container stays the same.
This is because each Nginx Plus instance in the cluster has the necessary persistence information from ``sticky learn`` to make the correct load balancing decision.

.. image:: /_static/request1.png

.. image:: /_static/request1.png




.. _`sticky learn`: https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky
.. _`request limit`: https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-http/#limit_req
.. _`key value storage`: https://docs.nginx.com/nginx/admin-guide/security-controls/blacklisting-ip-addresses/