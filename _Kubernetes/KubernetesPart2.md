---
title: "Kubernetes  Architecture Control Plane Components"
excerpt: "In this module, we will play with the master node and the pods running on it."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day2.png
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
{: style="text-align: justify;"}
## We will Walk Through the Server

We have a 3 node cluster, to list the master and associated worker node execute the command "**kubectl get nodes**".
### List all the nodes in the cluster
```markdown
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES                  AGE   VERSION
k8s-master   Ready    control-plane,master   15d   v1.21.1
node-1       Ready    <none>                 15d   v1.21.1
node-2       Ready    <none>                 15d   v1.21.1
node-3       Ready    <none>                 15d   v1.21.1
rajith@k8s-master:~$ 
```


Use "**kubectl cluster-info**" command to display the addresses of the control plane and the cluster services. This will give an idea on which IP the API server bounded to and which port it is listening etc.
### Display the addresses of the master node and services
```yaml
rajith@k8s-master:~$ kubectl cluster-info
Kubernetes control plane is running at https://192.168.50.10:6443
CoreDNS is running at https://192.168.50.10:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
rajith@k8s-master:~$ 

```


### List the Control Plane Components

We had a quick look at the server. Now let us have a look at the control-plane components. 

**Before that, we will understand a few concepts and commands.**
If you find it hard to understand the below points, don't worry. Skip those points and go on. These are here to give an overview of the command combination we used below. We are discussing it in detail later.
{: .notice--info}
{: style="text-align: justify;"}

* The command 'kubectl get pods' display the pods running on a k8s cluster.
* The Kubernetes environment is further isolated with a concept called a namespace. We will have a detailed study on it later.
* The objects running on a particular namespace can be listed with -n < namespace > along with the suitable commands.
* The control-plane components are running as a pod in the master node.
* There is a concept called static pods.
* The control-plane components making use of this method to start.
* The static pod will be suffixed with the node name in which they started.
* The control plane components are placed under the namespace called 'kube-system' .


We got all the details to filter the control-plane components. 
We will form the command from it.

```yaml
kubectl get pods -n kube-system  |grep master
```
Will execute it on a server and see the result.

```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep master
etcd-k8s-master                      1/1     Running   7          15d
kube-apiserver-k8s-master            1/1     Running   7          15d
kube-controller-manager-k8s-master   1/1     Running   7          15d
kube-scheduler-k8s-master            1/1     Running   7          15d
rajith@k8s-master:~$ 
```
Now go back and look at the name of the components we discussed and compare it with the above output.

* [kube-apiserver](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)
* [etcd](https://kubernetes.io/docs/concepts/overview/components/#etcd)
* [kube-scheduler](https://kubernetes.io/docs/concepts/overview/components/#kube-scheduler)
* [kube-controller-manager](https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager)

Ya it is same , we can see all the control-plane components are running on master node under the namespace kube-system. Thats what we explained with the eight points :) . 

[ Next we will see, The worker Node Components, click here](https://rajith.in/Kubernetes/KubernetesPart3/)

{: style="text-align: justify;"}



<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



