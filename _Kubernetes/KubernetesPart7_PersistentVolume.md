---
title: "Kubernetes Persistent Volume"
excerpt: "In this module, we will learn how to preserve data in a pod with the help of Persistent Volumes."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Day7.png
  excerpt: "In this module, we will learn how to preserve data in a pod with the help of Persistent Volumes."
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
## Persistent Volumes.

What is Kubernetes Volume?
Kubernetes volume is a directory that is bound to the pod. Kubernetes volume dies along with the pod. So we can not store any data which we need after the pod's lifecycle.
{: style="text-align: justify;"}
## What is Kubernetes Persistent Volume? 

Kubernetes Persistent volume is the solution to the issues we faced in the Kubernetes volume. Kubernetes persistent volume remains after the pod lifecycle. So it can be bound to another pod and use the data.
{: style="text-align: justify;"}

## Different Types of Kubernetes Volumes.


PersistentVolume types are implemented as plugins. Kubernetes currently supports the following plugins:
[Please refer to this link to have a detailed view on this topic, there are many varieties of persistent volume which is supported by Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#types-of-persistent-volumes) 

* awsElasticBlockStore - AWS Elastic Block Store (EBS)
* azureDisk - Azure Disk
* azureFile - Azure File
* cephfs - CephFS volume
* cinder - Cinder (OpenStack block storage) (deprecated)
* csi - Container Storage Interface (CSI)
* fc - Fibre Channel (FC) storage
* flexVolume - FlexVolume
* flocker - Flocker storage
* gcePersistentDisk - GCE Persistent Disk
* glusterfs - Glusterfs volume
* hostPath - HostPath volume (for single node testing only; WILL NOT WORK in a multi-node cluster; consider using local volume instead)
* iscsi - iSCSI (SCSI over IP) storage
* local - local storage devices mounted on nodes.
* nfs - Network File System (NFS) storage
* photonPersistentDisk - Photon controller persistent disk. (This volume type no longer works since the removal of the corresponding cloud provider.)
* portworxVolume - Portworx volume
* quobyte - Quobyte volume
* rbd - Rados Block Device (RBD) volume
* scaleIO - ScaleIO volume (deprecated)
* storageos - StorageOS volume
* vsphereVolume - vSphere VMDK volume

## Volume Demo.

### Create a pod to test data persistency.

```markdown
rajith@k8s-master:~$ kubectl run test-data --image=busybox --command sleep "5000" 
pod/test-data created
rajith@k8s-master:~$
```
* Pod name is test-data.
* The image used to create the Pod is busybox.
* Pod lifecycle end if there is no running process available.
* The busybox image does not have any process in the startup.
* We are executing a sleep command to make it alive till the completion of our test.


```markdown
rajith@k8s-master:~$ kubectl get pods 
NAME        READY   STATUS    RESTARTS   AGE
test-data   1/1     Running   0          10s
rajith@k8s-master:~$ 
```
The pod got created and it started.
{: .notice--success}
{: style="text-align: justify;"}

### Connect to the pod in the Kubernetes cluster
To connect to the pod in the Kubernetes cluster we use the command " kubectl exec".
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl exec -it test-data -- sh 
/ # 
/ # uname -a
Linux test-data 5.4.0-73-generic #82-Ubuntu SMP Wed Apr 14 17:39:42 UTC 2021 x86_64 GNU/Linux
/ # hostname 
test-data
/ # 
```
We connected to the pod. The hostname of the pod is 'test-data'
{: style="text-align: justify;"}

### Place data to test.
We will create a directory and place some data on it.
{: style="text-align: justify;"}

```markdown
/ # mkdir /data
/ # cd /data
/data # touch testfile1
/data # echo "My data to Test the Persistency" > MyDataFile.txt
/data # cat MyDataFile.txt
My data to Test the Persistency
/data # 
command terminated with exit code 130
```
Placed the data in the pod and disconnected from it. 
{: style="text-align: justify;"}


```markdown
rajith@k8s-master:~$ kubectl get pods 
NAME        READY   STATUS    RESTARTS   AGE
test-data   1/1     Running   0          2m56s
rajith@k8s-master:~$ 
```

### Delete and recreate the pod.
The pod 'test-data' is still running.Delete and recreate it.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl delete pod test-data
pod "test-data" deleted
rajith@k8s-master:~$ kubectl run test-data --image=busybox --command sleep "5000"
pod/test-data created
```

The pod 'test-data' is recreated, we will see the data in it. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl exec -it test-data -- sh
/ # ls -ld /data
ls: /data: No such file or directory
/ # ls -l /data
ls: /data: No such file or directory
/ # 
command terminated with exit code 1
rajith@k8s-master:~$ 
```
The data directory itself is not available. 
{: style="text-align: justify;"}

### create the pod with volume.

We will create the pod 'test-data' with the pod definition file given below.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat TestData.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: test-data
spec:
  containers:
  - image: busybox
    name: busybox
    command: [ "sleep" , "4800" ]
    volumeMounts:
    - mountPath: /data
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /mnt/data
      # this field is optional
      type: DirectoryOrCreate
rajith@k8s-master:~$
```
In addition to the 'sleep' command. We have added the volume entry also in this pod definition file.
Here, 
The volume type is hostPath.
* If the directory ' /mnt/data' is not present in the host ( worker node)  it will create. 
* The volume name is 'test-volume'.
* On the pod, the host directory '/mnt/data' is mounted under '/data'.
{: style="text-align: justify;"}

We will go ahead and create the pod.

```markdown
rajith@k8s-master:~$ kubectl create -f TestData.yaml 
Error from server (AlreadyExists): error when creating "TestData.yaml": pods "test-data" already exists
rajith@k8s-master:~$ 
```

What happened? 🤔 Yes, we did not delete the pod 'test-data'.
{: style="text-align: justify;"}

We can not run two pods with the same name under the same namespace.
{: .notice--info}
{: style="text-align: justify;"}

Delete the pod and recreate it.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl delete pod test-data 
pod "test-data" deleted
rajith@k8s-master:~$ kubectl create -f TestData.yaml 
pod/test-data created
rajith@k8s-master:~$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
test-data   1/1     Running   0          8s
rajith@k8s-master:~$
```

### Place the data under the volume.

Connect to the pod 'test-data'.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl exec -it test-data -- sh
/ # ls -l 
total 40
drwxr-xr-x    2 root     root         12288 Jun  7 17:34 bin
drwxr-xr-x    2 root     root          4096 Jun 27 03:41 data
drwxr-xr-x    5 root     root           360 Jun 27 03:42 dev
drwxr-xr-x    1 root     root          4096 Jun 27 03:42 etc
drwxr-xr-x    2 nobody   nobody        4096 Jun  7 17:34 home
dr-xr-xr-x  171 root     root             0 Jun 27 03:42 proc
drwx------    1 root     root          4096 Jun 27 03:43 root
dr-xr-xr-x   13 root     root             0 Jun 27 03:42 sys
drwxrwxrwt    2 root     root          4096 Jun  7 17:34 tmp
drwxr-xr-x    3 root     root          4096 Jun  7 17:34 usr
drwxr-xr-x    1 root     root          4096 Jun 27 03:42 var
/ # 
```

You could see the directory "/data" is already present in the pod.
{: style="text-align: justify;"}

```markdown
/ # df -Ph /data
Filesystem                Size      Used Available Capacity Mounted on
/dev/mapper/vgvagrant-root           61.3G      3.5G     54.7G   6% /data
/ # 
```
Note: - The '/data' mounted from the worker node.
{: .notice--info}
{: style="text-align: justify;"}

We will place some files under the '/data' directory.
{: style="text-align: justify;"}

```markdown
/ # cd /data/
/data # ls -l 
total 0
/data # touch testfile1
/data # echo "My data to Test the Persistency" > MyDataFile.txt
/data # ls -l 
total 4
-rw-r--r--    1 root     root            32 Jun 27 03:41 MyDataFile.txt
-rw-r--r--    1 root     root             0 Jun 27 03:40 testfile1
/data # cat MyDataFile.txt 
My data to Test the Persistency
/data # 
```
### Recreate the pod with the volume.

Now the files are placed under the pod 'test-data'. We will recreate the pod. 
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl delete pod test-data 
pod "test-data" deleted
rajith@k8s-master:~$ 
rajith@k8s-master:~$ kubectl create -f TestData.yaml 
pod/test-data created
rajith@k8s-master:~$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
test-data   1/1     Running   0          8s
rajith@k8s-master:~$
```
### Connect to the pod and verify the data

The pod 'test-data' is recreated. We will connect to it and verify the data.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl exec -it test-data -- sh
/ # df -Ph /data
Filesystem                Size      Used Available Capacity Mounted on
/dev/mapper/vgvagrant-root           61.3G      3.5G     54.7G   6% /data
/ #
```
The directory is present and it is mounted. We will verify the data.

```markdown
/ # cd /data
/data # ls -l 
total 4
-rw-r--r--    1 root     root            32 Jun 27 03:41 MyDataFile.txt
-rw-r--r--    1 root     root             0 Jun 27 03:40 testfile1
/data # cat MyDataFile.txt 
My data to Test the Persistency
/data # 
```

Yes, it is there.This is how we will preserve the data. 
{: .notice--success}
{: style="text-align: justify;"}


But the volume 'hostPath' is bounded to the worker node which the pod is running. If the pod is recreated/relocated to another node then the data will be lost. To avoid such a situation during our demo I made the other two nodes unschedulable.
{: .notice--warning}
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl get node
NAME         STATUS                     ROLES                  AGE   VERSION
k8s-master   Ready                      control-plane,master   25d   v1.21.1
node-1       Ready,SchedulingDisabled   <none>                 25d   v1.21.1
node-2       Ready,SchedulingDisabled   <none>                 25d   v1.21.1
node-3       Ready                      <none>                 25d   v1.21.1
rajith@k8s-master:~$ kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
test-data   1/1     Running   1          85m   10.32.0.2   node-3   <none>           <none>
rajith@k8s-master:~$ 
```
Our demo was happening under node-3, you can see the status of the other nodes. Those are not schedulable. 
{: style="text-align: justify;"}

If you need relocatable pods then you need to choose the other solutions which support that.
{: .notice--danger}
{: style="text-align: justify;"}

## Persistent Volume definition file
We will have a look at the persistent volume definition file.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat PersistentVolume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-vol
spec:
  capacity:
    storage: 10Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
rajith@k8s-master:~$ 
```

This defenition file is self explnatory.
* The name of the volume is 'my-vol'.
* The capacity is 10Mi.
* The host path is "/mnt/data".

## Create the Persistent Volume
We will create the Persistent volume.

```markdown
rajith@k8s-master:~$ kubectl create -f PersistentVolume.yaml
persistentvolume/my-vol created
rajith@k8s-master:~$ kubectl get persistentvolume
NAME     CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
my-vol   10Mi       RWO            Retain           Available                                   4s
rajith@k8s-master:~$ 
```

A persistent volume looks like this. If we need to utilize it,  we need to create a 'persistent volume claims' and mount it to the corresponding pod as we have done in our demo. We will look into those details in our upcoming series. 
{: .notice--info}
{: style="text-align: justify;"}

**With this, we completed our introductory series. Thanks for joining me. We will meet again in the series for Pod .. -- Need to replace the name.**
{: .notice--success}
{: style="text-align: justify;"}

<script data-ad-client="ca-pub-3571483507017585" async src="https://pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



