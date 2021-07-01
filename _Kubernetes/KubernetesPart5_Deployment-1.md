---
title: "Create your First Deployment."
excerpt: "In this module, we will go through the basics of Deployment and create deployment."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day5.png
  excerpt: "Create your Deployment and understand the Pod definition file."
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

## [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

We discussed about the Pods in the previous module, the deployment controller controls the way Pods behave.
{: style="text-align: justify;"}
It allows,
{: style="text-align: justify;"}
* Scaling

Scale up the Deployment to facilitate more load.
{: .text-center}


* Rolling updates

Rolling updates allow Deployment's update to take place with zero downtime by incrementally updating Pods.
{: .text-center}
* Rollbacks

Rollback to an earlier version, if the current state of the Deployment is not stable or it is completely failed to serve the purpose.
{: .text-center}

## Deployment Definition file

The structure of the deployment definition file looks like this.
{: style="text-align: justify;"}

```yaml
apiVersion: 
kind: 
metadata:apiVersion: 
kind: 
metadata:
  name: 
  labels:
    app: 
spec:
  replicas: 
  selector:
    matchLabels:
      app: 
  template:
    metadata:
      labels:
        app: 
    spec:
      containers:
      - name: 
        image: 
        ports:
        - containerPort: 
```
**We will fill in the required information to create a deployment.**
{: style="text-align: justify;"}
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
* The name of the 'Deployment' is  'nginx-deployment'.
* The number of replicas ( the number of pods created by the deployment ) is 3.
* The name of the Pod is "nginx".
* The image used to create the deployment is "nginx:1.14.2"
* The Pod listen to the port "80"
In simple words, this is what this deployment definition file tells us.
{: style="text-align: justify;"}

We will have a detailed discussion on deployment in a later module which is specifically for deployment. 
{: .notice--info}
{: style="text-align: justify;"}


## Creating the Deployment.

As mentioned in the Pod, there are two methods to create a Deployment.

* Using Imperative Commands
* Using Declarative methods
{: style="text-align: justify;"}

### Creating the Deployment Using Deployment Definition file.

Check for running pods.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
my-first-pod   1/1     Running   1          45h
rajith@k8s-master:~$ 
```
One pod running on this cluster, check for any existing deployment. 
{: style="text-align: justify;"}


```markdown
rajith@k8s-master:~$ kubectl get deployments
No resources found in default namespace.
rajith@k8s-master:~$ 
```
At present there are no deployment running on this cluster.
{: style="text-align: justify;"}
Copy the deployment definition given above and create the definition file named "nginx-deployment.yaml" .
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ vi nginx-deployment.yaml
rajith@k8s-master:~$ cat nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
rajith@k8s-master:~$ 
```

Execute the command "kubectl create -f nginx-deployment.yam" to create the deployment.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create -f nginx-deployment.yaml 
deployment.apps/nginx-deployment created
rajith@k8s-master:~$ 
```
Verify the deployment status.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   0/3     3            0           6m1s
rajith@k8s-master:~$ 
```

