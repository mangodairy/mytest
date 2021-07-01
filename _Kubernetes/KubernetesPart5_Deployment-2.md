---
title: "Deployment Demo."
excerpt: "Here we will play with the deployment. We will create, verify, scale-up, scale down and upgrade.."
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

In the previous module, we saw how to create the deployment using a pod definition file. Here along with the demo, we will create the deployment with "imperative command". 
{: style="text-align: justify;"}

## Create the deployment with imperative command

The below command does the same action it performed in the previous module using the declarative method.
{: style="text-align: justify;"}

```markup
kubectl create deployment nginx-deployment --image=nginx:1.14.2 --replicas=3
```
* It creates a  'Deployment' named 'nginx-deployment'.
* With three replicas in it.
* With the Nginx image version "nginx:1.14.2".
* The name of the container will be "nginx".
* The Pod listen to the port "80".



## Perform the cleanup.

List the pods.

```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-66b6c48dd5-bkwj6   1/1     Running   1          10h
nginx-deployment-66b6c48dd5-rlqjk   1/1     Running   1          10h
nginx-deployment-66b6c48dd5-s6pn4   1/1     Running   1          8h
rajith@k8s-master:~$
```
Check the deployment.

```markdown 
rajith@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           10h
rajith@k8s-master:~$ 
```
Delete the deployment.

```markdown 
rajith@k8s-master:~$ kubectl delete deployments  nginx-deployment
deployment.apps "nginx-deployment" deleted
rajith@k8s-master:~$ 
```
Verify the status of the deployment.
{: style="text-align: justify;"}
```markdown 
rajith@k8s-master:~$ kubectl get deployments
No resources found in default namespace.
rajith@k8s-master:~$ 
```
The deployment got deleted, what about the pods associated with it? 
{: style="text-align: justify;"}
```markdown 
rajith@k8s-master:~$ kubectl get pods
No resources found in default namespace.
rajith@k8s-master:~$ 
```
The pods also deleted along with the deployment. Cleanup is completed.
{: style="text-align: justify;"}

## Creating the deployment.

As mentioned, let us start the deployment with the imperative command.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create deployment nginx-deployment --image=nginx:1.14.2 --replicas=3 --port=80 
deployment.apps/nginx-deployment created
rajith@k8s-master:~$ 
```
Verify the pod creation.
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS              RESTARTS   AGE
nginx-deployment-65587f96cc-4fm7p   0/1     ContainerCreating   0          6s
nginx-deployment-65587f96cc-lwc4c   1/1     Running             0          6s
nginx-deployment-65587f96cc-sdhlf   1/1     Running             0          6s
rajith@k8s-master:~$ 
```
It starts creating the pods. Two pods already created.  One of the pod is containerising now. We will see the status of the deployment.

```markdown
rajith@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           27s
rajith@k8s-master:~$ 
```
It shows the 'READY` status '3/3' means all the pods are ready and running. We will see the pod status again.

```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-65587f96cc-4fm7p   1/1     Running   0          46m
nginx-deployment-65587f96cc-lwc4c   1/1     Running   0          46m
nginx-deployment-65587f96cc-sdhlf   1/1     Running   0          46m
rajith@k8s-master:~$ 
```

We verified the pods and the deployment. We have one more 'Kind' called 'replicaSet'. 
In the background, it works like this.
{: style="text-align: justify;"}
* The deployment creates the replicaset.
* The replicaset create the pods.

See the replicaset.
```markdown
rajith@k8s-master:~$ kubectl get replicasets
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-65587f96cc   3         3         3       64m
rajith@k8s-master:~$ 
```
We will detail about "replicaset" later. Now understand few concepts, we need a basic understanding of replicaset. That's why I thought of mentioning it here.
{: style="text-align: justify;"}
{: .notice--info}


## Scale the number of the pod to 5.

```markdown
rajith@k8s-master:~$ kubectl scale deployment nginx-deployment --replicas=5
deployment.apps/nginx-deployment scaled
rajith@k8s-master:~$ 
```
Verify the number of pods.

