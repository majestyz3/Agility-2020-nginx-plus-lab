Lab - Health Checks
--------------------------

For passive health checks, NGINX and NGINX Plus monitor transactions as they happen, and try to resume failed connections. If the transaction still cannot be resumed, NGINX open source and NGINX Plus mark the server as unavailable and temporarily stop sending requests to it until it is marked active again.

The conditions under which an upstream server is marked unavailable are defined for each upstream server with parameters to the server directive in the upstream block:

.. code:: shell

  upstream backend {
      server backend1.example.com;
      server backend2.example.com max_fails=3 fail_timeout=30s;
  }

Active Health Checks
~~~~~~~~~~~~~~~~~~~~
NGINX Plus can periodically check the health of upstream servers by sending special health‑check requests to each server and verifying 
the correct response.

To enable active health checks:

In the location that passes requests (proxy_pass) to an upstream group, include the *health_check* directive. 

.. code:: shell

  server {
      location / {
          proxy_pass http://backend;
          health_check;
      }
  }

By default, every five seconds NGINX Plus sends a request for “/” to each server in the backend group. 
If any communication error or timeout occurs (the server responds with a status code outside the range from 200 through 399) the health check fails. The server is marked as unhealthy, and NGINX Plus does not send client requests to it until it once again passes a health check. To allow the worker processes to use the same set of counters to keep track of responses from the servers in an upstream group use a shared memory zone (``zone``).

*health_check* supports the following parameters:
- port
- interval
- fails
- passes
- uri
- match

  
Defining Custom Conditions
~~~~~~~~~~~~~~~~~~~~~~~~~~
You can set custom conditions that the response must satisfy for the server to pass the health check. 
The conditions are defined in a ``match`` block, which is referenced in the match parameter of the health_check directive.

.. code:: shell

  http {
      #...
      match server_ok {
          # tests are here
      }
  }

Refer to the block from the health_check directive by specifying the *match* parameter and the name of the match block.

.. code:: shell

  sudo bash -c 'cat > /etc/nginx/conf.d/labExample.conf' <<EOF
  upstream f5App { 
      least_conn;
      zone f5App 64k;
      server docker.nginx-udf.internal:8080;  
      server docker.nginx-udf.internal:8081;  
      server docker.nginx-udf.internal:8082;
  }

  upstream nginxApp { 
      least_conn;
      zone nginxApp 64k;
      server docker.nginx-udf.internal:8083;  
      server docker.nginx-udf.internal:8084;  
      server docker.nginx-udf.internal:8085;
  }

  match f5_ok {
      status 200;
  }

  match nginx_ok {
      status 200-399;
      body !~ "maintenance mode";
  }

  server {
      listen 80;
      root /usr/share/nginx/html;
      error_log /var/log/nginx/LabApp.error.log info;  
      access_log /var/log/nginx/LabApp.access.log combined;
      status_zone default;

      location /f5/ {
          proxy_pass http://f5App/;
          health_check match=f5_ok;
      }
      location /nginx/ {
          proxy_pass http://nginxApp/;
          health_check match=nginx_ok;
      }
  }
  EOF

.. image:: /_static/class1-module2-lab2-nginx-plus-nodeport.png

.. NOTE:: You will have a different port value!
