Installing Nginx Plus
--------------------------------------

This exercise will cover installation of Nginx Plus in a standalone (CentOS7) instance.
The UDF environment has three Nginx Plus instances -- Master, Plus2, and Plus3. 
To save time Nginx Plus has been installed on Plus2 and Plus3. In this lab we will install Nginx Plus on Master.

Repository Certificate and Key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Typically, a customer would log into the `Nginx Plus Customer Portal`_ and download thier ``nginx-repo.crt`` and ``nginx-repo.key`` files. 
These files are used to authenticate to the Nginx Plus repository in order to retrieve the Nginx Plus package for installation.  
For this lab the necessary cert and key have already been provided on the instance in **/etc/ssl/nginx**.

Install Nginx Plus
~~~~~~~~~~~~~~~~~~~~

.. note:: Execute these commands on the Nginx Plus Master instance.

.. code:: shell

  sudo yum install -y ca-certificates && \
  sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/nginx-plus-7.repo && \
  sudo yum install -y nginx-plus && \
  sudo systemctl enable nginx.service && \
  sudo systemctl start nginx.service

These commands install Certificate Authorities certificates, download the repository information from the Nginx customer portal, and install the Nginx Plus package.
The Nginx service is set to ``enable`` to start on boot. The last command starts the service. Nginx should be running at this time.

Verify Nginx Plus is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Check the service status from systemd.**

.. note:: Execute this command on the Nginx Plus Master instance.

.. code:: shell

  systemctl status nginx.service

**Verify the output shows the service running:**

.. code:: shell

  ● nginx.service - Nginx Plus - high performance web server
   Active: active (running) since Fri 2019-05-10 12:08:14 UTC; 2min 18s ago

For an additional check, you should be able to curl to the Nginx default placeholder page:

.. code:: shell

  curl http://localhost

.. code:: shell

  Nginx DEFAULT PAGE 

  
Nginx Plus is now installed and running on the Nginx Plus Master instance.

.. _Nginx Plus Customer Portal: https://cs.nginx.com