This is not a usual behaviour, we will see the status of Pod.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
my-first-pod                        1/1     Running   1          46h
nginx-deployment-66b6c48dd5-98ksg   0/1     Pending   0          6m15s
nginx-deployment-66b6c48dd5-bkwj6   0/1     Pending   0          6m15s
nginx-deployment-66b6c48dd5-rlqjk   0/1     Pending   0          6m15s
rajith@k8s-master:~$ 
```
The pod is in the pending state, there is something unusual in the cluster. **Went through the logs and identified that the worker node had some issue.** 
{: style="text-align: justify;"}
Here , the worker nodes status is "NotReady".
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS     ROLES                  AGE   VERSION
k8s-master   Ready      control-plane,master   23d   v1.21.1
node-1       NotReady   <none>                 23d   v1.21.1
node-2       NotReady   <none>                 23d   v1.21.1
node-3       NotReady   <none>                 23d   v1.21.1
rajith@k8s-master:~$
```
Corrected the issue for "node-1" and node-2", you can see those node's status changed to "Ready".
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS     ROLES                  AGE   VERSION
k8s-master   Ready      control-plane,master   23d   v1.21.1
node-1       Ready      <none>                 23d   v1.21.1
node-2       Ready      <none>                 23d   v1.21.1
node-3       NotReady   <none>                 23d   v1.21.1
rajith@k8s-master:~$
```
Now we will see the status of those Pods.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS              RESTARTS   AGE
my-first-pod                        1/1     Running             1          46h
nginx-deployment-66b6c48dd5-98ksg   0/1     ContainerCreating   0          10m
nginx-deployment-66b6c48dd5-bkwj6   0/1     ContainerCreating   0          10m
nginx-deployment-66b6c48dd5-rlqjk   0/1     ContainerCreating   0          10m
rajith@k8s-master:~$ 
```
It changed to "ContainerCreating ".We will see whether the pod has started or not.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
my-first-pod                        1/1     Terminating   1          47h
nginx-deployment-66b6c48dd5-98ksg   1/1     Running       0          100m
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running       0          100m
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running       0          100m
rajith@k8s-master:~$ 
```
You can see the Pods created by the "nginx-deployment"  is in a running state. However, the other Pod is "Terminating". 
{: style="text-align: justify;"}
**Actually, I did not have the plan to share these details in this module. This should have been covered in the troubleshooting session. While preparing this document my cluster node had some real-time issue so I thought of sharing those things also with you.** Still, you can see the other Pod has some issue. That was expected when all the worker nodes are not ready it should have been in dead state but it took some time to reflect it. 
{: .notice--success}
{: style="text-align: justify;"}
Anyway, we reached here, we take a few more step ahead. 
{: style="text-align: justify;"}
In the previous output we saw that only two worker nodes are "Ready", so the deployment should place the pods only to those two nodes, but how we will see the Pod distribution? Hope you remember the "-o wide" option which we used ? 
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS        RESTARTS   AGE    IP          NODE     NOMINATED NODE   READINESS GATES
my-first-pod                        1/1     Terminating   1          47h    10.32.0.2   node-3   <none>           <none>
nginx-deployment-66b6c48dd5-98ksg   1/1     Running       0          114m   10.38.0.3   node-2   <none>           <none>
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running       0          114m   10.38.0.1   node-2   <none>           <none>
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running       0          114m   10.38.0.2   node-2   <none>           <none>
rajith@k8s-master:~$ 
```

Here you can see all the pods are placed on "node-2" .
{: style="text-align: justify;"}
The reason is node-3 is anyway not ready.  First I corrected the issue of node-02, immediately after node-2 is ready the deployment controller started placing the Pods on that node. So Even if the "node-1" is ready the Pods are already placed on node-2.  So deployment controller will not relocate the Pod at this moment.
{: style="text-align: justify;"}
**Now the chances of placing the Pod by this deployment controller is Listed below.**
{: style="text-align: justify;"}
* If there is any new addition ( scale-up ) 
* Recreation or during the upgrade.
* In case of any Pod termination within the deployment. 

{: style="text-align: justify;"}

We will see the third possibility in action.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
my-first-pod                        1/1     Terminating   1          2d
nginx-deployment-66b6c48dd5-98ksg   1/1     Running       0          132m
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running       0          132m
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running       0          132m
rajith@k8s-master:~$ 
```
I am going to delete the Pod **"nginx-deployment-66b6c48dd5-98ksg"**
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl delete pod nginx-deployment-66b6c48dd5-98ksg
pod "nginx-deployment-66b6c48dd5-98ksg" deleted
rajith@k8s-master:~$ 
```
The Pod is deleted, will see the status once again. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get pod 
NAME                                READY   STATUS        RESTARTS   AGE
my-first-pod                        1/1     Terminating   1          2d
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running       0          135m
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running       0          135m
nginx-deployment-66b6c48dd5-s6pn4   1/1     Running       0          50s
rajith@k8s-master:~$
```
You can see another Pod named **"nginx-deployment-66b6c48dd5-s6pn4"** is automatically created and it is Running for the 50s. This means the deployment has the auto-healing capability. We no need to create the pod manually.
{: style="text-align: justify;"}

```markdown
We will see where it is placed?
rajith@k8s-master:~$ kubectl get pod -o wide |grep nginx
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running       0          144m    10.38.0.1    node-2   <none>           <none>
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running       0          144m    10.38.0.2    node-2   <none>           <none>
nginx-deployment-66b6c48dd5-s6pn4   1/1     Running       0          9m47s   10.40.0.11   node-1   <none>           <none>
rajith@k8s-master:~$ 
```
It is placed on node-1.

We came lots of steps ahead of what we thought of discussing here. There are lot more to cover, those points will be cover in another series. 
{: style="text-align: justify;"}


**I thought of covering a demo here however, the topic went for a long so giving a small pause here.In the next module we will cover the demo with the below steps.**

* Creating a Kubernetes deployment.
* Verify the pods replicaset and deployment created with the above step.
* Verify the deployment details.
* Scale the number of pod to 5.
* Verify the rollout status.
* Upgrade the application/container image version.
* scale down the number of deployment to 3.
* Destroy the deployment.
{: style="text-align: justify;"}
[Our next module, Deployment demo, click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-2/)


<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



