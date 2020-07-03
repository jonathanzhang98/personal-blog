---
title: Pods in Kubernetes
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2020-07-01 22:35:29
subtitle:
tags:
- kubernetes
- pods in kubernetes
categories:
- kubernetes
---
In docker, a container is the smallest unit, while in kubernetes, **pods** are the most basic building blocks. 

*Note: kubernetes can use container runtimes other than docker*

A **pod** contains one or more containers, and each **pod** has:
- A single IP address
- Share localhost
- A shared IPC space
- A shared network port range
- Shared volumes

> Everything in a **pod** is tightly coupled.

> A **pod** is a way to represent a running process in a cluster.  (A container is also just a running process.)

## Run Pods with kubectl

Before doing anything, we must create a **kubernetes cluster** first:

```bash
$ minikube start --vm-driver=virtualbox
```

We can run a pod using the following command:

```bash
$ kubectl run NAME --image=image [--env="key=value"] [--port=port] [--dry-run=server|client] [--overrides=inline-json] [--command] -- [COMMAND] [args...]
```

For example, you can a nginx server like following:

```bash
$ kubectl run nginx --image=nginx --generator "run-pod/v1"
pod/nginx created
```

We can verify that a pod was created by running:

```bash
$ kubectl get pods
NAME                    READY   STATUS    RESTARTS   AGE
nginx                   1/1     Running   0          4s
```

We can ssh into minikube to confirm that a container created from the latest nignx image is running inside of the cluster:

```bash
$ minikube ssh
$ docker container ls -f ancestor=nginx
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
4f5ceb7b8921        nginx               "/docker-entrypoint.…"   2 minutes ago       Up 2 minutes                            k8s_nginx_nginx_default_620758d2-c891-4ee4-a507-306257de8a74_0
$ exit
```

We can also link our docker client with the docker server inside of minikube:

```bash
$ eval $(minikube docker-env)
$ docker container ls -f ancestor=nginx
```

To delete a pod, run:

```bash
$ kubectl delete ([-f FILENAME] | [-k DIRECTORY] | TYPE [(NAME | -l label | --all)])
```

So we can run:

```bash
$ kubectl delete pod nginx
pod "nginx" deleted
```

### Why this is bad practice?

In the above, we manually created a pod running ngnix image. This is just a simple application with only one node. *What if we have a complex application which needs thousands of nodes?* Creating all these nodes manually is very tedious and error-prone. Even if we created all these nodes, we can't possibly reproduce this result easily or even correctly.

#### Infrastructure as Code

Instead of running pods in an **imperative** way, as shown above, we should run them in a **declarative** way. We want to define all the pods we need in files and let kubernetes to manage the process of creating all these pods. This way, our infrastructure can be documented, just like code, so we can easily store, share, and reproduce it.
