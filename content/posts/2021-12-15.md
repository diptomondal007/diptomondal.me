---
title: Nodeport, TargetPort, Port
date: 2021-12-15 13:42:00
tags:
    - kubernetes
categories:
    - tech
keywords:
    - kubernetes
    - nodeport
    - target port
    - port
---

Noobies like me get confused about what are nodeport , targetport and port and what are their usecases. **Let's start by an example -**

Let's assume you have written a simple server in golang which is running on port - **3000**.

Now, to deploy this server in kuberenetes you need containerize this with docker and in your docker file you would probably be used this line
```
EXPOSE 3000
```
That means you container is exposing this port as your server is running on **3000**.

Ok cool. Now you have deployed the application and defined a service.yml file to create a service let A to logically abstract the pods of your application.

Now if you want to expose your app to the outside world you need a port defined for your node because there may be several services running so you need to define a unique port in which node will listen for your service A. This port is NodePort. And whenever the nodeport will be hit by request from outside world, the traffic will be routed to the service's Port. So then the service port will route the traffic to the pod's Target Port. The Port is basically to be used as internal port of the service. So if another pods of another services want to communicate with you service A's pod they will call the service as A:Port.

### Summary:

* NodePort: The port on the node where external traffic will come in
* Port: The port of the service
* TargetPort: The port of the pod(s) to forward traffic to.