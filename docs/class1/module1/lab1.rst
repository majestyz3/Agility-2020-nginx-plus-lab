Installing NGINX Plus
--------------------------------------

This exercise will cover installation of NGINX Plus in a standalone (CentOS7) instance.

**Perform these tasks from the NGINX Plus Master instance.**

Repository Certificate and Key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Typically, a customer would log into the NGINX Plus Customer Portal and download thier nginx-repo.crt and nginx-repo.key files. 
These files are used to authenticate to the repository in order to retrieve the NGINX Plus package for installation.  
For this lab the necessary cert and key have already been provided on the instance in **/etc/ssl/nginx**.

Install NGINX Plus
~~~~~~~~~~~~~~~~~~~~

.. code:: shell

  sudo yum install -y ca-certificates && \
  sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/nginx-plus-7.repo && \
  sudo yum install -y nginx-plus && \
  sudo systemctl enable nginx.service && \
  sudo systemctl start nginx.service

TALK THROUGH WHAT EACH STEP IS DOING

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