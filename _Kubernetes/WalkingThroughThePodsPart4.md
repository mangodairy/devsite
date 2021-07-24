---
title: "Pod Classifications in Kubernetes"
excerpt: "In this module, we will go through the different types of pods available in Kubernetes. Like Single Container Pods, Multi Container Pods, Init Containers and the Static Pod."
header:
  overlay_color: "#80aaff              "
  teaser: /assets/images/kuberneties/Pods/Pods-4.png
  excerpt: "Get a Shell to a Running Container."
  show_overlay_excerpt: false
  show_date: true
sidebar:
  - title: "Introduction to Kubernetes"
    image: /assets/images/kuberneties/Pods/Pods-4.png
    image_alt: "logo"
    text: "Written by"
  - title: "Rajith P"
    text: "Pod Classifications in Kubernetes "

tags:
  - table of contents
toc: true
toc_label: " "
toc_icon: "Pod Classifications in Kubernetes"
toc_sticky: true
---

# Pod Classifications

There are different ways of classifying pods. In this module, we will stick to the below classification.  
* Single Container Pods.
* Multi Container Pods.
* Static Pods.
* Init Containers.
{: style="text-align: justify;"}



# Single Container Pods

The single container pod is the most common use case in the Kubernetes. Throughout the previous modules, we saw many such use cases. As the name implies, the single container pod contains only one container within the pod.  We saw many such use-cases. So we will not detail about it here. But how to identify the number of containers inside the pod without going into the "kubectl describe pod <pod name >" option?
{: style="text-align: justify;"}
```yaml
rajith@Shiva:/k8s/vagrant-iac-usecases/virtualbox-kubernetes$ kubectl get pod 
NAME     READY   STATUS    RESTARTS   AGE
centos   1/1     Running   7          3d11h
nginx    1/1     Running   4          4d1h
rajith@Shiva:/k8s/vagrant-iac-usecases/virtualbox-kubernetes$ 
```
Here the second field: "REDAY" indicate the number of the running container inside the pod /total number of the container inside the pod. It means both the above pod has only a single container in it and it is ready. 
{: style="text-align: justify;"}

# Multi Container Pods

