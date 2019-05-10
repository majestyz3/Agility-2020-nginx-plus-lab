Exposing an Application Service in Kubernetes
------------------------------------------------

In the previous lab we deployed an application.  In this lab we will walk
through how to expose the services outside the Kubernetes cluster.

Expose a Cluster Service
~~~~~~~~~~~~~~~~~~~~~~~~

In the last lab exercise we were able to reach a service by connecting to
the Pod IP address.  We will first look at using "kube-proxy" the built-in
Kubernetes load balancer to access the service.

Using ``kubectl`` run.

.. code:: shell

  kubectl expose deployment/coffee --name coffee-svc

Now run:

.. code:: shell

  kubectl get svc
  
.. NOTE:: "svc" is short for "service".  You could also type it out fully, but commonly "svc" is used.

You should see

.. code:: shell

  ubuntu@kmaster:~$ kubectl get svc
  NAME                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
  coffee-svc              ClusterIP   10.107.15.241    <none>        80/TCP           78s
  ...
  
Note that by default it has created a service of type ``ClusterIP``. This means
that it is only accessible inside the Kubernetes cluster using the "Cluster" IP 
address.

It also creates a local DNS record.  You can view this record by running nslookup
from one of your coffee pods (optional exercise).

For example:

.. code:: shell

  ubuntu@kmaster:~$ kubectl exec -it coffee-bbd45c6-6ptzj -- nslookup coffee-svc
  nslookup: can't resolve '(null)': Name does not resolve

  Name:      coffee
  Address 1: 10.107.15.241 coffee-svc.default.svc.cluster.local
  
Verify that you can access the coffee service via the Cluster IP.

From the K8S Master run.

.. code:: shell
  
  curl [Cluster IP]
  
Example output (Your Cluster IP will be a different value)

.. code:: shell

  ubuntu@kmaster:~$ curl 10.107.15.241
  Server address: 10.244.1.91:80
  Server name: coffee-bbd45c6-blhck
  Date: 09/May/2019:15:05:09 +0000
  URI: /
  Request ID: cf3070d1aba245d56a7d8e62838876b1
  ubuntu@kmaster:~$ curl 10.107.15.241
  Server address: 10.244.2.84:80
  Server name: coffee-bbd45c6-6ptzj
  Date: 09/May/2019:15:05:10 +0000
  URI: /
  Request ID: 16749709f45d6d50a49b2be9d340517f

.. NOTE:: Note that the "Server address" that is reported by the coffee app 
          changes between requests.

Coffee and Tea Service
~~~~~~~~~~~~~~~~~~~~~~

In the previous steps we've replicated part of the examples from: 
https://github.com/nginxinc/kubernetes-ingress/blob/master/examples/complete-example/cafe.yaml

We will now "apply" the complete example that includes a "tea" service.  
You can also run this step if you are instructed to "skip ahead" by your
instructor.

.. code:: shell

  kubectl apply -f https://raw.githubusercontent.com/nginxinc/kubernetes-ingress/master/examples/complete-example/cafe.yaml

Copies of these files are also on the K8S Master node.  Example output (running against local copy of the files).

.. code:: shell

  ubuntu@kmaster:~$ kubectl apply -f ~/kubernetes-ingress/examples/complete-example/cafe.yaml
  Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
  deployment.extensions/coffee configured
  Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
  service/coffee-svc configured
  deployment.extensions/tea created
  service/tea-svc created
  