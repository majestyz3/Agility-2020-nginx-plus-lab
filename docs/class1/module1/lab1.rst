Deploying an Application in Kubernetes
--------------------------------------

This exercise will cover the basics of deploying an application in Kubernetes.

Deploy an application
~~~~~~~~~~~~~~~~~~~~~

From the K8S Master node.

.. code:: shell

  kubectl run coffee --image=nginxdemos/hello:plain-text --port=80 --replicas=2 --labels="app=coffee"

You can also opt to run

.. code:: shell

  kubectl create -f - <<'EOF'
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    name: coffee
  spec:
    replicas: 2
    selector:
      matchLabels:
        app: coffee
    template:
      metadata:
        labels:
          app: coffee
      spec:
        containers:
        - name: coffee
          image: nginxdemos/hello:plain-text
          ports:
          - containerPort: 80
  EOF
  
These are both the same commands.  The second method is more commonly how you would deploy a service (from a YAML file), but it is possible to create a deployment using the CLI as well.

Verify your application is running
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Run the following command to see a list of pods that are running.

.. code:: shell

  kubectl get po
  
You should see output similar to:

.. code:: shell

  ubuntu@kmaster:~$ kubectl get po
  NAME                                    READY   STATUS    RESTARTS   AGE
  coffee-bbd45c6-6ptzj                    1/1     Running   0          2m18s
  coffee-bbd45c6-blhck                    1/1     Running   0          2m18s

Add the "-o wide" command to see the pod IP addresses

.. code:: shell

  kubectl get po -o wide
  
Expected output:

.. code:: shell
  
  ubuntu@kmaster:~$ kubectl get po -o wide
  NAME                                    READY   STATUS    RESTARTS   AGE     IP            NODE      NOMINATED NODE   READINESS GATES
  coffee-bbd45c6-6ptzj                    1/1     Running   0          3m11s   10.244.2.84   knode2    <none>           <none>
  coffee-bbd45c6-blhck                    1/1     Running   0          3m11s   10.244.1.91   knode1    <none>           <none>
  
.. NOTE:: Observe that the pods are running on separate nodes (knode1 and knode2) and that the IP addresses are outside the routable range of the environment (in UDF it would be 10.1.0.0/16).

Use the ``curl`` command to test whether your application is running.

.. code:: shell
  
  curl [pod IP]
  
For example (your POD IP address will be different)

.. code:: shell

  ubuntu@kmaster:~$ curl 10.244.2.84
  Server address: 10.244.2.84:80
  Server name: coffee-bbd45c6-6ptzj
  Date: 09/May/2019:14:42:33 +0000
  URI: /
  Request ID: 8f7bfd37fdc6b4b24403c92d196494be
  
Congratulations you have deployed an application!