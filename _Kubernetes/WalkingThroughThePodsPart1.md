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
  - title: "Walking through the pods"
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

## Kubernetes pods vs containers 

Pod, container, docker is the most common interchanged word in the Kubernetes world. So before going to the technical definition, we will clearly define what it is.

Logically a container is packed inside a pod in a Kubernetes cluster. A pod is a covering of one or more containers. A pod can have multiple containers running on it on a Kubernetes cluster.

Another common misunderstanding about the docker. What is docker? For better clarity, I am adding two words after docker.
{: style="text-align: justify;"}

* Docker image.
* Docker container.

The issue begins from our understanding of docker. Some of us consider the docker image and docker container are the same. Some of us don't know where to draw the line between them. And even some of us think the docker and Pod are the same. Don't worry, I had all this confusion at the beginning. We will clarify it.  

So let us start from the base. The flow looks like this.
{: style="text-align: justify;"}

Docker image -> Docker container -> Pods 

## Docker image

Docker images are read-only templates. If you are familiar with the Vmware template, you can relate it to the docker image. It is a mini OS template. Basically, a docker image is a file. It doesn't need any computing power ( CPU or memory ). Docker images are available/kept in,
{: style="text-align: justify;"}

* Docker hub. 
* Google container registry.
* Almost all the cloud providers option to create a docker registry.
* You can keep it in your private registry.
* Or directly in your system.

You can decide this based on where you are hosting the Kubernetes cluster, based on the compliance policy of your organisation, complexity of your environment etc...
{: .notice--info}
{: style="text-align: justify;"}

## Docker containers

Docker containers are runnable instances of a docker image. It requires CPU, memory and other computing resources to run.
{: style="text-align: justify;"}

> It is a mini VM. Since it called a microservice may be, we can call it a micro virtual machine :).
{: style="text-align: justify;"}

## Pod.
In simple words, a pod is a box that contains one or more containers. Pod share the filesystem. Even if you have multiple containers, it has only a single IP address for external communication. The communication within the pods between the containers happens through the localhost. Pods are the smallest deployable units of computing that we can create and manage in Kubernetes. 
{: style="text-align: justify;"}

{: .notice--success}
{: style="text-align: justify;"}

[Our next module is on deployment, Click here](https://rajith.in/Kubernetes/KubernetesPart5_Deployment-1/)

<div markdown="0"><a href="#" class="btn btn--success">Go back to the Top of the page </a></div>      [Info Button Text](https://mangodairy.github.io/devsite/Kubernetes/#kubernetes-in-7-days-in-a-week-){: .btn .btn--info}



