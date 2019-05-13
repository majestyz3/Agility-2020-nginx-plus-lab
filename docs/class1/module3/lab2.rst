Lab – Configuration Synchronization
-----------------------------------------

The ``listen`` directives in the example server blocks do not specify IP addresses -- only ports. One way to ensure we have common shared memory zones across
Nginx Plus instances n=in our cluster is to synchronize the configuration. Nginx provides a package/script for this.

.. image:: /_static/nginx-sync-sh.png

.. NOTE:: Prior to lab ssh access (via keys) to all cluster mamebers was configured for the NGINX Plus Master.

Create the configuration file ``/etc/nginx-sync.conf`` on the master machine.

.. note:: Execute these steps on the NGINX Plus Master Instance.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx-sync.conf' <<EOF
    NODES="plus2.nginx-udf.internal plus3.nginx-udf.internal"
    CONFPATHS="/etc/nginx/nginx.conf /etc/nginx/conf.d"
    EXCLUDE="default.conf"
    EOF

Run nginx-sync.sh on the master to push configuration in ``CONFPATHS`` to the ``NODES``, omitting configuration files named in ``EXCLUDE``.

.. note:: Execute these steps on the NGINX Plus Master Instance.

.. code:: shell

    sudo yum install -y nginx-sync && \
    sudo nginx-sync.sh

Answer the ECDSA key fingerprint prompts if necessary. 
Verify the configuration has been synchronized and is running with curl or the browser on the Windows Jump Host.
For example:

.. code:: shell

    curl http://plus2.nginx-udf.internal

This request should resolve to the server block with ``default_server``.

