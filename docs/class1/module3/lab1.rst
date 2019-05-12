Lab – Configure Zone Synchronization
-----------------------------------------

Each NGINX instance in a cluster needs to be configured to listen and exchange data with other cluster members. We need to add the cluster configuration to the to level ``stream`` block 
(notice this is being cacatencated to the end of /etc/nginx/nginx.conf). NGINX Plus instances can exchange state data with all other members in a cluster, provided that the shared memory zone has the same name on all cluster members.

.. code:: shell
    
    sudo bash -c 'cat >> /etc/nginx/nginx.conf' <<EOF
    stream {

        server {
        listen 9000;

        zone_sync;
        zone_sync_server master.nginx-udf.internal:9000;
        zone_sync_server plus2.nginx-udf.internal:9000;
        zone_sync_server plus3.nginx-udf.internal:9000;
        }
    }
    EOF

The nginx proc must be restarted for the zone sync to initiate.

.. code:: shell 

    sudo systemctl restart nginx

