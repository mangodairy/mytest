---
title: "What is a Service?"
excerpt: "Make the application access to the external world through the Kubernetes service."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day6.png
  excerpt: "Make the application access to the external world through the Kubernetes service."
  show_overlay_excerpt: false
  show_date: true
sidebar:
  - title: "Introduction to Kubernetes"
    image: /assets/images/kuberneties/KubernetesSidebar.png
    image_alt: "logo"
    text: "Written by"
  - title: "Rajith P"
    text: "Introduction to Kubernetes"

tags:
  - table of contents
toc: true
toc_label: " "
toc_icon: "Introduction to Kubernetes"
toc_sticky: true
---

## What is a Service?

It allows exposing the application running on a pod/deployment to,
{: style="text-align: justify;"}
* To other pods.
* To an external use.
* Service types.

## Pictorial representation of a service 

<figure>
  <img src="/assets/images/kuberneties/Introduction/Pod/PictorialrepresentationService.png" alt="Image 1">
  <figcaption>Pictorial representation of a service (iptables proxy mode), from kubernetes.io </figcaption>
</figure>

## Service types

* ClusterIP (default type)
* NodePort
* LoadBalancer


## ClusterIP

We will be having some situations like we need the application container to reach the DB service. It happens within the cluster. We do not need the DB port to be accessible to the external user. In this scenario, we can make use of the clusterIP. Exposes the DB/application port to its internal IP. ClusterIP is the default ServiceType in Kubernetes.
{: style="text-align: justify;"}

## NodePort

The default Kubernetes service(ClusterIP) exposes the port within the cluster. What if we need access to the application outside? NodePort service makes the application visible outside the Kubernetes cluster.
You'll be able to contact the NodePort outside the cluster. You can specify the <NodeIP>:<NodePort> to access the exposed port.
NodePort ranges (typically 30000–32767 ) 
{: style="text-align: justify;"}
## LoadBalancer

In the NodePort service, the port will be listening to each node. We need a mechanism to distribute the external access to each node. This can be accomplished by keeping a network load balancer. The majority of the cloud providers provide LoadBalancer for Kubernetes without any additional configuration. That is what the service type 'LoadBalancer' does. Traffic from the external load balancer is directed to the backend pods. The cloud provider decides how it is load balanced.
{: style="text-align: justify;"}
## Service Definition file 

The structure of the service definition file looks like this.
{: style="text-align: justify;"}

```yaml
apiVersion: 
kind: 
metadata:
  name: 
spec:
  type: 
  selector:
    app: 
  ports:
    - port: 
      targetPort: 
      nodePort: 
```
**We will fill in the required information to create a service.**
{: style="text-align: justify;"}

```yaml
apiVersion: v1
kind: Service
metadata:
  name: test-service
spec:
  # By default the `service type ` is set to ClusterIp if it is not defined in the defenition file.
  type: NodePort
  selector:
    app: test-app
  ports:
      # By default the `targetPort` is set to the same value as the `port` field.
    - port: 80
      targetPort: 80
      # Optional field
      # By default Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
```
## Creating the Service 

```yaml
To create a service syntax is "kubectl create -f <service defenition file name >" 
```
We will create a service and have a look at it.
Created the same service defenition file in to our Kubernetes setup.
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ vi test-service.yaml
rajith@k8s-master:~$ cat test-service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: test-service
spec:
  # By default the `service type ` is set to ClusterIp if it is not defined in the defenition file.
  type: NodePort
  selector:
    app: test-app
  ports:
      # By default the `targetPort` is set to the same value as the `port` field.
    - port: 80
      targetPort: 80
      # Optional field
      # By default Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
rajith@k8s-master:~$ 
```

```markdown
rajith@k8s-master:~$ kubectl create -f test-service.yaml
service/test-service created
rajith@k8s-master:~$
```

Created the service.We will validate the service.

```markdown
rajith@k8s-master:~$ kubectl get svc 
NAME           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes     ClusterIP   10.96.0.1       <none>        443/TCP        24d
test-service   NodePort    10.99.149.101   <none>        80:30007/TCP   7s
rajith@k8s-master:~$ 
```

* We have two services running on this cluster.
* The service named 'kubernetes' is the default service coming along with the cluster deployment.
* The name of the service which we created is 'test-service '.
* It is a 'NodePort' service.
* It is listening to port 300007.

**In the next module we will have a demo with the below steps.**

* Creating a Kubernetes deployment with Nginx image.
* Verify the deployment details and try connecting to Nginx.
* Create a NodePort service to expose the Nginx application.
* Verify the Nginx connectivity after the service creation.
* Verify the details of the service and the nginx deployment.
* Delete the service and try accessing Nginx.
* Destroy the deployment.

[Next module, Service demo, click here](https://rajith.in/Kubernetes/KubernetesPart6_service-2/)
{: .notice--success}
{: style="text-align: justify;"}

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



