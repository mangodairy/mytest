---
title: "Kubernetes  Architecture Control Plane Components"
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/IntroductiontoKubernetes.png
  excerpt: "Introduction to Kubernetes."
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

## Kubernetes Architecture
This figure gives you an illustration of Kubernetes architecture. It shows how a Kubernetes cluster with all the components tied together.
{: style="text-align: justify;"}

<figure>
	<a href="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"><img src="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"></a>
	<figcaption><a href="https://kubernetes.io/docs/concepts/overview/components/" title="Kubernetes Architecture, on kubernetes.io">Kubernetes Architecture, from kubernetes.io</a>.</figcaption>
</figure>

## Control Plane Components ( Master node )

* [kube-apiserver](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)
* [etcd](https://kubernetes.io/docs/concepts/overview/components/#etcd)
* [kube-scheduler](https://kubernetes.io/docs/concepts/overview/components/#kube-scheduler)
* [kube-controller-manager](https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager)

If you are interested to see the definition of each component please refer to the official documentation, for ease of access I have given the corresponding link.

**To see what it is and how it is implemented in the k8s cluster we will go through each component.**

## What is Kubernetes?

```markdown
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
k8s-master   Ready    control-plane,master   15d   v1.21.1
node-1       Ready    <none>                 15d   v1.21.1
node-2       Ready    <none>                 15d   v1.21.1
node-3       Ready    <none>                 15d   v1.21.1
rajith@k8s-master:~$ 
```
{: style="text-align: justify;"}

## Why Kubernetes?

The reason behind the adoption of Kubernetes is because of the below factors,
{: style="text-align: justify;"}

### Speed of deployment
 
Reduces the wait time minimal time delay between developers hand to Production.
{: style="text-align: justify;"}

### Quicker upgrade and rollback

An easy and quick way to upgrade and roll back the application. ( Version changes will not take much time ).
{: style="text-align: justify;"}

### Quicker recovery

If the application fails ( I mean in case of any pod failure ), Since Kubernetes deployment has a self-healing option on it, it recreates the failed pod.
{: style="text-align: justify;"}

## Kubernetes Architecture

This figure gives you an illustration of Kubernetes architecture. It shows how a Kubernetes cluster with all the components tied together.

### Kubernetes Architecture Diagram
{: style="text-align: justify;"}

<figure>
	<a href="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"><img src="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"></a>
	<figcaption><a href="https://kubernetes.io/docs/concepts/overview/components/" title="Kubernetes Architecture, on kubernetes.io">Kubernetes Architecture, from kubernetes.io</a>.</figcaption>
</figure>


## Kubernetes Components

### Control Plane Components ( Master node )

* [kube-apiserver](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)
* [etcd](https://kubernetes.io/docs/concepts/overview/components/#etcd)
* [kube-scheduler](https://kubernetes.io/docs/concepts/overview/components/#kube-scheduler)
* [kube-controller-manager](https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager)

### Worker Node Components

* [kubelet](https://kubernetes.io/docs/concepts/overview/components/#kubelet) 
* [kube-proxy](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)
* [Container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime)


## Kubernetes in action.

**We will take this scenario**
1. Our developer developed a microservice.
2. We will start 4 containers with this docker image.
3. Place an internal load balancer in front of these containers.
4. After starting we understood 4 images are not sufficient for serving the traffic.
5. We will scale it to 6.
6. Place a public load balancer in front of these containers.
7. Today is new year day traffic spikes.
8. Scale the number of containers now, we will increase it to 10.
9. Our Dev team added new fetchers to the application.
10. The business wanted to implement it now since it is the new year day.
11. We will implement the change  by Keep serving the existing  requests during the upgrade without any interruption to the end-user




<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