```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-65587f96cc-4fm7p   1/1     Running   0          79m
nginx-deployment-65587f96cc-lwc4c   1/1     Running   0          79m
nginx-deployment-65587f96cc-phjlk   1/1     Running   0          5s
nginx-deployment-65587f96cc-px7ns   1/1     Running   0          5s
nginx-deployment-65587f96cc-sdhlf   1/1     Running   0          79m
rajith@k8s-master:~$ 
```
You can see two pods created 5 seconds ago. And we have a total of 5 pods in the deployment.
We will see the status of the deployment and replicaset.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   5/5     5            5           79m
rajith@k8s-master:~$ kubectl get replicasets
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-65587f96cc   5         5         5       80m
rajith@k8s-master:~$
```
Current and the desired state is five,  looks good.
{: style="text-align: justify;"}

## Upgrade the application/container image version.

Now let us go and see another feature of deployment. 
Rolling updates -> Rolling updates allow deployment update to take place with zero downtime by incrementally updating Pods.
{: style="text-align: justify;"}

See the current definition file. Have a look at the image version.
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
  replicas: 5
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx:1.14.2 <---Current  image version is 1.14.2
        name: nginx
        ports:
        - containerPort: 80
```
Update the Nginx version to 'nginx:1.16.1 '.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ vi nginx-deployment.yaml
rajith@k8s-master:~$ cat nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx-deployment
  name: nginx-deployment
spec:
  replicas: 5
  selector:
    matchLabels:
      app: nginx-deployment
  template:
    metadata:
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx:1.16.1 ##<---The image version changed to nginx:1.16.1##
        name: nginx
        ports:
        - containerPort: 80
rajith@k8s-master:~$ 

