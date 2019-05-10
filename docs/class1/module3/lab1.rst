Lab – Configuration Synchronization
-----------------------------------------

All NGINX Plus instances can exchange state data with all other members in a cluster, provided that the shared memory zone has the same name on all cluster members.
Recall that our *listen* directive do not specify IP addresses -- only ports. One simple way to ensure we have common shared memory zones across
our NGINX Plus instances is to synchronize the configuration in the cluster. NGINX provides a pakcage/script for this.

.. image:: /_static/nginx-sync-sh.png

.. NOTE:: Prior to lab the the nginx-sync package was installed on the master and the master was granted ssh access (using keys) to all cluster members.

Create the configuration file ``/etc/nginx-sync.conf`` on the master machine.

.. code:: shell

    sudo bash -c 'cat > /etc/nginx-sync.conf' <<EOF
    NODES="plus2.nginx-udf.internal plus3.nginx-udf.internal"
    CONFPATHS="/etc/nginx/nginx.conf /etc/nginx/conf.d"
    EXCLUDE="default.conf"
    EOF

Run nginx-sync.sh on the master to push configuration in ``CONFPATHS`` to the ``NODES``, omitting configuration files named in ``EXCLUDE``.

.. code:: shell

    nginx-synx.sh

Answer the ECDSA key fingerprint prompts if necessary. Verify the configuration has been synchronized and is running with curl or the browser on the Windows Jump Host.

.. code:: shell

    curl http://plus2.nginx-udf.internal