We had touched upon multi-container pods in the previous series. [Please have a look.](https://www.rajith.in/Kubernetes/KubernetesPart4_Pods/#multi-container-pod) It is a pack of multiple containers within the pod which need to work together. 
There are many use cases like,
* data pullers
* data pushers
* proxies
{: style="text-align: justify;"}
Instead of going with this approach, we will take a slightly different approach to understand this. Once we get the concept, we can integrate multiple containers in a pod as per our requirements.
{: style="text-align: justify;"}
So we will forget all the naming. We are going to start with a Single Container pod and developing it from there. 
{: style="text-align: justify;"}

Here is the two definition file that we are going to use here. 
{: style="text-align: justify;"}


By now, we have used this definition file many times, so we are not going through it. We will see the content of it. 
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ cat NginxPod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels :
    key : test
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html/
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: DirectoryOrCreate
rajith@k8s-master:~$ 
```
It is the usual nginx pod definition file. The only difference is we used a volume to keep the HTML file of the nginx server.
{: style="text-align: justify;"}

**The option "DirectoryOrCreate" is new to us.  It is to create the "/data" directory under the host if it is not present.** 
{: style="text-align: justify;"}

Let us create the pod with this pod definition file.
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create -f NginxPod.yaml
pod/nginx created
rajith@k8s-master:~$ kubectl get pod
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          7s
rajith@k8s-master:~$ 
```
The pod is created and running. Let us check the webpage.
{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-01.png" alt="Image 1">
  <figcaption>Unable to Connect to the web page </figcaption>
</figure>

Yes, we did not create the service to expose the pod outside.
{: style="text-align: justify;"}


Let us create the service.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat Nginx-service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort : 30007
  selector:
    key : test
  type: NodePort
rajith@k8s-master:~$ kubectl create -f Nginx-service.yaml
service/nginx created
rajith@k8s-master:~$ kubectl get svc
NAME         TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1     <none>        443/TCP        43d
nginx        NodePort    10.99.9.171   <none>        80:30007/TCP   5s
rajith@k8s-master:~$ 
```

Service created, now the webpage should be available outside the cluster with the port. Let us see.
{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-02.png" alt="Image 1">
  <figcaption>403 Forbidden</figcaption>
</figure>

**403 Forbidden** 
{: style="text-align: justify;"}

?? 😱. What is happening? 
{: style="text-align: justify;"}


Let us examine the pod. 
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl get pod -o wide
NAME    READY   STATUS    RESTARTS   AGE   IP          NODE     NOMINATED NODE   READINESS GATES
nginx   1/1     Running   0          11m   10.40.0.1   node-1   <none>           <none>
rajith@k8s-master:~$ 
```
Yes, the pod is running on node-1. Let us get into the pod.
{: style="text-align: justify;"}


```yaml
rajith@k8s-master:~$ kubectl exec -it nginx -- bash 
root@nginx:/# cd /usr/share/nginx/html/
root@nginx:/usr/share/nginx/html# ls -l 
total 0
root@nginx:/usr/share/nginx/html# 
```

Yes, this time, we mounted "/usr/share/nginx/html" on "/data" directory, which is the folder from the worker node. Since it was not available, it got created along with the pod creation. So we don't have index.html to serve the webpage.
{: style="text-align: justify;"}

Let us create it. 
{: style="text-align: justify;"}

```html
root@nginx:/usr/share/nginx/html# ls -l 
total 0
root@nginx:/usr/share/nginx/html# echo -e '<html>\n<html>\n\t<body>\n\t\t<h1>Created the "index.html" inside the Volume </h1>\n\t</body>\n</html>' > /usr/share/nginx/html/index.html
root@nginx:/usr/share/nginx/html# cat /usr/share/nginx/html/index.html
<html>
<html>
	<body>
		<h1>Created the "index.html" inside the Volume </h1>
	</body>
</html>
root@nginx:/usr/share/nginx/html# 
```

Now the 'index.html' file is created, let us see the webpage.
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-03.png" alt="Image 1">
  <figcaption>Created the "index.html" inside the Volume</figcaption>
</figure>

Yes, it is okay. 👍
{: style="text-align: justify;"}


The directory was from node-1. Let us see the directory content from node-1.
{: style="text-align: justify;"}
```html
rajith@node-1:~$ sudo su - 
root@node-1:~# cd /data/
root@node-1:/data# ls -ltrh 
total 4.0K
-rw-r--r-- 1 root root 94 Jul 15 15:30 index.html
root@node-1:/data# cat index.html 
<html>
<html>
	<body>
		<h1>Created the "index.html" inside the Volume </h1>
	</body>
</html>
root@node-1:/data# 
```
Yes, the same file is available here.
{: style="text-align: justify;"}

Let us modify the content from node-1.
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-04.png" alt="Image 1">
  <figcaption>Modified the content from node-1</figcaption>
</figure>

Yes, the change is reflecting.
{: style="text-align: justify;"}


We did all these circuses, okay looks good. But what does the 'multi-container pod means? 🤔
{: style="text-align: justify;"}

Wait, we are coming to that. Now let us add a 'CentOS' container to this pod.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat NginxWithCentosPod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels :
    key : test
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: test-volume
  - name : centos
    image : centos 
    volumeMounts:
    - mountPath: /html
      name: test-volume
    command : [ "sleep" , "5000" ]
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: DirectoryOrCreate
rajith@k8s-master:~$ 
```

Let us examine the pod definition file.
* Now this definition file has two containers, nginx and centos.
* Both mounted the same volume named 'test-volume'.
* The pod nginx mounted the volume under '/usr/share/nginx/html'.
* The pod centos mounted the volume under '/html'.
* It means both the container sharing the same volume. 

Let us delete the existing setup and recreate the pod with two containers.
{: style="text-align: justify;"}

Note: - We are not deleting the service. 
{: .notice--info}
{: style="text-align: justify;"}

```markdown
rajith@k8s-master:~$ kubectl create -f NginxWithCentosPod.yaml
pod/nginx created
rajith@k8s-master:~$ kubectl get pods 
NAME    READY   STATUS              RESTARTS   AGE
nginx   0/2     ContainerCreating   0          6s
rajith@k8s-master:~$ kubectl get pods 
NAME    READY   STATUS    RESTARTS   AGE
nginx   2/2     Running   0          32s
rajith@k8s-master:~$ 
```
The pod started the two containers. You can see the number of containers under the field READY.
{: style="text-align: justify;"}

Let us verify the web page. It should show the '403 Forbidden' error.

{: style="text-align: justify;"}
<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-02.png" alt="Image 1">
  <figcaption>403 Forbidden</figcaption>
</figure>

Yes, it is. 

We will create the 'index.html' from the container nginx.

```html
rajith@k8s-master:~$ kubectl exec -it nginx -c nginx  -- bash 
root@nginx:/# ls -l /usr/share/nginx/html/ 
total 0
root@nginx:/# echo -e '<html>\n<html>\n\t<body>\n\t\t<h1>Created the 'index.html' inside the Volume from the nginx container. </h1>\n\t</body>\n</html>' > /usr/share/nginx/html/index.html
root@nginx:/# cat /usr/share/nginx/html/index.html
<html>
<html>
	<body>
		<h1>Created the index.html inside the Volume from the nginx container. </h1>
	</body>
</html>
root@nginx:/# 
```
<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-05.png" alt="Image 1">
  <figcaption>Created the 'index.html' inside the Volume from the nginx container. </figcaption>
</figure>

Let us edit the file from the centos container.

```html
rajith@k8s-master:~$ kubectl exec -it nginx -c centos  -- bash 
[root@nginx /]# cd /html/
[root@nginx html]# ls -l 
total 4
-rw-r--r-- 1 root root 118 Jul 15 17:19 index.html
[root@nginx html]# vi index.html 
[root@nginx html]# cat index.html 
<html>
<html>
	<body>
		<h1>Created the index.html inside the Volume from the nginx container. </h1>
		<h1>           Added the content from the centos container.            </h1>
	</body>
</html>
[root@nginx html]# 
```
<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-06.png" alt="Image 1">
  <figcaption>Created the 'index.html' inside the Volume from the nginx container. </figcaption>
</figure>


Yes,  now we edited the nginx content from the centos container. But why it is needed. We could have done it from the nginx container itself. 
{: style="text-align: justify;"}
It is just an example. The actual use case is different. 
{: style="text-align: justify;"}


```html
[root@nginx html]# curl -OL https://raw.githubusercontent.com/iamrajith/k8s/main/index.html
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   439  100   439    0     0   2162      0 --:--:-- --:--:-- --:--:--  2162
[root@nginx html]# cat index.html 
<!DOCTYPE html>
<html>
<body>

<h1 style="background-color:DodgerBlue;">Multi Container Pods.</h1>

<p style="background-color:Tomato;">
The Pods that run multiple containers that need to work together is called Multi Container pods.  These pods share resources like filesystems and IP addresses. The primary purpose of the multi containers pod is to support helper applications that assist the main application. 
</p>

</body>
</html>
[root@nginx html]# 
```
See, now we downloaded an 'index.html' from the git repo.
{: style="text-align: justify;"}


Let us have a look at the web page. 
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-07.png" alt="Image 1">
  <figcaption>The we page from Git </figcaption>
</figure>

Yes, the page is modified.
{: style="text-align: justify;"}
The same way we can use, git or any tool which can not be used or installed in the actual application container because of,
* Some security/compliance issues.
* To reduce the application container size.
* Or due to the tool compatibility issues. 
* And many more. We can not list all of it here.
{: style="text-align: justify;"}

I  hope you got some idea about the multi-pod containers.
{: style="text-align: justify;"}

In multi-pod containers, we again have classifications. 
* Sidecar containers
* Ambassador containers
* Adapter containers
{: style="text-align: justify;"}

We will consider it in another series. 
{: style="text-align: justify;"}

We will go back to our example. Now we have done the download manually. But is this the correct way of doing it? 
{: style="text-align: justify;"}


No, it should integrate within the container.
{: style="text-align: justify;"}

Let us do that.
{: style="text-align: justify;"}

We will do the cleanup first.
{: style="text-align: justify;"}


```markdown
rajith@k8s-master:~$ kubectl delete pod nginx 
pod "nginx" deleted
rajith@k8s-master:~$ ssh node-1
rajith@node-1's password: 
rajith@node-1:~$ sudo su - 
root@node-1:~# rm -rf /data
root@node-1:~# logout
rajith@node-1:~$ logout
Connection to node-1 closed.
rajith@k8s-master:~$ kubectl get pods 
No resources found in default namespace.
rajith@k8s-master:~$ 
```
Cleanup completed.
{: style="text-align: justify;"}

Now let us modify the centos command section to automatically donload the index.html during the container startup. 
{: style="text-align: justify;"}
```yaml
rajith@k8s-master:~$ cat NginxWithCentosPod-2.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels :
    key : test
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: test-volume
  - name : centos
    image : centos 
    volumeMounts:
    - mountPath: /html
      name: test-volume
    #command : [ "sleep" , "5000" ]
    command : [ 'curl' , '-OL' , 'https://raw.githubusercontent.com/iamrajith/k8s/main/index.html' , 'sleep' , '5000' ]
    workingDir: /html
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: DirectoryOrCreate
rajith@k8s-master:~$ 
```
Please have a look at the command section.
{: style="text-align: justify;"}

```yaml 
    command : [ 'curl' , '-OL' , 'https://raw.githubusercontent.com/iamrajith/k8s/main/index.html' , 'sleep' , '5000' ]
    workingDir: /html
```
Here is the change we made in the command section of the centos pod.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ kubectl create -f NginxWithCentosPod-2.yaml
pod/nginx created
rajith@k8s-master:~$ kubectl get pod 
NAME    READY   STATUS              RESTARTS   AGE
nginx   0/2     ContainerCreating   0          6s
rajith@k8s-master:~$ kubectl get pod 
NAME    READY   STATUS    RESTARTS   AGE
nginx   2/2     Running   0          14s
rajith@k8s-master:~$ 
```
Let us see the web page.
{: style="text-align: justify;"}

<figure>
  <img src="/assets/images/kuberneties/Pods/PodClassificationsInKubernetes-07.png" alt="Image 1">
  <figcaption>The we page from Git </figcaption>
</figure>

Yes, it is working as expected. ✌️
{: style="text-align: justify;"}

All good, let us windup. Wait, **once the download is complete, our centos container is sleeping 💤. Do we need someone sleeping when others are working?**
{: style="text-align: justify;"}

# Init Containers

That is the purpose of an init container. Init container start before the application container and end on its completion. 
* Init containers are like regular containers, except:
* Init containers always run to completion.
* Each init container must complete successfully before the next one starts.
* We can have multiple init containers within a pod.
{: style="text-align: justify;"}

**We were talking about the centos container in our previous example. We don't need it to be running once it downloaded the file. Else it will be wasting the resource in the cluster. We don't need to waste CPU, memory and storage for a sleeping container.**
{: style="text-align: justify;"}
We will rewrite the pod definition file to make the centos container an init container.
{: style="text-align: justify;"}

```yaml
rajith@k8s-master:~$ cat NginxWithCentosInitConatiner.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels :
    key : test
spec:
  containers:
  - image: nginx
    name: nginx
    volumeMounts:
    - mountPath: /usr/share/nginx/html
      name: test-volume
  initContainers:
  - name : centos
    image : centos 
    volumeMounts:
    - mountPath: /html
      name: test-volume
    #command : [ "sleep" , "5000" ]
    command : [ 'curl' , '-OL' , 'https://raw.githubusercontent.com/iamrajith/k8s/main/index.html' ]
    workingDir: /html
  volumes:
  - name: test-volume
    hostPath:
      # directory location on host
      path: /data
      # this field is optional
      type: DirectoryOrCreate
rajith@k8s-master:~$ 
```
Here we brought the centos container under the 'init containers.
{: style="text-align: justify;"}

We will create the pod.
{: style="text-align: justify;"}
```markdown
rajith@k8s-master:~$ kubectl create -f NginxWithCentosInitConatiner.yaml
pod/nginx created
rajith@k8s-master:~$ kubectl get pods
NAME    READY   STATUS     RESTARTS   AGE
nginx   0/1     Init:0/1   0          3s
rajith@k8s-master:~$ kubectl get pods
NAME    READY   STATUS            RESTARTS   AGE
nginx   0/1     PodInitializing   0          5s
rajith@k8s-master:~$ kubectl get pods
NAME    READY   STATUS            RESTARTS   AGE
nginx   0/1     PodInitializing   0          7s
rajith@k8s-master:~$ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          10s
rajith@k8s-master:~$ 
```

See the difference during the pod creation, 
{: style="text-align: justify;"}
Initially, the status field shows 'Init:0/1'. That is during the time of init container startup.
{: style="text-align: justify;"}
Once it was complete, it started initializing the nginx pod.
{: style="text-align: justify;"}
Also, see now the READY field has only one container in it.
{: style="text-align: justify;"}

Look at the kubectl describe output.
{: style="text-align: justify;"}

To make it easy. We will look at the portion which shows the status of the init container.
```yaml
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Fri, 16 Jul 2021 02:48:29 +0000
      Finished:     Fri, 16 Jul 2021 02:48:29 +0000
```
It terminated on completion. I hope you got clarity on the init container. Let us explore the static pod.
{: style="text-align: justify;"}


































{: style="text-align: justify;"}
That is it for now. Let us meet again in the next module. 
{: style="text-align: justify;"}

[Our next module is on deployment, Click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>