```
Before updating the image version, have a look at pods, deployment and replicaset. 
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods,deployment,rs
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-65587f96cc-4fm7p   1/1     Running   0          126m
pod/nginx-deployment-65587f96cc-6jgbj   1/1     Running   0          14m
pod/nginx-deployment-65587f96cc-lwc4c   1/1     Running   0          126m
pod/nginx-deployment-65587f96cc-scjr4   1/1     Running   0          14m
pod/nginx-deployment-65587f96cc-sdhlf   1/1     Running   0          126m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   5/5     5            5           126m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-65587f96cc   5         5         5       126m
rajith@k8s-master:~$
```
All looks good.
We need to see the image version of the running container. How to see that?
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get rs -o wide
NAME                          DESIRED   CURRENT   READY   AGE    CONTAINERS   IMAGES         SELECTOR
nginx-deployment-65587f96cc   5         5         5       127m   nginx        nginx:1.14.2   app=nginx-deployment,pod-template-hash=65587f96cc
```
Have a look at the 8th field, the "IMAGES". It is 'nginx:1.14.2'.
We will upgrade the deployment with the help of the deployment definition file. 
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl apply -f nginx-deployment.yaml 
deployment.apps/nginx-deployment configured
```
We will see the status of pods, deployment and replicaset.
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ kubectl get pods,deployment,rs
NAME                                    READY   STATUS              RESTARTS   AGE
pod/nginx-deployment-5dfc477cf8-75fxk   0/1     ContainerCreating   0          4s		<----###containerizing###	
pod/nginx-deployment-5dfc477cf8-kfj29   0/1     ContainerCreating   0          4s
pod/nginx-deployment-5dfc477cf8-qjcgd   0/1     ContainerCreating   0          4s
pod/nginx-deployment-65587f96cc-4fm7p   1/1     Running             0          128m
pod/nginx-deployment-65587f96cc-6jgbj   1/1     Running             0          16m
pod/nginx-deployment-65587f96cc-lwc4c   1/1     Running             0          128m
pod/nginx-deployment-65587f96cc-scjr4   0/1     Terminating         0          16m    	<----###Terminating###
pod/nginx-deployment-65587f96cc-sdhlf   1/1     Running             0          128m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   4/5     3            4           128m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-5dfc477cf8   3         3         0       5s			<----###New replicaset###	 
replicaset.apps/nginx-deployment-65587f96cc   4         4         4       128m
rajith@k8s-master:~$
```

Look at the output.

* A new replicaset has created. 
* One container started Terminating. 
* Three pods started containerizing.

Look at the available, ready, desired, current status and see what is exactly happening here.

```markdown
rajith@k8s-master:~$ kubectl get pods,deployment,rs
NAME                                    READY   STATUS              RESTARTS   AGE
pod/nginx-deployment-5dfc477cf8-75fxk   0/1     ContainerCreating   0          15s
pod/nginx-deployment-5dfc477cf8-kfj29   0/1     ContainerCreating   0          15s
pod/nginx-deployment-5dfc477cf8-qjcgd   0/1     ContainerCreating   0          15s
pod/nginx-deployment-65587f96cc-4fm7p   1/1     Running             0          128m
pod/nginx-deployment-65587f96cc-6jgbj   1/1     Running             0          16m
pod/nginx-deployment-65587f96cc-lwc4c   1/1     Running             0          128m
pod/nginx-deployment-65587f96cc-sdhlf   1/1     Running             0          128m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   4/5     3            4           128m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-5dfc477cf8   3         3         0       15s
replicaset.apps/nginx-deployment-65587f96cc   4         4         4       128m
rajith@k8s-master:~$
```
Just have a look at the change.

```yaml
rajith@k8s-master:~$ kubectl get rs -o wide
NAME                          DESIRED   CURRENT   READY   AGE    CONTAINERS   IMAGES         SELECTOR
nginx-deployment-5dfc477cf8   3         3         0       22s    nginx        nginx:1.16.1   app=nginx-deployment,pod-template-hash=5dfc477cf8
nginx-deployment-65587f96cc   4         4         4       128m   nginx        nginx:1.14.2   app=nginx-deployment,pod-template-hash=65587f96cc
rajith@k8s-master:~$ kubectl get pods,deployment,rs
NAME                                    READY   STATUS              RESTARTS   AGE
pod/nginx-deployment-5dfc477cf8-4s74w   0/1     ContainerCreating   0          4s
pod/nginx-deployment-5dfc477cf8-75fxk   1/1     Running             0          28s     	<----###New pod created###
pod/nginx-deployment-5dfc477cf8-crtwr   0/1     ContainerCreating   0          2s
pod/nginx-deployment-5dfc477cf8-kfj29   1/1     Running             0          28s		<----###New pod created###
pod/nginx-deployment-5dfc477cf8-qjcgd   0/1     ContainerCreating   0          28s
pod/nginx-deployment-65587f96cc-4fm7p   1/1     Running             0          128m
pod/nginx-deployment-65587f96cc-6jgbj   1/1     Terminating         0          16m		<----###Terminating###
pod/nginx-deployment-65587f96cc-lwc4c   0/1     Terminating         0          128m
pod/nginx-deployment-65587f96cc-sdhlf   1/1     Running             0          128m
NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   4/5     5            4           128m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-5dfc477cf8   5         5         2       28s		<----###Newly created podon new replicaset###
replicaset.apps/nginx-deployment-65587f96cc   2         2         2       128m
```
See the difference now.
* Two new pod status changed to 'running' (28s). 
* At the same time, one more container termination started. 
* Have a close watch on available, ready, desired, current status along with its numbers.
* Now the newly created replicaset has two pods running on it. 

Hope you are getting what is happening during the upgrade?

**Did you notice at any given point in time minimum of four pods were running. So at any given time, we will have four pods to serve the application.**
{: style="text-align: justify;"}
**This means the end-users are unaffected during the application upgrade.**
{: .notice--success}
{: style="text-align: justify;"}

```markdown	
rajith@k8s-master:~$ kubectl get pods,deployment,rs
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-5dfc477cf8-4s74w   1/1     Running   0          28s
pod/nginx-deployment-5dfc477cf8-75fxk   1/1     Running   0          52s
pod/nginx-deployment-5dfc477cf8-crtwr   1/1     Running   0          26s
pod/nginx-deployment-5dfc477cf8-kfj29   1/1     Running   0          52s
pod/nginx-deployment-5dfc477cf8-qjcgd   1/1     Running   0          52s

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   5/5     5            5           129m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-5dfc477cf8   5         5         5       52s
replicaset.apps/nginx-deployment-65587f96cc   0         0         0       129m
rajith@k8s-master:~$ 
```
Now all the five pods started running. The old replicaset doesn't have any pods on it. The new replicaset has five pods running on it.
{: style="text-align: justify;"}

Upgrade completed successfully. But how do we confirm the pods are running with the new image?

```markdown
rajith@k8s-master:~$ kubectl get replicasets -o wide
NAME                          DESIRED   CURRENT   READY   AGE     CONTAINERS   IMAGES         SELECTOR
nginx-deployment-5dfc477cf8   5         5         5       124m    nginx        nginx:1.16.1   app=nginx-deployment,pod-template-hash=5dfc477cf8
nginx-deployment-65587f96cc   0         0         0       4h12m   nginx        nginx:1.14.2   app=nginx-deployment,pod-template-hash=65587f96cc
rajith@k8s-master:~$
```
Have a look at the 8th field, the 'IMAGES' and the number of the running pod in each replicasets.

## Scale down the number of deployment to 3.

No difference, the same way we scaled up the deployment.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl scale deployment nginx-deployment --replicas=3 
deployment.apps/nginx-deployment scaled
rajith@k8s-master:~$ 
```

