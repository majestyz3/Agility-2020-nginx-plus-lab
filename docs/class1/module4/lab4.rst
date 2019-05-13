Dynamic Upstream Configuration
------------------------------

Changes to upstream servers can be made through the Dashboard or through the API. 
*Performing an upstream change through the Dashboard or API only update the shared memory zone -- configuration files are not updated.*
Reloading NGINX when the shared memory state is not consistent with the configuration files will cause a loss of state.
Nginx Plus provides a mechanism for keeping upstream state between reloads.

state /var/lib/nginx/state/appservers.conf;

.. note:: Execute this step on the NGINX Plus Master Instance.

.. code:: 

    sudo bash -c 'cat > /etc/nginx/conf.d/labUpstream.conf' <<EOF
    upstream f5App { 
        least_conn;
        zone f5App 64k;
        state /var/lib/nginx/state/f5App.conf;

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

.. note:: Reload the Nginx Configuration (```sudo nginx -t && sudo nginx -s reload```)

Interacting with the State File
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The ``state`` directive defines a state file. The state file can be modified only with configuration commands from the API interface. 

**Using requests in the "Update Upstream" folder of the Postman collection, manipulate the state of the f5App upstream.**

.. image:: /_static/upstreamupdate.png

**Using the Dashboard, Remove one or more upstream servers from the ``nginxApp`` upstream group**

.. image:: /_static/morestate.png

.. image:: /_static/noservers.png

.. note:: Reload the Nginx Configuration (``sudo nginx -t && sudo nginx -s reload``)

Go back to the Dashboard and check the status of the ``nginxApp`` upstream group.




