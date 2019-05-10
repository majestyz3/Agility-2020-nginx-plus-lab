Installing NGINX Plus
--------------------------------------

This exercise will cover installation of NGINX Plus in a standalone (CentOS7) instance.

**Perform these tasks from the NGINX Plus Master instance.**

Repository Certificate and Key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Typically, a customer would log into the NGINX Plus Customer Portal and download thier nginx-repo.crt and nginx-repo.key files. 
These files are used to authenticate to the repository in order to retrieve the NGINX Plus package for installation.  
For this lab the necessary cert and key have already been provided on the instance in **/etc/ssl/nginx**.

Install Prerequisites
~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo yum install ca-certificates

Retrieve and Add the Repository
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/nginx-plus-7.repo

Repository URLs will vary depending on your OS and package manager.

Install the NGINX Plus Package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo yum install nginx-plus

Enable NGINX Plus to Start on Boot
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo systemctl enable nginx.service

Verify NGINX Plus is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The command:

.. code:: shell

  systemctl status nginx.service

Output should contain text similiar to:

.. code:: shell

  ● nginx.service - NGINX Plus - high performance web server
   Active: active (running) since Fri 2019-05-10 12:08:14 UTC; 2min 18s ago

Additionally, you should be able to curl to the default placeholder page:

.. code:: shell

  curl http://localhost

.. code:: shell

  NGINX DEFAULT PAGE 

  
NGINX Plus is now installed and running on the NGINX Plus Master instance.