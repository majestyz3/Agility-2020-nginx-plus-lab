Shared Memory Zone Synchronization
===========================================

In this module you will learn about synchronization of shared memory zones across clusters of NGINX Plus instances.
If several NGINX Plus instances are organized in a cluster, they can share some state data between them, including:

- [sticky learn session persistence](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
- [requests limiting](https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-http/#limit_req)
- [key-value storage](https://nginx.org/en/docs/http/ngx_http_keyval_module.html?&_ga=2.152343995.323579242.1557331203-116083997.1552344913#keyval_zone)


.. toctree::
   :maxdepth: 1
   :glob:

   lab*