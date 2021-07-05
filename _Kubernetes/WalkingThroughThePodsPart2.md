---
title: "Create your First Pod."
excerpt: "In this module, we will go through the basics of pods and understand the structure of pod definition."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day4.png
  excerpt: "Create your First Pod and understand the Pod definition file."
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

##  Create a Pod 

It covered in the previous series. We will look into it from a different angle. We will use the pod definition file created in the previous module and create a pod.
{: style="text-align: justify;"}
If you do not have any previous experience on the pod, have a look at the previous series[Kubernetes in 7 days? In a week !!!](https://www.rajith.in/Kubernetes/#kubernetes-learning-series) and come back here.[Click here](https://www.rajith.in/Kubernetes/KubernetesPart4_Pods/#creating-a-pod)
{: style="text-align: justify;"}


```yaml
rajith@k8s-master:~$ vi my-first-pod.yaml 
rajith@k8s-master:~$ cat my-first-pod.yaml 
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
rajith@k8s-master:~$ kubectl create -f my-first-pod.yaml 
pod/my-first-pod created
rajith@k8s-master:~$ kubectl get pods 
NAME           READY   STATUS              RESTARTS   AGE
my-first-pod   0/1     ContainerCreating   0          5s
rajith@k8s-master:~$ kubectl get pods 
NAME           READY   STATUS              RESTARTS   AGE
my-first-pod   0/1     ContainerCreating   0          8s
rajith@k8s-master:~$
```
It is containerising. How will we check the details of it? I mean, to see what is happening in the background. 🤔

```yaml
rajith@k8s-master:~$ kubectl describe pod my-first-pod 
Name:         my-first-pod
Namespace:    default
Priority:     0
Node:         node-3/192.168.50.13
Start Time:   Mon, 05 Jul 2021 16:09:50 +0000
Labels:       env=prod
              type=web
Annotations:  <none>
Status:       Running
IP:           10.32.0.2
IPs:
  IP:  10.32.0.2
Containers:
  my-first-pod:
    Container ID:   docker://3134b4d480414366cccdb9baa208d0eb618124bdb92c13e2f715b335510f61d9
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:47ae43cdfc7064d28800bc42e79a429540c7c80168e8c8952778c0d5af1c09db
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 05 Jul 2021 16:10:00 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-d7gtz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-d7gtz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  18s   default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    17s   kubelet            Pulling image "nginx"
  Normal  Pulled     8s    kubelet            Successfully pulled image "nginx" in 8.907628369s
  Normal  Created    8s    kubelet            Created container my-first-pod
  Normal  Started    8s    kubelet            Started container my-first-pod
rajith@k8s-master:~$ 
```
Yes, the command **kubectl describe pod my-first-pod** give the details of it.

We will break it down further. 

The first section containes the metadata information of the pod.
```yaml
Name:         my-first-pod
Namespace:    default
Priority:     0
Node:         node-3/192.168.50.13
Start Time:   Mon, 05 Jul 2021 16:09:50 +0000
Labels:       env=prod
              type=web
Annotations:  <none>
Status:       Running
IP:           10.32.0.2
IPs:
  IP:  10.32.0.2
```

```yaml
Containers:
  my-first-pod:
    Container ID:   docker://3134b4d480414366cccdb9baa208d0eb618124bdb92c13e2f715b335510f61d9
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:47ae43cdfc7064d28800bc42e79a429540c7c80168e8c8952778c0d5af1c09db
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 05 Jul 2021 16:10:00 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-d7gtz (ro)
```
Next, it talks about the container details inside the pod. You can see the container name, container ID, image used,  Image ID, etc in it.
{: style="text-align: justify;"}

```yaml
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
```
Now it talks about the condition of the pod, hopes it is self-explanatory. 


```yaml
Volumes:
  kube-api-access-d7gtz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
```
It is about the volume information. If we mount any persistent volume, it shows here. Then about Toleration, Node-Selectors those are a completely different topic we will talk about it later.

```yaml
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  18s   default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    17s   kubelet            Pulling image "nginx"
  Normal  Pulled     8s    kubelet            Successfully pulled image "nginx" in 8.907628369s
  Normal  Created    8s    kubelet            Created container my-first-pod
  Normal  Started    8s    kubelet            Started container my-first-pod
```
Now it is about the event, the actual part which we thought of discussing here. 

In the event, the first line talks about pod scheduling. 
* It used the 'default scheduler to schedule the pod. We discussed [scheduler](https://www.rajith.in/Kubernetes/KubernetesPart2/#control-plane-components--master-node-) in our first series, **control plane components.**
* It says it assigned to node-3.
How do verify? Yes, we knew that. 
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ kubectl get pod  -o wide
NAME           READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
my-first-pod   1/1     Running   0          96m   10.32.0.2   node-3   <none>           <none>
rajith@k8s-master:~$ 
```
It assigned to node-3. 

```yaml
  Normal  Pulling    17s   kubelet            Pulling image "nginx"
  Normal  Pulled     8s    kubelet            Successfully pulled image "nginx" in 8.907628369s
``` 
The next two lines say it is pulling the image "Nginx" from the docker repository. It took 8s to pull the image. 

```yaml
  Normal  Created    8s    kubelet            Created container my-first-pod
  Normal  Started    8s    kubelet            Started container my-first-pod
```
And the last two line says it created the pod named 'my-first-pod', it is started.
{: style="text-align: justify;"}

That's it, initially by looking at the big output we thought it is rocket science. It is as simple as that of our first standard textbook. 
{: .notice--success}
{: style="text-align: justify;"}

[Our next module is on deployment, Click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



