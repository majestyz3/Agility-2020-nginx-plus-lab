Nginx Plus Caching
-------------------

Nginx and Nginx Plus are commonly deployed as a caching tier between a load balancing tier and the application server tier.

.. image:: /_static/cache-flow.png

This lab explores a simple caching configuration. Detailed information about caching configuration directives can be found here_. 
In the lab's simple example, Nginx Plus will cache content by respecting the ``Cache-Control`` header in the response.
Content with a ``Cache-Control`` header containing a ``max-age=0`` will not be cached by default.

Add a cache
~~~~~~~~~~~

**Update the configuration to define a proxy_cache and a method for purging the cache.**

.. note:: Execute this command on the NGINX Plus Master instance.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx/conf.d/labApp.conf' <<EOF
    proxy_cache_path /tmp/cache keys_zone=f5AppCache:10m inactive=60m;

    map $request_method $purge_method {
        PURGE 1;
        default 0;
    }

    server {
        listen 80 default_server;
        server_name f5-app.nginx-udf.internal bigip-app.nginx-udf.internal;
        error_log /var/log/nginx/f5App.error.log info;  
        access_log /var/log/nginx/f5App.access.log combined;
        status_zone f5App;
        add_header X-Lab-NGINX $hostname;

        location / {
            proxy_pass http://f5App;
            health_check match=f5_ok;

            proxy_cache f5AppCache;
            proxy_cache_purge $purge_method;
        }
    }

    server {
        listen 80;
        server_name nginx-app.nginx-udf.internal;
        error_log /var/log/nginx/nginxApp.error.log info;  
        access_log /var/log/nginx/nginxApp.access.log combined;
        status_zone nginxApp;

        location /text {
            proxy_pass http://nginxApp-text;
            health_check match=nginx_ok;
        }
        location / {
            proxy_pass http://nginxApp;
            health_check match=nginx_ok;
        }
    }
    EOF

.. note:: Reload the Nginx Configuration (``sudo nginx -t && sudo nginx -s reload``)

This configurartion defines a ``proxy_cache_path`` directory on the local file system, a shared memory zone where hashed keys for cached items are stored, and an inactivity timeout.
The ``proxy_cache`` is referenced in the server block. Additionally, method for purging the cache (sending a request with an HTTP verb of ``PURGE``) is defined.

Several methods for signaling a cache purge could be created -- a magic header, requests from a certian IP address, etc.
In production usage, the ability to make a cache invalidating request should be protected in a suitable manner. This lab provides no security around cache invalidating requests.


Invalidating Cached items
~~~~~~~~~~~~~~~~~~~~~~~~~

**Purge a single item from the cache.**

.. note:: Execute these commands from the NGINX Plus Master instance.

.. code:: shell

    curl -v -X PURGE http://f5-app.nginx-udf.internal/f5-logo.png

**Purge the entire associated cache.**

.. code:: shell

    curl -v -X PURGE http://f5-app.nginx-udf.internal/*

An expected HTTP response code for cache invalidating requests is "*HTTP/1.1 204 No Content*".

.. _here: https://docs.nginx.com/nginx/admin-guide/content-cache/content-caching/

