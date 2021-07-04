---
title: "Pod definition file."
excerpt: "In this module, we will go through the basics of pods and understand the structure of pod definition."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day4.png
  excerpt: "Understand the Pod definition file."
  show_overlay_excerpt: false
  show_date: true
sidebar:
  - title: "Walking through the pods"
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

## Kubernetes pods vs containers 

Pod, container, docker is the most common interchanged word in the Kubernetes world. So before going to the technical definition, we will clearly define what it is.

Logically a container is packed inside a pod in a Kubernetes cluster. A pod is a covering of one or more containers. A pod can have multiple containers running on it on a Kubernetes cluster.

Another common misunderstanding about the docker. What is docker? For better clarity, I am adding two words after docker.
{: style="text-align: justify;"}

* Docker image.
* Docker container.

The issue begins from our understanding of docker. Some of us consider the docker image and docker container are the same. Some of us don't know where to draw the line between them. And even some of us think the docker and Pod are the same. Don't worry, I had all this confusion at the beginning. We will clarify it.  

So let us start from the base. The flow looks like this.
{: style="text-align: justify;"}

Docker image -> Docker container -> Pods 

## Docker image

Docker images are read-only templates. If you are familiar with the Vmware template, you can relate it to the docker image. It is a mini OS template. Basically, a docker image is a file. It doesn't need any computing power ( CPU or memory ). Docker images are available/kept in,
{: style="text-align: justify;"}

* Docker hub. 
* Google container registry.
* Almost all the cloud providers option to create a docker registry.
* You can keep it in your private registry.
* Or directly in your system.

You can decide this based on where you are hosting the Kubernetes cluster, based on the compliance policy of your organisation, complexity of your environment etc...
{: .notice--info}
{: style="text-align: justify;"}

## Docker containers

Docker containers are runnable instances of a docker image. It requires CPU, memory and other computing resources to run.
{: style="text-align: justify;"}

> It is a mini VM. Since it called a microservice may be, we can call it a micro virtual machine :).
{: style="text-align: justify;"}

## Pods
In simple words, a pod is a box that contains one or more containers. Pod share the filesystem. Even if you have multiple containers, it has only a single IP address for external communication. The communication within the pods between the containers happens through the localhost. Pods are the smallest deployable units of computing that we can create and manage in Kubernetes. 
{: style="text-align: justify;"}


## Pod definition file 

Kubernetes definition file contain four top-level fields.
{: style="text-align: justify;"}

```yaml
apiVersion: 
kind: 
metadata:
 
Spec:
```
How do we get this information? 🤔 
{: style="text-align: justify;"}
Execute the command, **kubectl explain pod** you will get the details.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ kubectl explain pod
KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

FIELDS:
   apiVersion	<string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind	<string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata	<Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec	<Object>
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status	<Object>
     Most recently observed status of the pod. This data may not be up to date.
     Populated by the system. Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

rajith@k8s-master:~$ 
```
From the above command, we got ,

**KIND:     Pod**
{: style="text-align: justify;"}
**VERSION:  v1**
{: style="text-align: justify;"}

```yaml
apiVersion: v1
kind: Pod
```

What about the other field metadata and spec? It is specified there but, it is not clear. 
{: style="text-align: justify;"}

**metadata**  [We will follow the link given in the command output. 🏃🏼](https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata)
{: style="text-align: justify;"}

The detailed explanation is available under the given link. But do we need all of those? No, we will take only the required fields from it. 
{: style="text-align: justify;"}


* Name:- A string that uniquely identifies this object within the current namespace. 
* Namespace:- It is to isolate the objects in a Kubernetes environment. We will talk about the namespace more in another series.
* Labels:- A map of string keys and values that can be used to organize and categorize objects. It is to identify the 'object' in the different categories. For example, web servers can be labelled with "type: web", the production servers can be labelled with "env: prod".  
{: style="text-align: justify;"}

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
  labels:
      type : web
      env : prod  
```

**spec** Now we came to the important part of the definition file 'spec'.
{: style="text-align: justify;"}

It is the specification of the container inside the pod.
We need to specify the name of the container, the image used to create the container. And if need additional parameters like command, arguments and a few more parameters can be added here. If you have multiple containers within the pod, then each container specification should be mentioned here. 
{: style="text-align: justify;"}
```yaml
Spec:
    # The container specification section starts here
   containers:
   - name: my-first-pod
     image: nginx
    # The container specification section ends here
```

We will form a pod definition by combining all the above details and create a pod definition file. 

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
  labels:
      type : web
      env : prod  
spec:
    # The container specification section starts here
   containers:
   - name: my-first-pod
     image: nginx
    # The container specification section ends here
```

{: .notice--success}
{: style="text-align: justify;"}

[Our next module is on deployment, Click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>     

 [Info Button Text](https://mangodairy.github.io/devsite/Kubernetes/#kubernetes-in-7-days-in-a-week-){: .btn .btn--info}
 




