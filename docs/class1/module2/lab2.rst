Deploy the NGINX+ Ingress Controller
------------------------------------

This lab will deploy an NGINX+ Ingress Controller.

In the lab environment NGINX+ has been already built into an image and is
available in a private repository.  The deployment files have also been modified
to make use of the private repository when deploying NGINX+ Ingress Controller.

The following steps are adapted from: https://github.com/nginxinc/kubernetes-ingress/blob/master/docs/installation.md

Change directory into the "deployments" directory
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the K8S Master host you will need to change into the ``~/kubernetes-ingress/deployments/``
directory.

.. code:: shell

  $ cd ~/kubernetes-ingress/deployments/

Create NameSpace and Service Account
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The NGINX+ Ingress Controller runs in an isolated NameSpace and uses a separate 
ServiceAccount for accessing the Kubernetes API.  To create this namespace and
service account.

.. code:: shell

  $ kubectl apply -f common/ns-and-sa.yaml

Install Default SSL Cert/Key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
  
The Ingress Controller will use a "default" SSL certificate for requests that 
are not configured to use an explicit certificate.  The following loads the 
default certificate into Kubernetes.

.. code:: shell

  $ kubectl apply -f common/default-server-secret.yaml
  
.. NOTE:: from NGINX Docs "The default server returns the Not Found page with the 404 status code for all requests for domains for which there are no Ingress rules defined. For testing purposes we include a self-signed certificate and key that we generated. However, we recommend that you use your own certificate and key."

Create a NGINX ConfigMap
~~~~~~~~~~~~~~~~~~~~~~~~

NGINX+ makes use of a Kubernetes ConfigMap to store customizations to the NGINX+ 
configuration.

.. code:: shell

  $ kubectl apply -f common/nginx-config.yaml

Configure RBAC
~~~~~~~~~~~~~~

In this lab environment RBAC is enabled and you will need to enable access
from the NGINX Service Account to the Kubernetes API.

.. code:: shell

  $ kubectl apply -f rbac/rbac.yaml

.. NOTE:: The ``ubuntu`` user is accessing the Kubernetes Cluster as a "Cluster Admin" and has privileges 
          to apply RBAC permissions.

Create a Deployment
~~~~~~~~~~~~~~~~~~~

We will be deploying NGINX+ as a deployment.  It is also possible to deploy as 
a "daemonset" on every node (or subset).  

The following are Eric's opinion on the differences:

Advantages of deployment: flexible allocation (not limited to 1 per node).

Advantages of daemonset: fixed allocation (better if you want to expose port 80/443 directly)

.. code:: shell

  $ kubectl apply -f deployment/nginx-plus-ingress.yaml
  
.. NOTE:: The lab environment has modified ``nginx-plus-ingress.yaml`` and 
          created resources to support it.  Normally you **MUST** modify 
          this file before deploying.

Verify your deployment
~~~~~~~~~~~~~~~~~~~~~~

Make sure that everything is running.  Add ``-n`` to specify the correct
namespace.

.. code:: shell

  $  kubectl get po -n nginx-ingress
  
You should see output similar to:

.. code:: text 
  
  NAME                            READY   STATUS    RESTARTS   AGE
  nginx-ingress-56454fb6d-c5hl6   1/1     Running   0          44m
  
Expose NGINX+ via NodePort
~~~~~~~~~~~~~~~~~~~~~~~~~~

Finally we need to enable external access to the Kubernetes cluster.

In the previous lab we made use of a "Cluster" service that was only
accessible within the Kubernetes cluster.  We will create a NodePort
service to enable access from outside the cluster.  This will create
a ephemeral port that will map to port 80/443 on the NGINX+ Ingress
Controller.

.. code:: shell

  $ kubectl create -f service/nodeport.yaml

.. _retrieve_nodeport:
  
Retrieve Node Port 
~~~~~~~~~~~~~~~~~~

We will next retrieve the port number that NGINX+ port 80 is exposed at.

.. code:: shell

  $ kubectl get svc -n nginx-ingress

You should see output similar to (your port values will be different):

.. code:: shell

  ubuntu@kmaster:~/kubernetes-ingress/deployments$ kubectl get svc -n nginx-ingress
  NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
  nginx-ingress   NodePort   10.98.14.232   <none>        80:32148/TCP,443:30661/TCP   5m34s
  
In the example above port 32148 maps to port 80 on NGINX+.

.. NOTE:: You will have a different port value!  Record the value for the 
          next lab exercise.

Access NGINX+ From Outside the Cluster
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

From the Windows JumpHost open up the Chrome browser and go to:

  ``http://10.1.20.20:[Previous Recorded Port Number]``

You should see something like:

.. image:: /_static/class1-module2-lab2-nginx-plus-nodeport.png

.. NOTE:: You will have a different port value!
