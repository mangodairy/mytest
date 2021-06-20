---
title: "Kubernetes  Architecture Worker Node Components"
excerpt: "In this module, we will play with the worker node, pods and the other components running on it."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day3.png
  excerpt: "Kubernetes  Architecture Worker Node Components."
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

### List the Worker Node Components

In the previous chapter, before filtering the master node components we went through a few concepts and commands hope you remember those points.If not, [please refresh it.  ](https://mangodairy.github.io/mytest/Kubernetes/KubernetesPart2/#list-the-control-plane-components)
{: .notice--info}
{: style="text-align: justify;"}

**If you remeber those points we will add few more points here to understand it better.**

* Like pod, there is another 'kind' available in k8s called daemonset.
* It can be listed with the command  "kubectl get daemonsets".
* There is an option available with the get command that is "-o wide " with the help of it we will get more details of the given command.

### [kube-proxy](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)

kube-proxy is a network proxy that runs on each cluster node including the master node, each node will have one set of kube-proxy running on it. This is achieved through daemonset which will discuss later in detail. Since it is maintained with the help of daemonset if we add additional nodes to the cluster we no need to deploy the kube-proxy on that node daemonset will take care of it and add a new pod in the node.

Now we have the required details to filter only the kube-proxy from the cluster.We will make the command.
{: .notice--info}
{: style="text-align: justify;"}

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
{: .notice--info}
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system -o wide  |grep kube-proxy
kube-proxy-86f72                     1/1     Running   8          16d   192.168.50.11   node-1       <none>           <none>
kube-proxy-lpvws                     1/1     Running   7          16d   192.168.50.13   node-3       <none>           <none>
kube-proxy-n9phj                     1/1     Running   7          16d   192.168.50.12   node-2       <none>           <none>
kube-proxy-vkdvx                     1/1     Running   7          16d   192.168.50.10   k8s-master   <none>           <none>
rajith@k8s-master:~$ 
```
See the 6th and 7th column you can see the server IP address and the hostname respectively. Please have a close look, you will notice that each kube-proxy pod is running on each node of the cluster. That is what we discussed sometime back.
{: .notice--info}
{: style="text-align: justify;"}

But with the above command we listed only pod then what is 'daemonset'? . These pods are created with the help of a 'daemonset' we will see that.
We will execute the command which we discussed as the second point. *"kubectl get daemonsets"*

```markdown
rajith@k8s-master:~$ kubectl get daemonsets -n kube-system 
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-proxy   4         4         4       4            4           kubernetes.io/os=linux   16d
weave-net    4         4         4       4            4           <none>                   16d
rajith@k8s-master:~$ 
```
You can see that there are two 'daemonset' out of which one is 'kube-proxy' that's what we are looking for. The other one 'weave-net' we will discuss it shortly.

### [kubelet](https://kubernetes.io/docs/concepts/overview/components/#kubelet)

Kubelet is an agent that runs on each node in the cluster. It makes sure that containers are running. Unlike other components, kubelet is running as a service in each server. Let's have a look at it.

```yaml
rajith@node-1:/$ systemctl status kubelet 
● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running) since Fri 2021-06-18 02:22:22 UTC; 18min ago
       Docs: https://kubernetes.io/docs/home/
   Main PID: 643 (kubelet)
      Tasks: 15 (limit: 1072)
     Memory: 101.7M
     CGroup: /system.slice/kubelet.service
             └─643 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin=cni --p>

