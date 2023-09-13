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

`kubectl run [OPTIONS] -- [COMMAND] [args...]`, this `--` double-dash separates the kubectl CLI options and allows us
to overwrite the Dockerfile CMD with a new command and arguments/options. You can also use `--command` instead of `--`,
but double-dash method is used in many Linux shells and doesn't require us to quote the whole command we want to overwrite .

`kubectl api-resources` lists all the resource names and their short names.

`kubectl logs <pod-name>` shows standard output of a container

## Imperative vs. declarative

People wanna know how we are supposed to be using it, what's the best way, but that's hard, because it depends on what you
want out of it.

Imperative
- Focus on how a program operates
- `kubectl run`, `kubectl create deployment`, `kubectl update`
- Imperative is easier when you know the state and at CLI, but not easy to automate

Declarative
- Focus on what a program should accomplish
- `kubectl apply -f my-resources.yaml`
- We don't know current state, but we only the end result
- Resources can be many
- Requires YAML
- Easy to automate

## kubectl apply configuration YAML

Declarative objects approach by `kubectl apply -f filename.yaml`

create/update resources in a file `kubectl apply -f myfile.yaml`

create/update a whole directory of yaml `kubectl apply -f myyaml/`

create/update from a URL `kubectl apply -f https://bret.run/pod.yaml`

`apiVerion:` API version for a kind
- `kuebctl api-versions`
- First we need to know `apiVerion` and `kind`

`kind:`
- `kubectl api-resources` gets a list of resources
- Some resources have multiple API's such as `deployments`

`metadata:`
- Only name is required

`spec:`
- All the actions

Use `---` to have multiple manifests in one YAML such as creating service and deployment together

`kubectl explain <kind-resource-name> --recursive` gives quick list of all the keys each `kind:` supports

`kubectl explain <kind-resource-name>.spec` drill down one key

`kubectl explain <kind-resource-name>.<key>.<further-key>`

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

Use `kubectl replace --force -f <definition-file.yaml>` to update pod

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

Service in Kubernetes is a stable address (network endpoint) to connect to a pod.

When you create a pod in Kubernetes, it won't automatically connect a pod to the external things. 

Service name we create becomes a part of DNS name

4 different types of services
- ClusterIP
  - For connection within cluster
  - default internal virtual IP
  - ClusterIP is cluster internal only, so we cannot `curl` it.
- NodePort
  - For connection outside cluster
  - Through IP addresses
  - Need to use high port number allocated on each node
  - Anyone can connect
  - You don't have to jump into a pod, but you can access by `curl` for example to test things.
- LoadBalancer
  - Mostly used in cloud
  - Controls a load balancer endpoint external to cluster
  - Creates NodePort + ClusterIP services, essentially automation
- ExternalName
  - Stuff in the cluster need to connect to outside cluster
  - Used less often
  - Adds CNAME DNS record to CoreDNS

Use `kubectl expose`

## DNS

Internal DNS is provided by **CoreDNS**

DNS-based service discovery, using hostnames to access Services

Only works for Services in the same Namespace

## Namespace

Filtered group of objects in cluster

No security feature in Kubernetes. Different from namespace security feature of Docker.

`Default` namespace is automatically created, when the number of applications we work with is small, we can stay in
default namespace.

`kube-system` internal purposes things isolated from a user to prevent overwrite

`kube-public` resources available to users

Going to enterprise, we can create different namespaces such as Dev, and Prod.

We can assign resource limits to each namespace

To DNS over different namespaces, `<service-name>.<namespace-name>.svc.cluster.local`

`kubectl get pods` list pods in `Default` namespace.

To list pods in a different namespace, `kubectl get pods --namespace=<namespace-name>`, or `-n`

To switch namespace to avoid using `--namespace` option all the time, `kubectl config set-context $(kubectl config current-context) --namespace=dev`

`kubectl get pods --all-namespaces` to list all namespaces, or `-A`

## Node

A cluster can have multiple nodes

# Cluster

A group of nodes

## Generators

`--dry-run=client -o yaml` outputs templates, and use those YAML defaults as a starting point.

Generators are opinionated defaults

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

## Dry run

## Label

Under `metadata:`. Meant to describe resource

`key: value` to identify resource, for example `env: prod`, `app: api`, `tier: frontend`

Use `-l key=value`

## Label selector

Links resource dependencies `selector:  app: <name>`, `selector:  matchLabels:  app: <name>`

Match up Services and Deployments

User labels and selectors to control which pods go to which nodes

Avoid **Taints** and **Tolerations** until you get advanced.

## StatefulSets

Resource type designed to run **database**

But avoid stateful workloads for first few deployments until you are good at the basics

Stateless is the basic approach for Docker, Swarm and Kubernetes.

Use **db-as-a-service** whenever you can.

## Volume

- Volumes
  - Tied to lifecycle of a Pod
  - All containers in a single Pod can share
- PersistentVolumes
  - Created at the cluster level, outlives a Pod
  - Separate storage config from Pod
  - Multiple Pods can share

Container Storage Interface (CSI) plugins are the better and new way to connect to storage

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

## Ingress

OSI Layer 7 (HTTP)

Route outside connections

## Helm

Higher deployment abstractions to extend Kubernetes API.

## Web GUI

https://github.com/kubernetes/dashboard

Many cloud vendors don't have GUI by default

## Future of Kubernetes

We need to make infrastructure boring as much as possible, so we rely on more the things we do on top of that.

**Knative** is serverless workloads on Kubernetes

**Service mesh** new layer in distributed app traffic for better control, security and monitoring

## Resource requirement

`1K` is `1 Kilobyte` = `1,000 bytes`

`1Ki` is `1 Kibibyte` = `1,024 bytes`

## Taint and tolerance

Taint is bug spray for node. Tolerance of bug property to overcome taint for pod.

Trait is for node, tolerance is for pod.

Trait and tolerance doesn't guarantee which node to place a pod.

What happens to a pod when the pod doesn't tolerate taint of a node
- NoSchedule
- PreferNoSchedule
- NoExecute

**Master node** has a taint by default to avoid running something

## Multi-container pod

- Sidecar
  - e.g. Logging agent alongside a web server to send logs to a central log server
- Adapter
  - e.g. A container exists between multiple containers and one central server
- Ambassador
  - e.g. A container for the application container to proxy to right one out of multiple servers

## Readiness probe

Check the application inside pod is ready, not if a pod or container is ready.

Readiness probe is useful,
- If your container needs to work on loading large data during startup.
- If you want your container to be able to take itself down for maintenance.
- If you want to start sending traffic to a pod only when a probe succeeds.

Even if pod or container can quickly get ready, the actual application inside may take longer time to complete all the 
setups.

Use `readinessProbe` in pod definition file and use `httpGet`, `tcpSocket`, `exec`

Use `initialDelaySeconds`, `periodSeconds`.

POD state
1. Pending
2. ContainerCreating
3. Running

POD conditions
- PodScheduled
- Initialized
- ContainersReady (when multiple containers pod)
- Ready (Pod is ready)

## Liveness probe

Livenss probe can be configured on containers to periodically test whether the application within the container is healthy.

If the test fails, the container is destroyed and recreated.

Developers need to define what it means for application to be healthy.

Check whether the container is healthy, not about being ready.

Use `livenessProbe` in `containers` in pod definition file.

## Monitoring

Heapster is deprecated

**Metrics Server** is now, in-memory solution, collects metrics from nodes and pods, doesn't store metrics in disks.

`kubectl top node` and `kubectl top pod` show list of CPU and memory

## Job

A batch task running inside a pod. When a job runs multple in sequence or in parallel, multiple pods will be created.

