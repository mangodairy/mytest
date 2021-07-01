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

If you are interested to see the definition of each component, please refer to the official documentation. For ease of access, I have given the corresponding link.

**To see the implementation of those components in the k8s cluster, we will go through each of them. **
{: style="text-align: justify;"}
## We will Walk Through the Server

### List the Worker Node Components

In the previous chapter, before filtering the master node components we went through a few concepts and commands hope you remember those points.If not, [please refresh it.  ](https://mangodairy.github.io/mytest/Kubernetes/KubernetesPart2/#list-the-control-plane-components)
{: .notice--info}
{: style="text-align: justify;"}

**If you remember those points, we will add few more points here to understand them better.**


* Like pod, there is another ‘kind’ available in k8s called daemonset.
* It can be viewed with the command “kubectl get daemonsets”.
* There is an option available with the get command that is “-o wide “. With this option, we will get more details of it.

### [kube-proxy](https://kubernetes.io/docs/concepts/overview/components/#kube-proxy)

Kube-proxy is a network proxy that runs on each cluster node includes the master node. Each node will have one set of Kube-proxy running on it. Kube-proxy implemented with the help of a daemonset. 
{: style="text-align: justify;"}

Daemonset ensures one pod is running on each cluster node. If there is any addition of node, it takes care of adding the new pod on it. 
{: .notice--info}
{: style="text-align: justify;"}

Now we have the required details to filter only the kube-proxy from the cluster.We will make the command.
{: .notice--success}
{: style="text-align: justify;"}

```yaml
    kubectl get pods -n kube-system  |grep master
```

Will execute it on a server and see the result.

{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep kube-proxy
kube-proxy-86f72                     1/1     Running   8          16d
kube-proxy-lpvws                     1/1     Running   7          16d
kube-proxy-n9phj                     1/1     Running   7          16d
kube-proxy-vkdvx                     1/1     Running   7          16d
```
In the command output, you can see four Kube-proxy runnings on this cluster. It is a four-node cluster that includes the master node. But how do we see the pods distributed across the nodes?

Did you remember we discussed an option "-o wide"?  We will try it with the above command to see how the output looks.
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
See the 6th and 7th column. You can see the server IP address and the hostname, respectively. Please have a close look. You will notice that each Kube-proxy pod is running on each node of the cluster. That is what we discussed sometime back.
{: .notice--info}
{: style="text-align: justify;"}

But with the above command, we listed only the pods, then what is 'daemonset'? These pods created with the help of a 'daemonset'. We will see that shortly.
We will execute the command which we discussed as the second point. *"kubectl get daemonsets"*

```markdown
rajith@k8s-master:~$ kubectl get daemonsets -n kube-system 
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-proxy   4         4         4       4            4           kubernetes.io/os=linux   16d
weave-net    4         4         4       4            4           <none>                   16d
rajith@k8s-master:~$ 
```
You can see that there are two ‘daemonset’ out of which one is ‘Kube-proxy’ that’s what we are looking for. The other one, ‘weave-net’, will discuss shortly.
{: style="text-align: justify;"}
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
It is a Linux command. The warning shows because I am running it as a non-root user. Ignore it for now.
{: .notice--info}
{: style="text-align: justify;"}


### [Container runtime](https://kubernetes.io/docs/concepts/overview/components/#container-runtime)

The container runtime is the software that is responsible for running containers. Kubernetes supports several container runtimes like docker, container, CRI-O etc. In my current setup, I am using docker as the container runtime. Like, kubelet docker also running a Linux service. Let’s have a look at it.
{: style="text-align: justify;"}

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
Now I ran this command as the root user. You can see the previous warning has gone, lots of other message and warning came, at this stage, we are not much worried about those messages and warnings.
{: .notice--info}
{: style="text-align: justify;"}


## Addons

The components already discussed are the core components needed for Kubernetes to work. There are few more components required for the smooth functioning of Kubernetes. We will go through it.
{: style="text-align: justify;"}

### [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns)
Even though the DNS considered as an addon., it is one of the mandatory requirements. You can select your own DNS. By default, [CoreDNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns)  come along with the “kubeadm” implementation. As the name implies, it is for DNS. I think it doesn’t need an explanation.

#### Pod associated with CoreDNS
```markdown
###Pod associated with CoreDNS###

rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep dns
coredns-558bd4d5db-8t95j             1/1     Running   6          12d
coredns-558bd4d5db-mtq96             1/1     Running   2          3d7h
```
#### Deployment associated with CoreDNS
```markdown
###Deployment associated with CoreDNS###
rajith@k8s-master:~$ kubectl get deployments.apps  -n kube-system  
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   2/2     2            2           18d
```
#### Service associated with CoreDNS
```markdown
###Service associated with CoreDNS###

rajith@k8s-master:~$ kubectl get service -n kube-system 
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   18d
rajith@k8s-master:~$ 
```
This is how the CoreDNS implemented on a cluster, 

* Two pods are running to serve dns requests.
* It controlled by a deployment named "coredns".
* It is made available to the cluster through the service named "kube-dns".

We will discuss deployment and service in the upcoming modules.
{: .notice--info}
{: style="text-align: justify;"}

### The Kubernetes Networking Model
Kubernetes doesn't have its own networking model. It uses third party implementation. However, Kubernetes imposes the following fundamental requirements on any networking implementation (barring any intentional network segmentation policies):
* Pods on a node can communicate with all pods on all nodes without NAT
* Agents on a node (e.g. system daemons, kubelet) can communicate with all pods on that node
{: style="text-align: justify;"}
Note: For those platforms that support Pods running in the host network (e.g. Linux)
{: style="text-align: justify;"}
{: .notice--info}
* Pods in the host network of a node can communicate with all pods on all nodes without NAT.
{: style="text-align: justify;"}

There are many varieties of network implementation for Kubernetes. [ Please refer to this link for details](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

Here we used ["weave-net"](https://www.weave.works/oss/net/).*We will go through the cluster to understand the implementation.*
{: style="text-align: justify;"}

#### We have four pods running with 'weave-net' image.
{: style="text-align: justify;"}
```markdown
###Pod associated with weave-net###

rajith@k8s-master:~$ kubectl get pods -n kube-system  |grep weave-net
weave-net-46pcs                      2/2     Running   22         18d
weave-net-dsw4n                      2/2     Running   20         18d
weave-net-slfx4                      2/2     Running   21         18d
weave-net-t4g45                      2/2     Running   19         18d
```

#### It is controlled by daemonsets named 'weave-net'.
{: style="text-align: justify;"}
```markdown
###Daemonset associated with weave-net###

rajith@k8s-master:~$ kubectl get daemonsets  -n kube-system 
NAME         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-proxy   4         4         4       4            4           kubernetes.io/os=linux   18d
weave-net    4         4         4       4            4           <none>                   18d
```

#### If it is a 'daemonsets' it should implement one pod per node, we will see how it is deployed.
{: style="text-align: justify;"}
```markdown
###Pod and the associated nodes###
rajith@k8s-master:~$ kubectl get pods -n kube-system -o wide  |grep weave-net
weave-net-46pcs                      2/2     Running   22         18d    192.168.50.11   node-1       <none>           <none>
weave-net-dsw4n                      2/2     Running   20         18d    192.168.50.10   k8s-master   <none>           <none>
weave-net-slfx4                      2/2     Running   21         18d    192.168.50.12   node-2       <none>           <none>
weave-net-t4g45                      2/2     Running   19         18d    192.168.50.13   node-3       <none>           <none>
rajith@k8s-master:~$
```
See the 6th and 7th column. You can see the server IP address and the hostname, respectively. It implemented in the master node + three worker node, one per node.
{: style="text-align: justify;"}

There are many addons available for Kubernetes. At this moment, it is not needed for us. Maybe we will cover it later. [ Or if you are interested to go through it please click here](https://kubernetes.io/docs/concepts/overview/components/#addons)
{: .notice--info}
{: style="text-align: justify;"}

[Next we will create our first Pod,click here](https://rajith.in/Kubernetes/KubernetesPart4_Pods/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



