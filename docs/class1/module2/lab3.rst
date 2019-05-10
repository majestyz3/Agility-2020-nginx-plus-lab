Deploy the "Cafe" Application
-----------------------------

In this lab we will deploy the "Cafe" application behind NGINX+.

This is adapted from:

https://github.com/nginxinc/kubernetes-ingress/tree/master/examples/complete-example

Once the application is deployed we will be able to observe how NGINX+ is being
used to load balance the traffic.

Deployment
~~~~~~~~~~

On the K8S Master host go to the ``~/kubernetes-ingress/examples/complete-example/`` 
directory.

.. code:: shell

  $ cd ~/kubernetes-ingress/examples/complete-example/
  
In the previous lab you should have already completed the following, but re-run
to make sure that everything is all set.

.. code:: shell

  $ kubectl apply -f cafe.yaml
  
Load Certs and Keys
~~~~~~~~~~~~~~~~~~~

NGINX+ Controller can load certificates and keys that are stored in a 
Kubernetes Secret.  To load the cert/key:

.. code:: shell

  $ kubectl create -f cafe-secret.yaml

Load Ingress Resource
~~~~~~~~~~~~~~~~~~~~~

Kubernetes has a standard "Ingress" resource that is used to describe content
routing rules like "cafe.example.com/tea" goes to the "tea-svc".

Take a look at the contents of ``cafe-ingress.yaml``.

.. code:: YAML

  ...
  rules:
  - host: cafe.example.com
    http:
      paths:
      - path: /tea
        backend:
          serviceName: tea-svc
          servicePort: 80
  ...

Now deploy the Ingress resource.

.. code:: shell

  $ kubectl create -f cafe-ingress.yaml

Verify Service
~~~~~~~~~~~~~

Retrieve the port that is used for 443 traffic to NGINX+ using steps similar to :ref:`retrieve_nodeport` to test
that the service is up.  On the K8S Master node run the following ``curl``
command (you will need to specify the port number for your environment).

.. code:: shell

 $ curl --resolve cafe.example.com:[SSL Port Number]:10.1.20.20 https://cafe.example.com:[SSL Port Number]/coffee -k

You should see results similar to:

.. code:: shell

  ubuntu@kmaster:~/kubernetes-ingress/examples/complete-example$ curl --resolve cafe.example.com:30661:10.1.20.20 https://cafe.example.com:30661/coffee -k
  Server address: 10.244.2.84:80
  Server name: coffee-bbd45c6-6ptzj
  Date: 09/May/2019:18:34:55 +0000
  URI: /coffee
  Request ID: 591de144555f865fbbc4cc29e80b18dd
  ubuntu@kmaster:~/kubernetes-ingress/examples/complete-example$ curl --resolve cafe.example.com:30661:10.1.20.20 https://cafe.example.com:30661/coffee -k
  Server address: 10.244.1.91:80
  Server name: coffee-bbd45c6-blhck
  Date: 09/May/2019:18:40:32 +0000
  URI: /coffee
  Request ID: 54cb1d2d839d34a1bb241423f911350b
