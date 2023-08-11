# Kubernetes

Making many servers act like one is **container orchestration**.

Kubernetes is a popular container orchestrator.

It provides API and CLI to manage containers across servers.

Faster DevOps

Kubernetes is a series of containers, CLI's and configurations.

**k8s**, "k-eights", (There are 8 letters between k and s) kube for short

**kubectl** "cube control" is CLI to configure Kubernetes and manage apps

**Node** means a single server in the Kubernetes cluster

**Kubelet** is an agent running containers on nodes and talking to master (control plane).

**Kube-proxy** is in each node to control networking

**Control plane** (master) is a set of containers that manage the cluster. It includes API server, scheduler, controller
manager, etcd

**API server** is the way to talk to cluster and issue orders to it.

**Scheduler** decides how and where the containers are placed in the nodes

**Controller manager** looks at the state of whole cluster,

**etcd** is a distributed storage system for key-values

**Pod** is a basic unit of deployment. Containers are always in pods. Pod is running on a node and running containers.
Pods are the resource object that controls its individual containers on a node. We create pods, and then Kubernetes 
creates the container inside it.

**Controller** is used to control (create and update) pods

**Service** is endpoint given to a set of pods

**Namespace** is a filtered view in CLI, not security feature.

## Command line

`kubectl version` to check whether it's working

`kubectl run` single pod per command

`kubectl create` creates some resources via CLI or YAML

`kubectl apply` creates and updates anything via YAML.

## Use Kubernetes

`https://killercoda.com/`

## Installation

Use **Docker Desktop** to enable Kubernetes.

Read `https://cloud.google.com/blog/products/containers-kubernetes/kubectl-auth-changes-in-gke`

Install latest `gcloud` by `https://cloud.google.com/sdk/docs/install

`gcloud components install gke-gcloud-auth-plugin` to install Kubectl authentication plugin

`gke-gcloud-auth-plugin --verion` to verify installation

`https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/`

`kubectl version --client`

## Resources for creating pods

- Deployment
  - Not expected to terminate like web servers.
- ReplicaSet
    - Not expected to terminate like web servers.
- StatefulSet
    - Not expected to terminate like web servers.
- Job
  - Expected to terminate once work is complete like batch computation
- DaemonSet
  - Run one per eligible node

## Secret

Secret can be used with a pod by

- Files in a volume mounted on one or more of its containers
- Container environment variable
  - `env[].valueFrom.secretKeyRef` to place secret's name and key
- kubelet when pulling images for the pod

## Probe

**Readiness probe** checks the status of applications running inside pods. Perform customized health checks within Kubernetes environments.
Readiness probe indicates whether applications running in a container are ready to receive traffic.

- Container needs to work on loading large data during startup
- Make container be able to take itself down for maintenance
- Start sending traffic to Pod only when a probe succeeds.

**Liveness probe** indicates if the container is operating. If not, the kubelet kills and restarts the container.

**Startup probe** indicates if the application running in the container has started. If not, the kubelet kills and restarts
the container.

## Deployment

## Storage

**PersistentVolume** is storage in the cluster provisioned by an administrator, cannot be provisioned on-demand.

But **dynamic volume provisioning** allows storage volumes to be created on-demand, so it eliminates the need for 
cluster administrators to pre-provision storage.

Cluster users create **PersistentVolumeClaim** for suitable PersistentVolume. The users create a Pod that uses the
PersistentVolumeClaim as storage.

PersistentVolumeClaim to PersistentVolume binding is a **one-to-one mapping**, using ClaimRef which is a bi-directional
binding between PV and PVC.


PersistentVolumeClaim will remain unbound indefinitely if a matching volume doesn't exist and the claim can't be 
satisfied. Claims will be bound as matching volumes become available.

