---
title: "Service Demo"
excerpt: "In this demo, we will expose an Nginx deployment outside the cluster with the help of the ClusterIP service."
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

## Steps involved in our demo.

* Creating a Kubernetes deployment with Nginx image.
* Create a NodePort service to expose the Nginx application.
* Verify the Nginx connectivity after the service creation.
* Verify the details of the service and the nginx deployment.
* Delete the service and try accessing Nginx.
* Destroy the deployment.

## Inspect the cluster.

Check for existing service, pod,replicaset, deployments etc.

```markdown
rajith@k8s-master:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   24d
rajith@k8s-master:~$ kubectl get deployments,rs,pods
No resources found in default namespace.
rajith@k8s-master:~$ 
```
In the current setup, we have only one service that is the default service coming along with the Kubernetes implementation. 

## Walk through the definition files.

We will have a look at our service and deployment definition files.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat nginx-deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx:1.16.1 #<--Image verion #
        name: nginx
        ports:
        - containerPort: 80
rajith@k8s-master:~$ 
```
We are already familiar with the deployment definition file. We used the same during our demo for [deployment.](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-2/). If you need any clarification please have a look at it. 
> One additional point to be noted is the 'labels'.  The label which we defined here should match with the 'selector' in the service definition file.
> Another important point is the 'containerPort' defined in the deployment should match with the 'targetPort' in the service definition file.
{: style="text-align: justify;"}

Coming to the service defenition file.
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ cat nginx-service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  # By default the `service type ` is set to ClusterIp if it is not defined in the defenition file.
  type: NodePort
  selector:
    app: nginx-deployment
  ports:
      # By default the `targetPort` is set to the same value as the `port` field.
    - port: 80
      targetPort: 80
      # Optional field
      # By default Kubernetes control plane will allocate a port from a range (default: 30000-32767)
      nodePort: 30007
rajith@k8s-master:~$ 
```
We used the same service definition file in the previous module. If you need any details please have a look at it. 
{: style="text-align: justify;"}

* Here the only difference is the name changed to 'nginx-service'.
* The selector changed to 'nginx-deployment'.

Make sure,

* The 'selector' in the service definition file matches with 'labels' in the deployment.
* The 'targetPort' in the service definition file matches with the  'containerPort' defined in the deployment.

## Create the deployment
That's it for now, we will create the deployment first. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create -f nginx-deployment.yaml 
deployment.apps/nginx-deployment created
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          7s
nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          7s
nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          7s
rajith@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          26s   10.32.0.3   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          26s   10.32.0.2   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          26s   10.38.0.1   node-2   <none>           <none>
rajith@k8s-master:~$ 
```

Deployment created, pods are running. We will come back to the '-o wide' output after the service creation.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create -f  nginx-service.yaml 
service/nginx-service created
rajith@k8s-master:~$ kubectl get svc nginx-service
NAME            TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
nginx-service   NodePort   10.99.197.69   <none>        80:30007/TCP   17s
rajith@k8s-master:~$
```

## Inspect the service 
The 'nginx-service' created. We need some detailed view of the service before going further. 

```markdown
"kubectl describe svc <name of the service >" is the command to view the details of the service.
```

```markdown
rajith@k8s-master:~$ kubectl describe svc nginx-service
Name:                     nginx-service
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=nginx-deployment
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.99.197.69
IPs:                      10.99.197.69
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30007/TCP
Endpoints:                10.32.0.2:80,10.32.0.3:80,10.38.0.1:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
rajith@k8s-master:~$
```


Here look at the endpoint IP and the port. We will take the output of pods with the 'wide' option once again.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE     IP          NODE     NOMINATED NODE   READINESS GATES
nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          9m48s   10.32.0.3   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          9m48s   10.32.0.2   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          9m48s   10.38.0.1   node-2   <none>           <none>
rajith@k8s-master:~$ 
```
You can see the pod IP, 6th field matches with the endpoints. 
{: style="text-align: justify;"}


To make it clear useing some filtering in the output. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get pods -o wide |awk '{ print $6 }'
IP
10.32.0.3
10.32.0.2
10.38.0.1
rajith@k8s-master:~$ kubectl describe svc nginx-service |grep Endpoints
Endpoints:                10.32.0.2:80,10.32.0.3:80,10.38.0.1:80
rajith@k8s-master:~$
```

For those who are not familiar with the Unix command, the above are some basic filtering in Unix. Nothing to do with Kubernetes.
{: .notice--info}
{: style="text-align: justify;"}

You can see the endpoints are automatically grabbed the pod IPs. This is because of the "Labels" and "Selector".
{: style="text-align: justify;"}


Now our setup is ready. The Nginx application exposed outside the cluster. How do we test it? 
For that, you need to understand my lab setup. 
{: style="text-align: justify;"}

## Undersrand the lab setup

I am running this setup on my laptop in a virtual box installed on it. You can see the IP mapping from the below commands.
{: .notice--info}
{: style="text-align: justify;"}

Host file from my laptop.

```yaml
rajith@Shiva:~$ grep 192 /etc/hosts
192.168.50.10 k8s-master master
192.168.50.11 node-1  node01
192.168.50.12 node-2  node02
192.168.50.13 node-3  node03
rajith@Shiva:~$ 
```
See the node IPs.

