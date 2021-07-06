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

Pod creation was covered in the previous series. We will look into it from a different angle. We will use the pod definition file created in the previous module to create a pod.
{: style="text-align: justify;"}
If you do not have any previous experience on the pod, have a look at the previous series[Kubernetes in 7 days? In a week !!!  --> Creating Pod](https://www.rajith.in/Kubernetes/KubernetesPart4_Pods/#creating-a-pod) and come back here.
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
It is containerising. How do we check the details of it? I mean, to see what is happening in the background. 🤔

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
Yes, the command **kubectl describe pod my-first-pod** give the details.

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
Now it is about the **event,** the actual part which we thought of discussing here. 

In the **event,** the first line talks about pod scheduling. 
* It used the 'default scheduler to schedule the pod. We discussed [scheduler](https://www.rajith.in/Kubernetes/KubernetesPart2/#control-plane-components--master-node-) in our first series, **control plane components.**
* It says it assigned to node-3.
How do we verify? Yes, we knew that. 
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
The next two lines says, it is pulling the image "Nginx" from the docker repository. It took 8s to pull the image. 

```yaml
  Normal  Created    8s    kubelet            Created container my-first-pod
  Normal  Started    8s    kubelet            Started container my-first-pod
```
And the last two line says it created the pod named 'my-first-pod', it is started.
{: style="text-align: justify;"}

That's it, initially by looking at the big output we thought it is rocket science. It is as simple as that of our first standard textbook. 
{: .notice--success}
{: style="text-align: justify;"}

## let us watch the pod creation output step by step

Now let us see the pod creation output at each step. To facilitate that, I am using a simile for loop with a filtering option in Unix.

To see the subsequent ten lines after a string. Use the grep command with this combination. **grep -A 10 Events**
{: .notice--info}
{: style="text-align: justify;"}

We will delete and recreate the same pod and see the events. To view only the events, we will form a Unix command combination with a simple for loop. 
{: style="text-align: justify;"}
We will see in action. 

```yaml
rajith@k8s-master:~$ kubectl create -f my-first-pod.yaml 
pod/my-first-pod created
rajith@k8s-master:~$ for i in {1..5} ; do kubectl describe pod my-first-pod |grep -A 10 Events  ; sleep 2  ;done
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  2s    default-scheduler  Successfully assigned default/my-first-pod to node-3     ##<--- It is scheduled on node-3 
  Normal  Pulling    0s    kubelet            Pulling image "nginx"																	##<--- Started pulling images
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  4s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    2s    kubelet            Pulling image "nginx"
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  6s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    4s    kubelet            Pulling image "nginx"
  Normal  Pulled     1s    kubelet            Successfully pulled image "nginx" in 2.880713986s					##<--- Image pull completed.
  Normal  Created    1s    kubelet            Created container my-first-pod													##<---  It created the container
  Normal  Started    1s    kubelet            Started container my-first-pod												    ##<---  And started
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  8s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    6s    kubelet            Pulling image "nginx"
  Normal  Pulled     3s    kubelet            Successfully pulled image "nginx" in 2.880713986s
  Normal  Created    3s    kubelet            Created container my-first-pod
  Normal  Started    3s    kubelet            Started container my-first-pod
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  10s   default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    8s    kubelet            Pulling image "nginx"
  Normal  Pulled     5s    kubelet            Successfully pulled image "nginx" in 2.880713986s
  Normal  Created    5s    kubelet            Created container my-first-pod
  Normal  Started    5s    kubelet            Started container my-first-pod
rajith@k8s-master:~$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
my-first-pod   1/1     Running   0          19s
rajith@k8s-master:~$ 
```
 Hope this gave you a better idea.
 {: style="text-align: justify;"}
 
 
##  Now let us see some failed events.
 
 ```yaml
 rajith@k8s-master:~$ kubectl delete pod my-first-pod 
pod "my-first-pod" deleted
rajith@k8s-master:~$ kubectl create -f my-first-pod.yaml 
pod/my-first-pod created
rajith@k8s-master:~$ for i in {1..3} ; do kubectl describe pod my-first-pod |grep -A 10 Events  ; sleep 2  ;done
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  2s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    1s    kubelet            Pulling image "nginxx"
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  4s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal  Pulling    3s    kubelet            Pulling image "nginxx"
Events:
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  6s    default-scheduler  Successfully assigned default/my-first-pod to node-3
  Normal   Pulling    5s    kubelet            Pulling image "nginxx"
  Warning  Failed     0s    kubelet            Failed to pull image "nginxx": rpc error: code = Unknown desc = Error response from daemon: pull access denied for nginxx, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     0s    kubelet            Error: ErrImagePull
  Normal   BackOff    0s    kubelet            Back-off pulling image "nginxx"
  Warning  Failed     0s    kubelet            Error: ImagePullBackOff
rajith@k8s-master:~$ 
 ```
We executed the same step but, in the third occurrence, the image pull failed this time.😫
 {: style="text-align: justify;"}
What happened? 🤔
{: style="text-align: justify;"}
Have a close look at the image which it is trying to pull. 🧐 
{: style="text-align: justify;"}
```yaml 
  Warning  Failed     4s    kubelet            Failed to pull image "nginxx": rpc error: code = Unknown desc = Error response from daemon: pull access denied for nginxx, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     4s    kubelet            Error: ErrImagePull
```
It is "nginxx" docker repository does not contain such images. That is the reason for the failure. It tried pulling the image, but the image is not available in the repository.
{: style="text-align: justify;"}
I modified our pod definition file to create this issue. 😜
{: style="text-align: justify;"}

```yaml
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
     image: nginxx							##<-- See the image name 
    # The container specification section ends here
rajith@k8s-master:~$ 
```
## Let us see another scenario

I corrected the pod definition file and recreated the pod.
{: style="text-align: justify;"}
Let us see what is happening? 
{: style="text-align: justify;"}


```yaml
rajith@k8s-master:~$ kubectl delete pod my-first-pod 
pod "my-first-pod" deleted
rajith@k8s-master:~$ vi my-first-pod.yaml 
rajith@k8s-master:~$ kubectl create -f my-first-pod.yaml 
pod/my-first-pod created
rajith@k8s-master:~$ for i in {1..5} ; do kubectl describe pod my-first-pod |grep -A 10 Events  ; sleep 2  ;done
Events:
  Type     Reason            Age              From               Message
  ----     ------            ----             ----               -------
  Warning  FailedScheduling  1s (x2 over 3s)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
Events:
  Type     Reason            Age              From               Message
  ----     ------            ----             ----               -------
  Warning  FailedScheduling  3s (x2 over 5s)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
Events:
  Type     Reason            Age              From               Message
  ----     ------            ----             ----               -------
  Warning  FailedScheduling  5s (x2 over 7s)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
Events:
  Type     Reason            Age              From               Message
  ----     ------            ----             ----               -------
  Warning  FailedScheduling  7s (x2 over 9s)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
Events:
  Type     Reason            Age               From               Message
  ----     ------            ----              ----               -------
  Warning  FailedScheduling  9s (x2 over 11s)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
rajith@k8s-master:~$ 
```

Now it is failing in the first step itself. It is unable to schedule the pod anywhere.
{: style="text-align: justify;"}


Why? Yes, we can not schedule anything on aster node. But what about the other three nodes?
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS                        ROLES                  AGE   VERSION
k8s-master   Ready                         control-plane,master   34d   v1.21.1
node-1       Ready,SchedulingDisabled      <none>                 34d   v1.21.1
node-2       NotReady,SchedulingDisabled   <none>                 34d   v1.21.1
node-3       Ready,SchedulingDisabled      <none>                 34d   v1.21.1
rajith@k8s-master:~$ 
```

I manually made the other node non-schedulable. 
{: style="text-align: justify;"}


## Try scheduling it again

```yaml
rajith@k8s-master:~$ kubectl get nodes
NAME         STATUS                        ROLES                  AGE   VERSION
k8s-master   Ready                         control-plane,master   34d   v1.21.1
node-1       Ready                         <none>                 34d   v1.21.1
node-2       NotReady,SchedulingDisabled   <none>                 34d   v1.21.1
node-3       Ready,SchedulingDisabled      <none>                 34d   v1.21.1
rajith@k8s-master:~$ for i in {1..5} ; do kubectl describe pod my-first-pod |grep -A 10 Events  ; sleep 2  ;done
Events:
  Type     Reason            Age                 From               Message
  ----     ------            ----                ----               -------
  Warning  FailedScheduling  61s (x13 over 12m)  default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 3 node(s) were unschedulable.
  Warning  FailedScheduling  49s                 default-scheduler  0/4 nodes are available: 1 node(s) had taint {node-role.kubernetes.io/master: }, that the pod didn't tolerate, 1 node(s) had taint {node.kubernetes.io/unschedulable: }, that the pod didn't tolerate, 2 node(s) were unschedulable.
  Normal   Scheduled         38s                 default-scheduler  Successfully assigned default/my-first-pod to node-1                 ##<-- It scheduled the pod on node-1.
  Normal   Pulling           37s                 kubelet            Pulling image "nginx"
  Normal   Pulled            34s                 kubelet            Successfully pulled image "nginx" in 2.946054283s
  Normal   Created           34s                 kubelet            Created container my-first-pod
  Normal   Started           34s                 kubelet            Started container my-first-pod

```
I made node-1 ready. Immediately after that, it scheduled the pod on node-1. See the events. 


[Our next module is on deployment, Click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



