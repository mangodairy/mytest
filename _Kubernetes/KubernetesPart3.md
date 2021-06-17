---
title: "Kubernetes  Architecture Worker Node Components"
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/IntroductiontoKubernetes.png
  excerpt: "Kubernetes  Architecture Worker Node Components."
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
In the previous chapter, we went through the [control-plane components.](https://mangodairy.github.io/mytest/Kubernetes/KubernetesPart2/) In this chapter, let's go through the worker node components.
{: style="text-align: justify;"}

<figure>
	<a href="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"><img src="https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg"></a>
	<figcaption><a href="https://kubernetes.io/docs/concepts/overview/components/" title="Kubernetes Architecture, on kubernetes.io">Kubernetes Architecture, from kubernetes.io</a>.</figcaption>
</figure>

## Worker Node Components

* [kubelet](https://kubernetes.io/docs/concepts/overview/components/#kubelet)
* [kube-proxy](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)
* [Container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime)

If you are interested to see the definition of each component please refer to the official documentation, for ease of access I have given the corresponding link.

**To see what it is and how it is implemented in the k8s cluster we will go through each component.**
{: style="text-align: justify;"}
## We will Walk Through the Server

### List the Control Plane Components

In the previous chapter, before filtering the master node components we went through a few concepts and commands hope you remember those point if not, [please refresh it.  ](https://mangodairy.github.io/mytest/Kubernetes/KubernetesPart2/#list-the-control-plane-components)

**If you remeber those points we will add few more points here to understand it better**

* Like pod, there is another 'kind' available in k8s called daemonset.
* It can be listed with the command  "kubectl get daemonsets".
* There is an option available with the get command that is "-o wide " with the help of it we will get more details of the given command.

### [kube-proxy](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)

kube-proxy is a network proxy that runs on each cluster node including the master node, each node will have one set of kube-proxy running on it. This is achieved through daemonset which will discuss later in detail. Since it is maintained with the help of daemonset if we add additional nodes to the cluster we no need to deploy the kube-proxy on that node daemonset will take care of it and add a new pod in the node.

Now we have the required details to filter only the kube-proxy from the cluster. 
We will make the command. It is ,

**kubectl get pods -n kube-system  |grep master**

Will execute it on a server and see the result.

{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep kube-proxy
kube-proxy-86f72                     1/1     Running   8          16d
kube-proxy-lpvws                     1/1     Running   7          16d
kube-proxy-n9phj                     1/1     Running   7          16d
kube-proxy-vkdvx                     1/1     Running   7          16d
```
In the command output you can see there are four kube-proxy runnings on this cluster, it is a four-node cluster including the master node. But how we will see the pods are distributed across the nodes?

You remember we were discussing an option "-o wide" we will try it with the above command to see how the output will look like.
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system -o wide  |grep kube-proxy
kube-proxy-86f72                     1/1     Running   8          16d   192.168.50.11   node-1       <none>           <none>
kube-proxy-lpvws                     1/1     Running   7          16d   192.168.50.13   node-3       <none>           <none>
kube-proxy-n9phj                     1/1     Running   7          16d   192.168.50.12   node-2       <none>           <none>
kube-proxy-vkdvx                     1/1     Running   7          16d   192.168.50.10   k8s-master   <none>           <none>
rajith@k8s-master:~$ 
```
See the 7th and 8th column you can see the server IP address and the hostname respectively. Please have a close look then you will notice that each kube-proxy pod is running on each node of the cluster. That is what we discussed sometime back.
{: style="text-align: justify;"}

Now go back and look at the name of the components we discussed and compare it with the above command output.

* [kube-apiserver](https://kubernetes.io/docs/concepts/overview/components/#kube-apiserver)
* [etcd](https://kubernetes.io/docs/concepts/overview/components/#etcd)
* [kube-scheduler](https://kubernetes.io/docs/concepts/overview/components/#kube-scheduler)
* [kube-controller-manager](https://kubernetes.io/docs/concepts/overview/components/#kube-controller-manager)

Ya it is same , we can see all the control-plane components are running on master node under the namespace kube-system. Thats what we explained with the eight points :) . 

{: style="text-align: justify;"}



<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