```markdown
rajith@k8s-master:~$ kubectl get node -o wide
NAME         STATUS   ROLES                  AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
k8s-master   Ready    control-plane,master   24d   v1.21.1   192.168.50.10   <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.6
node-1       Ready    <none>                 24d   v1.21.1   192.168.50.11   <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.6
node-2       Ready    <none>                 24d   v1.21.1   192.168.50.12   <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.6
node-3       Ready    <none>                 24d   v1.21.1   192.168.50.13   <none>        Ubuntu 20.04.2 LTS   5.4.0-73-generic   docker://20.10.6
rajith@k8s-master:~$ 
```
See the 6th field from the above output.
Here is the IP mapping.
{: style="text-align: justify;"}

* 192.168.50.10 ->  master
* 192.168.50.11 -> node-1 
* 192.168.50.12 -> node-2 
* 192.168.50.13 -> node-3 

```markdown
rajith@k8s-master:~$ kubectl get pod  -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          28m   10.32.0.3   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          28m   10.32.0.2   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          28m   10.38.0.1   node-2   <none>           <none>
rajith@k8s-master:~$ 
```

Pods are running on node-2 and node-3. The corresponding IP is '192.168.50.12' and '192.168.50.13'. We will see this IP from my laptop browser. 
{: style="text-align: justify;"}


<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Service-01.png" alt="Image 1">
  <figcaption>node-2 with the IP "http://192.168.50.12" </figcaption>
</figure>


**What happened? the Nginx welcome page is not showing! 😳**

```markdown
rajith@k8s-master:~$ kubectl describe svc nginx-service |grep NodePort
Type:                     NodePort
NodePort:                 <unset>  30007/TCP
rajith@k8s-master:~$ 
```
We need to use the NodePort along with the IP to access the page.
Try accessing 192.168.50.12:30007.
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Service-node02.png" alt="Image 2">
  <figcaption>node-2 with the IP and port "http://192.168.50.12:30007" </figcaption>
</figure>

**Yes, it is working.😊**
{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Service-node03.png" alt="Image 2">
  <figcaption>node-3 with the IP and port "http://192.168.50.13:30007" </figcaption>
</figure>

**Yes,  that also works. Perfect all good 👍.**


{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Service-node01.png" alt="Image 2">
  <figcaption>node-1 with the IP and port "http://192.168.50.11:30007" </figcaption>
</figure>


We will try accessing with the node-01 IP. "192.168.50.11:30007". 
{: style="text-align: justify;"}
**That also gives the welcome page!!🤔**
{: style="text-align: justify;"}


Let me try the master node "192.168.50.10:30007. 
{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Service-masternode.png" alt="Image 2">
  <figcaption>master node with the IP and port "http://192.168.50.10:30007" </figcaption>
</figure>


**That is also showing the same!! 😇** 
{: style="text-align: justify;"}
What's happening? The pods are started only on node-2 and node-3. But all the nodes includes the master node IP showing the 'Nginx welcome page' ??
{: style="text-align: justify;"}

**Are we doing something wrong? 😫**
{: style="text-align: justify;"}

**No, that's how the NodePort service designed to work. It serves through all the nodes IP. There is an internal forwarding.**
We will discuss this in another series.
{: .notice--success}
{: style="text-align: justify;"}

Hope, you are with me. We will delete the service and see what happens. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl delete service nginx-service 
service "nginx-service" deleted
rajith@k8s-master:~$ kubectl get service
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   24d
rajith@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          53m   10.32.0.3   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          53m   10.32.0.2   node-3   <none>           <none>
nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          53m   10.38.0.1   node-2   <none>           <none>
rajith@k8s-master:~$ 
```
 
Service is deleted but pods are running. We will see the application available through the browser. 
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Introduction/Service/Final-output.png" alt="Image 2">
  <figcaption>node-2 with the IP and port "http://192.168.50.12:30007" </figcaption>
</figure>


No, the "Nginx welcome page" is not available now. 
Even if the pods are running, we need the service to access the application externally.
{: style="text-align: justify;"}

## We will do the cleanup.

```markdown
rajith@k8s-master:~$ kubectl get deployments,rs,pods,svc 
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           3h45m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-5dfc477cf8   3         3         3       3h45m

NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-5dfc477cf8-jqn6q   1/1     Running   0          3h45m
pod/nginx-deployment-5dfc477cf8-vk8w4   1/1     Running   0          3h45m
pod/nginx-deployment-5dfc477cf8-zvflg   1/1     Running   0          3h45m

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   25d
rajith@k8s-master:~$ kubectl delete -f nginx-deployment.yaml
deployment.apps "nginx-deployment" deleted
rajith@k8s-master:~$ 
```

**Now we got a clear idea of how the Kubernetes service works and how it's related to the other components in the cluster.**

[Next, we will have a discussion on Kubernetes volume and persistent volume.](https://rajith.in/Kubernetes/KubernetesPart7_PersistentVolume/)
{: .notice--success}
{: style="text-align: justify;"}
<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



