Nginx Plus Caching
-------------------

Nginx and Nginx 


Add a cache
~~~~~~~~~~~

.. code:: shell

  sudo bash -c 'cat > /etc/nginx/conf.d/labExample.conf' <<EOF
  proxy_cache_path /tmp/cache keys_zone=labCache:10m inactive=60m;

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
          proxy_cache labCache;
      }
      location /nginx/ {
          proxy_pass http://nginxApp/;
          health_check match=nginx_ok;
          proxy_cache labCache;
      }
  }
  EOF


CACHE INVALIDATION REQUEST
curl -X PURGE http://f5-app.nginx-udf.internal/{{some_content}}
curl -X PURGE http://f5-app.nginx-udf.internal/*




