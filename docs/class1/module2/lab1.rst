Lab – Base Configuration
-----------------------------------------

This lab provides multiple containers of two example web applications running on **Docker Host**. These containers will be used as *upstreams* or *pool members* throughout the lab.

.. code:: shell

  [centos@ip-10-1-1-5 ~]$ docker ps --format "{{.Image}}\t{{.Names}}\t\t{{.Ports}}"
  f5-demo-app-lab:latest	f5App3		443/tcp, 0.0.0.0:8082->80/tcp
  f5-demo-app-lab:latest	f5App2		443/tcp, 0.0.0.0:8081->80/tcp
  f5-demo-app-lab:latest	f5App1		443/tcp, 0.0.0.0:8080->80/tcp
  nginxdemos/hello	nginxApp1		0.0.0.0:8083->80/tcp
  nginxdemos/hello	nginxApp3		0.0.0.0:8085->80/tcp
  nginxdemos/hello	nginxApp2		0.0.0.0:8084->80/tcp

Throughout the lab NGINX Plus configuration will be deployed directly from bash. In order to prevent tedious work in a text editor, the lab provides bash
commands using concatenation and redirection. These can simply be copy and pasted.

.. NOTE:: Frequently in the lab you will be asked to "reload the NGINX configuration". Use the following pattern:
.. code:: shell

  sudo nginx -t
  sudo nginx -s reload

The first command checks the configuration syntax. The second command reloads the configuration. 


Create the Base Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: 
  
  sudo bash -c 'cat > /etc/nginx/conf.d/labExample.conf' <<EOF
  upstream f5App { 
      server docker.nginx-udf.internal:8080;  
      server docker.nginx-udf.internal:8081;  
      server docker.nginx-udf.internal:8082;
  }

  server {
      listen 80;
      root /usr/share/nginx/html;
      error_log /var/log/nginx/LabApp.error.log info;  
      access_log /var/log/nginx/LabApp.access.log combined;

      location / {
          proxy_pass http://f5App;
      }
  }
  EOF

.. TODO:: Upstreams, Server block, location, proxy_pass discussion, lack of selection access_log

Test the site in the browser on your Jump host. The default selection algorithm is round-robin. Notice the upstream member address changing which subsequent requests.

Add Selection Algorithm, Weight
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo bash -c 'cat > /etc/nginx/conf.d/labExample.conf' <<EOF
  upstream f5App { 
      least_conn;
      server docker.nginx-udf.internal:8080 weight=10;  
      server docker.nginx-udf.internal:8081 weight=5;  
      server docker.nginx-udf.internal:8082 weight=5;
  }

  server {
      listen 80;
      root /usr/share/nginx/html;
      error_log /var/log/nginx/LabApp.error.log info;  
      access_log /var/log/nginx/LabApp.access.log combined;

      location / {
          proxy_pass http://f5App;
      }
  }
  EOF

Add Second Upstream, a Second Location, Shared Memory Zone
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
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

  server {
      listen 80;
      root /usr/share/nginx/html;
      error_log /var/log/nginx/LabApp.error.log info;  
      access_log /var/log/nginx/LabApp.access.log combined;
      status_zone default;

      location /f5/ {
          proxy_pass http://f5App/;
      }
      location /nginx/ {
          proxy_pass http://nginxApp/;
      }
  }
  EOF

In NGINX, weights are managed independently by each worker process. NGINX Plus uses a shared memory segment for upstream data 
(configured with the zone directive), so weights are shared between workers and traffic is distributed more accurately.

Location discussion -- trailing slash