Warning: some journal files were not opened due to insufficient permissions.
rajith@node-1:/$
```
This is a Linux command, the warning shows because I am running it as a noon-root user. Ignore it for now.
{: .notice--info}
{: style="text-align: justify;"}


### [Container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime)

The container runtime is the software that is responsible for running containers. Kubernetes supports several container runtimes like Docker,containerd,CRI-O etc. In my current setup I am using docker as container runtime. Like, kubelet docker also running a Linux service. Let’s have a look at it.

```yaml
root@node-1:~# systemctl status docker
● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Fri 2021-06-18 02:22:32 UTC; 43min ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 711 (dockerd)
      Tasks: 45
     Memory: 147.4M
     CGroup: /system.slice/docker.service
             └─711 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.330389076Z" level=info msg="Removing stale sandbox c33554adc2ded4e6e5c2a6fd4176e3c0e452d80c9890f05abd618fd37917b48e (d5ede5c9fa3b8dead09c31>
Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.348166166Z" level=warning msg="Error (Unable to complete atomic operation, key modified) deleting object [endpoint 26bacfffb1e7aa18137470eb>
Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.400646592Z" level=info msg="Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a>
Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.514334977Z" level=info msg="Loading containers: done."
Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.913785532Z" level=info msg="Docker daemon" commit=8728dd2 graphdriver(s)=overlay2 version=20.10.6
Jun 18 02:22:31 node-1 dockerd[711]: time="2021-06-18T02:22:31.917075618Z" level=info msg="Daemon has completed initialization"
Jun 18 02:22:32 node-1 systemd[1]: Started Docker Application Container Engine.
Jun 18 02:22:32 node-1 dockerd[711]: time="2021-06-18T02:22:32.012748800Z" level=info msg="API listen on /run/docker.sock"
Jun 18 02:22:43 node-1 dockerd[711]: time="2021-06-18T02:22:43.962407443Z" level=info msg="ignoring event" container=4e04444cb20e992e6ecf041ed433c8816f26ffa088a66d97f61b1d5752d13db3 module=libcontainerd>
Jun 18 02:23:08 node-1 dockerd[711]: time="2021-06-18T02:23:08.525253826Z" level=warning msg="Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without sw>
root@node-1:~#
```
Now I ran this command as the root user, you can see the previous warning has gone and lots of other message and warning came, at this stage, we are not much worried about those messages and warnings.
{: .notice--info}
{: style="text-align: justify;"}


## Addons

The components already discussed are the core components needed for Kubernetes to work, there are few more components needed for the smooth functioning of  Kubernetes we will go through it.
{: style="text-align: justify;"}

### [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns)
Even though the DNS is considered as addons this is one of the mandatory requirements. You can choose your own DNS, by default [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns) come along with the "kubeadm" implementation. As the name implies it is for DNS, I think it doesn't need an explanation.

#### Pod associated with CoreDNS
```markdown
#Pod associated with CoreDNS#

rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep dns
coredns-558bd4d5db-8t95j             1/1     Running   6          12d
coredns-558bd4d5db-mtq96             1/1     Running   2          3d7h
```
#### Deployment associated with CoreDNS
```markdown
#Deployment associated with CoreDNS#
rajith@k8s-master:~$ kubectl get deployments.apps  -n kube-system  
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           18d
```
#### Service associated with CoreDNS
```markdown
#Service associated with CoreDNS#

rajith@k8s-master:~$ kubectl get service -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   18d
rajith@k8s-master:~$ 
```
This is how the CoreDNS is implemented on a cluster, 

* Two pods are running to service this.
* It is controlled by a deployment named "coredns".
* It is made available to the cluster through the service named "kube-dns".

We will discuss deployment and service in upcoming modules.
{: .notice--info}
{: style="text-align: justify;"}

### The Kubernetes Networking Model
Kubernetes doesn't have its own networking model it uses third party implementation however, Kubernetes imposes the following fundamental requirements on any networking implementation (barring any intentional network segmentation policies):
* Pods on a node can communicate with all pods on all nodes without NAT
* Agents on a node (e.g. system daemons, kubelet) can communicate with all pods on that node
Note: For those platforms that support Pods running in the host network (e.g. Linux)
* Pods in the host network of a node can communicate with all pods on all nodes without NAT.
{: style="text-align: justify;"}
There are many varieties of network implementation for Kubernetes. [ Please refer this link for details](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

Here we used ["weave-net"](https://www.weave.works/oss/net/).

*We will go through the cluster to understand the implementation.*

We have four pods running with 'weave-net' image.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep weave-net
weave-net-46pcs                      2/2     Running   22         18d
weave-net-dsw4n                      2/2     Running   20         18d
weave-net-slfx4                      2/2     Running   21         18d
weave-net-t4g45                      2/2     Running   19         18d
```

It is controlled by daemonsets named 'weave-net'.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get daemonsets  -n kube-system 
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-proxy   4         4         4       4            4           kubernetes.io/os=linux   18d
weave-net    4         4         4       4            4           <none>                   18d
```

If it is a 'daemonsets' it should implement one pod per node, we will see how it is deployed.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system -o wide  |grep weave-net
weave-net-46pcs                      2/2     Running   22         18d    192.168.50.11   node-1       <none>           <none>
weave-net-dsw4n                      2/2     Running   20         18d    192.168.50.10   k8s-master   <none>           <none>
weave-net-slfx4                      2/2     Running   21         18d    192.168.50.12   node-2       <none>           <none>
weave-net-t4g45                      2/2     Running   19         18d    192.168.50.13   node-3       <none>           <none>
rajith@k8s-master:~$
```
See the 6th and 7th column you can see the server IP address and the hostname respectively.
{: style="text-align: justify;"}

There are many addons available for Kubernetes, at this moment it is not required for us. Maybe we will cover it later. [ Or if you are interested to go through it please click here](https://kubernetes.io/docs/concepts/overview/components/#addons)
{: .notice--info}
{: style="text-align: justify;"}

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