Now you would be able to understand the output. No explanation needed from me.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-5dfc477cf8-4s74w   1/1     Running       0          129m
nginx-deployment-5dfc477cf8-75fxk   0/1     Terminating   0          129m
nginx-deployment-5dfc477cf8-crtwr   0/1     Terminating   0          129m
nginx-deployment-5dfc477cf8-kfj29   1/1     Running       0          129m
nginx-deployment-5dfc477cf8-qjcgd   1/1     Running       0          129m
rajith@k8s-master:~$ kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-5dfc477cf8   3         3         3       129m
nginx-deployment-65587f96cc   0         0         0       4h18m
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-5dfc477cf8-4s74w   1/1     Running   0          129m
nginx-deployment-5dfc477cf8-kfj29   1/1     Running   0          129m
nginx-deployment-5dfc477cf8-qjcgd   1/1     Running   0          129m
rajith@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           4h18m
rajith@k8s-master:~$ 
```
## Destroy the deployment.

We will do the cleanup.
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ kubectl delete deployments nginx-deployment
deployment.apps "nginx-deployment" deleted
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-5dfc477cf8-4s74w   0/1     Terminating   0          150m
nginx-deployment-5dfc477cf8-qjcgd   0/1     Terminating   0          150m
rajith@k8s-master:~$ kubectl get rs
No resources found in default namespace.
rajith@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS        RESTARTS   AGE
nginx-deployment-5dfc477cf8-4s74w   0/1     Terminating   0          150m
nginx-deployment-5dfc477cf8-qjcgd   0/1     Terminating   0          150m
rajith@k8s-master:~$ kubectl get deployments
No resources found in default namespace.
rajith@k8s-master:~$ kubectl get pods
No resources found in default namespace.
rajith@k8s-master:~$ kubectl get rs
No resources found in default namespace.
rajith@k8s-master:~$ 
```
Pods, replicaset, deployment, everything deleted.
{: style="text-align: justify;"}
We have a lot more to cover in deployment. We will cover those topics in another series.
{: .notice--success}
{: style="text-align: justify;"}

[In the next module, we will discuss the Kubernetes service. Click here to access ](https://rajith.in/Kubernetes/KubernetesPart6_service-1/)
{: style="text-align: justify;"}

{: style="text-align: justify;"}

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



