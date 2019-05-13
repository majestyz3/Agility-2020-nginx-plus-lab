Installing NGINX Plus
--------------------------------------

This exercise will cover installation of NGINX Plus in a standalone (CentOS7) instance.
The UDF environment has three Nginx Plus instances -- Master, Plus2, and Plus3. 
To save time NGINX Plus has been installed on Plus2 and Plus3. In this lab we will install NGINX Plus on Master.

Repository Certificate and Key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Typically, a customer would log into the `NGINX Plus Customer Portal`_ and download thier ``nginx-repo.crt`` and ``nginx-repo.key`` files. 
These files are used to authenticate to the NGINX Plus repository in order to retrieve the NGINX Plus package for installation.  
For this lab the necessary cert and key have already been provided on the instance in **/etc/ssl/nginx**.

Install NGINX Plus
~~~~~~~~~~~~~~~~~~~~

.. note:: Execute these commands on the NGINX Plus Master Instance.

.. code:: shell

  sudo yum install -y ca-certificates && \
  sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/nginx-plus-7.repo && \
  sudo yum install -y nginx-plus && \
  sudo systemctl enable nginx.service && \
  sudo systemctl start nginx.service

These commands install a package CA SOMETHING, download the repository information from the NGINX customer portal, and install the NGINX Plus package.
The NGINX service is set to ``enable`` so it will start on boot and then started. NGINX should be running at this time.

Verify NGINX Plus is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. note:: Execute this command on the NGINX Plus Master Instance.

.. code:: shell

  systemctl status nginx.service

Verify the output shows the service running:

.. code:: shell

  ● nginx.service - NGINX Plus - high performance web server
   Active: active (running) since Fri 2019-05-10 12:08:14 UTC; 2min 18s ago

For an additional check, you should be able to curl to the NGINX default placeholder page:

.. code:: shell

  curl http://localhost

.. code:: shell

  NGINX DEFAULT PAGE 

  
NGINX Plus is now installed and running on the NGINX Plus Master instance.

.. _NGINX Plus Customer Portal: https://cs.nginx.com