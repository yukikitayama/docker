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

`kubectl run <image-name> --image <image-name>` deploy a container by creating a pod

`kubectl get pods` lists pods

`kubectl create -f <pod-definition-yaml-filename>` creates a pod

`kubectl get all` sees all objects

## Pod

Smallest object that we can create in Kubernetes. Basic unit of deployment. Containers are always in pods

Usually one pod to one container relation. Multi-container pod (One pod has multiple containers) typically doesn't have
same type of containers, but the applications coordinate with a network.

A node can have multiple pods

Pod is concept unique to Kubernetes, idea of resource type, wrapping around one or more containers that all share the
same IP address, and same deployment mechanism

You cannot create a container directly in K8s.

We create Pods, then K8s creates the containers inside it.

kubelet tells the container runtime to create containers for us.

`kubectl run <pod-name> --image <image-name>` creates a pod

`kubectl get pods` lists the pods

`kubectl delete pod <pod-name>`

### Update existing pod

If there's a pod definition file, edit it and use it to create a new pod

If there's not a pod definition file, extract the definition file by `kubectl get pod <pod-name> -o yaml > pod-definition.yaml`

Use `kubectl edit pod <pod-name>` to edit pod properties.

## Deployment

The most popular resource type you create in K8s.

Similar to Swarm Service, creating one or more pods

Deployment creates ReplicaSet for each new deployment type, then ReplicaSet create s Pods. ReplicaSet is kind of a version
of a deployment.

`kubectl create deployment <deployment-name> --image <image-name>` creates a production deployment

`kubectl get pods`

`kubectl get all` with `-o wide` or `-o yaml`

`kubectl delete deployment <deployment-name>`

To scale up, `kubectl scale deploy <deployment-name> --replicas <number>`

`kubectl describe deploy <deployment-name>`

## Controller

Create and update pods and other objects

- Deployment
- ReplicaSet
- StatefulSet

### Replication controller (Replica Set)

It runs multiple instances of a single pod in Kubernetes cluster to provide **high availability**

It creates a new pod if the existing pod fails 

Replication controller also works as load balancing and scaling by creating more pods in the same node or create more pods
in a different node if the number of users get bigger.

Replication controller is old technology and replaced by **replica set**.

`kind: ReplicationController`

`replicas` is a child of `spec`

`kubectl create -f rc-definition.yaml`

`kubectl get replicationcontroller`

`kubectl get pods`

```
spec:
  template:
    metadata:
      name: <pod-name>
      labels:
        xxx
    spec:
      containers:
      - name: <container-name>
        image: <image-name>

  replicas: 3
```

## Service

Network endpoint to connect to a pod

## Namespace

Filtered group of objects in cluster

No security feature in Kubernetes. Different from namespace security feature of Docker.

## Node

A cluster can have multiple nodes

# Cluster

A group of nodes

## YAML

Required fields
- apiVersion
- kind
  - Type of object; Pod, Service, etc
- metadata
  - Name, label
- spec
  - `containers` is list/array
    - It has `name` and `image`

## Service

To find a service,
- Environment variables
- DNS (Common)

## Calico

The default network for Kubernetes by Universal Control Plane.

## Command line

`kubectl version` to check whether it's working

`kubectl run` single pod per command

`kubectl create` creates some resources via CLI or YAML

`kubectl apply` creates and updates anything via YAML.

## Use Kubernetes

`https://killercoda.com/`

`katacoda.com`

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

## Network

- ClusterIP
  - Default method of exposing a service internally
  - Services will receive a cluster-scoped Virtual IP address also known as ClusterIP.
- NodePort
  - Allow services to be accessed from outside the cluster
- LoadBalancer
  - Allow services to be accessed from outside the cluster

