Lab – Configure Zone Synchronization
-----------------------------------------

Each NGINX instance in a cluster needs to be configured to listen and exchange data with other cluster members. We need to add the cluster configuration to the to level ``stream`` block.

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

Push the config to the the other cluster members again.

.. code:: shell

    nginx-synx.sh

::CHECK IF WE ARE CLUSTERED::
:: DO SOME CONFIG TO USE THE CLUSTER ::
sticky learn function
