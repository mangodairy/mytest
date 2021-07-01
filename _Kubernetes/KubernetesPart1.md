---
title: "Introduction to Kubernetes "
excerpt: "In this module, we are covering the basic structure of Kubernetes."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day1.png
  excerpt: "Introduction to Kubernetes."
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

## Introduction
Kubernetes is a container orchestrator which automates container-based application deployment. It also helps in the auto-scaling of container-based workloads with the help of pods. It is taking over the market mainly because of the below features.
{: style="text-align: justify;"}

* Speed of deployment
* Quicker upgrade and rollback 
* Quicker recovery 
{: style="text-align: left;"}
There are many attractive features included in Kubernetes we will go through it.
Kubernetes is also known as K8s the abbreviation k8s came because in the word Kubernetes the letter 'k' followed by 8 letters ( that is ubernete ) and it ends with s. 
It is,
{: style="text-align: justify;"}
*	K - ubernete - s 
*	k -  8       - s 
*	k    8         s
*	k8s
{: style="text-align: left;"}
## Prerequisite to start Kubernetes?

When you are looking into Kubernetes from an infrastructure perspective you should have a basic understanding of the below-listed items. Without the basic knowledge of this, your journey will be difficult.
{: style="text-align: justify;"}
* Basic understanding of Docker.
* Familiar with Linux command line operations.
* Basic idea about the networking concepts.
{: style="text-align: left;"}
## What is Kubernetes?

It is a container orchestration tool, hope you are already familiar with docker if not, please have some basic understanding of docker before getting into the Kubernetes. Like Kubernetes we have  Mesos and docker swarm which can act similar to a Kubernetes cluster, the word cluster is not the right fit here, for ease of understanding I am using the word cluster however the right fit is container orchestrator.
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

I will add the demo in another page and provide the link here . I am also planning a video  for the same but it will take some time .


[Our next module,Kubernetes Architecture Control Plane Components,click here](https://rajith.in/Kubernetes/KubernetesPart2/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



