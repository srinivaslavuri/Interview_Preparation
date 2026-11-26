## KUBERNETES ##
* kubernets also called as k8s.
* k8s is like docker swarm.
* k8s ia a container orchestration technology that manages docker containers.
* k8s originated at google. Now it is open source.
* k8s cluster means: Master and Node.

## Features of kubernetes ##
* Auto management of multiple containers running on different machines (orchestration)
* Scaling (auto relation of replicated containers)
* Healing (new containers in place of crashed containers)
* Load balance (node / pod failure)
* versioning (going forward / back to next / previous version)

----------------------------------

## constituents of node ##
* kubelet 
1. Agent running on the node.
2. Listens to kubernetes master (eg: pod management request)
3. Port 10255
4. Sends successfull report to master

* Docker 
1. Workers with kubelet
2. pulling images, starting & stopping containers
3. Exposing containers on ports specified in manifest

* kube-proxy
1. kube-proxy runs on each node & it is responsible to setup network (communication)
2. CNI plugin is responsible to assign ( private ip) to pod

------------------------------------

## Pod ##
* kubernetes needs pods because pods make networking, scaling, healing, and managing containers easy at large scale.
1. Outer cover of containers
2. Consists of 1 or more containers
3. In real-time, every pod ontains only one container.
4. Entire pod will be hosted on the same node (scheduler will decide about which node)
5. It represents the smallest deployable unit in the kubernetes object model.

-------------------------------------

* Every task in k8s is considered as "object"
* In swarm, we call "service". in k8s we call it "Object".

* Spec: What you define in YAML file
* Status: Current status of cluster

* by defaukt container runs on detached mode.

--------------------------------------

## Kubernetes Objects ##

The basic kubernetes objects include (eg: Car)

* What you want, you infom kubernetes in the from of an object.
1. Pod
2. Service
3. Volume
4. Namespace
5. ReplicaSet
6. Deployment
7. ConfigMaps
8. Secrets

----------------------------------------

## Kubernetes installation and configuration types ##

* All in one single node installation (like compose)
* Single-Master and multi-worker installation (like swarm)
* Multi-Master and multi-worker installation (Don't use much)

-----------------------------------------

## Install :

* Minicube (k8s package for single node)
* EKS (k8s package foe multi node)
* kubectl (User to k8s communication)

-----------------------------------------

## commands for kunernetes ##

1. kubectl version -- to check the version
2. eksctl create cluster --name srinu --region ap-south-1 --node-type t2.small
3. kubectl get nodes
4. kubectl apply -f pod1.yml
5. kubectl get pods/po/pod
6. kubectl get pods -o wide
7. kubectl describe pod testpod
8. 