# CKAD

## YAML

```yaml
apiVersion:
kind:
metadata:
  name: <object-name>
  labels:
    <key1>: <value1>
    <key2>: <value2>
  namespace: <namespace-name>
spec:
```

### Volume 

```yaml
spec:
  containers:
  - name: <container-name>
    volumeMounts:
      - mountPath: <path-in-container>
        name: <volume-name>
  volumes:
  - name: <volume-name>
    hostPath:
      path: <path-in-host-node>
      type:
```

### ReplicaSet

Label in selector and label in template metadata need to match

```yaml

spec:
  template:
    metadata:
      labels:
        <key1>: <value1>
  selector:
    matchLables:
      <key1>: <value1>
```

### Resource quota

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: <resource-quota-name>
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.cpu: "10"
    limits.memory: 10Gi
```

### Environment variable

```yaml
spec:
  containers:
    - name:
      env:
        - name:
          value:
```

```yaml
envFrom:
  - configMapKRef:
      name: <configmap-name>
```

```yaml
env:
  - name: <environment-variable-name>
    valueFrom:
      configMapKeyRef:
        name: <configmap-name>
        key:  <environment-variable-name>
```

Inject entire config as file in volume

```yaml
volumes:
  - name: <volume-name>
    configMap:
      name: <configmap-name>
```

```yaml
volumes:
  - name: <volume-name>
    secret:
      secretName: <configmap-name>
```

```yaml
name:
valueFrom:
  secretKeyRef:
    name: <secret-name>
    key: <secret-key>
```

```yaml
envFrom:
  - secretRef:
      name: <secret-name>
```

### Security context

```yaml
spec:
  securityContext:
    runAsUser: 1010 (for example)
```

Linux capabilities are container level

```yaml
spec:
  containers:
    - name: <container-name>
      securityContext:
        capabilities:
          add: ["SYS_TIME"] (for example)
```

### Service account

```yaml
spec:
  containers:
  serviceAccountName: <service-account-name>
```

### Resource requirement

Resource requests is minimum requested amount

```yaml
spec:
  containers:
    - name:
      image:
      resources:
        requests:
          cpu: 1
          memory: "1Gi"
        limits:
          cpu: 2
          memory: "2Gi"
```

### Toleration

We cannot specify toleration in `kubectl run` command line

All quoted strings

```yaml
spec:
  container:
  tolerations:
    - key: 
      operator: "Equal"
      value:
      effect:
```

### Node selector

But cannot apply advanced selections like OR or NOT

```yaml
kind: Pod

spec:
  containers:
  nodeSelector:
    <key-in-node>: <value-of-the-key>
```

### Node affinity

`kubectl edit deployment <deployment-name>`

https://kubernetes.io/docs/tasks/configure-pod-container/assign-pods-nodes-using-node-affinity/#schedule-a-pod-using-required-node-affinity

`operator: Exists` is also available.

```yaml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd     
```

### Readiness probe

```yaml
spec:
  containers:
    - name:
      image:
      readinessProbe:
        httpGet:
          path:
          port:
```

### Liveness probe

```yaml
spec:
  containers:
    - livenessProbe:
        httpGet:
          path:
          port:
        periodSeconds:
        initialDelaySeconds:
```

### Label

```yaml
metadata:
  labels:
    <key1>: <value1>
    <key2>: <value2>
```

ReplicaSet. `label-to-connect-replicaset-to-pod` and `label-configured-on-pod` need to match

```yaml
metadata:
  labels:
    <label-configured-on-replicaset>: <value>
spec:
  selector:
    matchLabels:
      <label-to-connect-replicaset-to-pod>: <value>
  template:
    metadata:
      labels:
        <label-configured-on-pod>: <value>
    spec:
```

### Annotation

Memo

```yaml
metadata:
  annotations:
    <key>: <value>
```

### Rolling update

Deployment YAML. Change rolling update to Recreate. `kubectl edit deployment <deployment-name>`

```yaml
spec:
  strategy:
    type: Recreate
```

### Restart policy

```yaml
spec:
  containers:
  restartPolicy: Never
```

### Job

`completions` runs a pod multiple times. 

`backoffLimit` allow us to run multiple times until it succeeds.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
spec:
  completions: 3
  parallelism: 3
  backoffLimit: 30
  template:
    spec:
      containers:
```

### Cron job

```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
```

### Service

`NodePort` is a service for external users to access a pod

`targetPort` is a port of a pod between pod and service. 

`port` is a port of a service between pod and service. 

`nodePort` is a port of a node between external users and service.

`selector` chooses a pod. Pod uses `metadata: labels:` for this.

```yaml
apiVersion: v1
kind: Service
metadata:
  name:
spec:
  type: NodePort
  ports:
    - targetPort:
      port:
      nodePort:
  selector:
    <key1-of-pod>: <value1>
```

```yaml
kind: Pod
metadata:
  <key1-of-pod>: <value1>
```

`ClusterIP` is a service for pods to communicate each other

### Persistent volume

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-log
spec:
  capacity:
    storage: 100Mi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /pv/log
```

### Persistent volume claim

To use persistent volume claim in a pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/var/www/html"
        name: mypd
  volumes:
    - name: mypd
      persistentVolumeClaim:
        claimName: myclaim
```

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: claim-log-1
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Mi
```

## CLI

`kubectl run <pod-name> --image=<image=name> --dry-run=client -o yaml > <file-name>.yaml`

`kubectl api-resources` lists short names

`kubectl describe <object-type> <object-name>`

`kubectl get pods -o wide` shows node

`kubectl get all` lists all objects.

`--force` in `kubect delete pod <pod-name> --force` fastens the process

`kubectl create -f <file-name.yaml>` to newly create object

`kubectl apply -f <file-name.yaml>`

To scale ReplicaSet, `kubectl scale replicaset <replicaset-name> --replicas=<integer>`

Use `--all-namespaces` or `-A` if objects spans across different namespaces.

Use `--labels="key=value"` or `-l="key=value"` to set a label

`kubectl get <resource-name> -A`, `-A` allows us to see objects in all the namespaces

To see the files in a pod, `kubectl exec <pod-name> -- cat /path/filename`

`kubectl config view` shows users

### Linux

`cat <file-name>` to see the text contents of the file

`watch <kubectl-command>` to monitor real-time

`echo -n '<secret-value>' | base64` to encode secrets and `echo -n '<base64-encoded-secret-value>' | base64 --decode` to decode

`alias` checks the existing aliases. `alias k='kubectl'`

`whoami` sees what user you are logged into. `kubectl exec <pod-name> -- whoami` sees the user in the pod

To use service account token in REST API call, `curl https://<endpoint> -insecure --header "Authorization: Bearer <token>`

`<kubectl-command-to-list-something> --no-headers | wc -l` to count the list items. `wc` is word count. `-l` prints the
number of lines in a file.

To check API version, `curl https://<master-node-address>:6443/version`

`cat /etc/kubernetes/manifests/kube-apiserver.yaml` shows kube API server.

### Debugging

`kubectl describe pod <pod-name>`

`kubectl explain <object-type>` e.g. `kubectl explain replicaset` shortens `kubectl`

`kubectl logs <pod-name>` inspects logs. `kubectl logs -f <pod-name>` shows logs in live by `-f`. If a pod is a multi-containers
pod, specify container name by `kubectl logs -f <pod-name> <container-name>`.

`kubectl exec -it <pod-name> -- <linux-command-for-debugging>`

### Resource usage

`kubectl top node` shows CPU and memory usage by node

`kubectl top pod` shows CPU and memory usage by pod

### Update

`kubectl edit rs <replicaset-name>` won't replace old pods with new pods, so we need to delete old pods manually

`kubectl replace --force -f <definition-file.yaml>`

### Delete resources

`kubectl delete -f <definition.yaml>`

### Pod

`kubectl run <pod-name> --image=<image-name> --port=<port-number> --expose=true` creates pod and service by exposing
on container port `<port-number>` (target port for a service) and create a service of type ClusterIP by the same name

To edit (update) a pod, use the following either approach
- `kubectl edit pod <pod-name>` and edit fails but YAML file is saved in `/tmp/` directory. Then `kubectl delete pod <pod-name>`
  and `kubectl create -f <yaml-file.yaml>`
- `kubectl get pod <pod-name> -o yaml > <file-name>.yaml`, `vi <file-name>.yaml`, then
  `kubectl delete pod <pod-name>` and `kubectl create -f <file-name>.yaml`
- `kubectl replace --force -f <file-name>.yaml` can save time

To pass command and arguments, use `kubectl <options-for-kubectl> -- <options-for-application>` or
`--command -- <command> <arg1> <arg2> ...`

### Deployment

`kubectl create deploy <deployment-name> --image=<image-name> --replicas=<integer>`

To edit (update) a deployment, unlike a pod, simply `kubectl edit deployment <deployment-name>`

### Namespace

To switch namespace, `kubectl config set-context --current --namespace=<namespace-name>` or `kubectl config set-context $(kubectl config current-context) --namespace=<namespace-name>`.
`kubectl config current-context` identifies the current context.

To get objects in all the namespaces, `kubectl get pods --all-namespaces`

### Service

Create a service named `redis-service` of type ClusterIP to expose pod `redis` on port 6379 
`kubectl expose pod redis --port=6379 --name redis-service`. This automatically set label in service from the label of the pod.

### Environment variable

`--from-literal=key1=value1 --from-literal=key2=value2`

### Secret

`kubectl create secret generic <secret-name> --from-literal=key=value`

Create TLS secret `kubectl create secret tls <secret-name> --cert "<cert-path>" --key "<key-path>"`

Don't forget `generic`

### Service account

`kubectl get sa` for short name

`kubectl create token <service-account-name>`

`kubectl exec -it <pod-name> ls /var/run/secrets/kubernetes.io/serviceaccount`

`kubectl exec -it <pod-name> cat /var/run/secrets/kubernetes.io/serviceaccount/token`

### Taint

`kubectl taint nodes <node-name> <key>=<value>:<taint-effect>`

`taint-effect`
- `NoSchedule`
- `PreferNoSchedule`
- `NoExecute`

To remove a taint, add `-` at the end. For example, `kubectl taint node controlplane node-role.kubernetes.io/control-plane:NoSchedule-`

### Label

`kubectl label nodes <node-name> <key>=<value>`

### Selector

Use label key and value in `metadata: labels: <key>: <value>`

`kubectl get pods --selector <label-key>=<label-value>`

With multiple selectors, separate by commas `kubectl get pods --selector <key1>=<value1>,<key2>=<value2>,<key3>=<value3>`

### Rollout

To update image `kubectl set image deployment <deployment-name> <container-name-in-the-deployment>=<new-image>`

`kubectl rollout status deployment <deployment-name>` shows status

`kubectl rollout history deployment <deployment-name>` show version history.

Rollback `kubectl rollout undo <deployment-name>`

### Ingress

`kubectl create ingress ingress-pay -n critical-space --rule="/pay=pay-service:8282"`

### Config

`kubectl config view` shows current configuration

To change current context, `kubectl config use-context <context-name> --kubeconfig <path-to-context-file>`

To make a new config file be the default config, `mv <path-to-new-config-file> </root/.kube/config>`

`kubectl config -h` lists

### Admission controller

`kubectl get pods -n kube-system`

`kubectl exec -it kube-apiserver-controlplane -n kube-system -- kube-apiserver -h | grep 'enable-admission-plugins'`

`vi /etc/kubernetes/minifests/kube-apiserver.yaml` or `grep enable-admission-plugins /etc/kubernetes/manifests/kube-apiserver.yaml`

### API version

To see preferred version of `authorization.k8s.io` API group, `kubectl proxy 8001&` and `curl localhost:8001/apis/authorization.k8s.io`

To enable API versions, `cp /etc/kubernetes/manifests/kube-apiserver.yaml /root/kube-apiserver.yaml` for backup, and modify the original file.

### Helm

`helm search hub <helm-chart-repo-name>` search helm chart repo

`helm repo add <name-in-my-resource> <chart-repo-url>` add a helm chart repo.

`helm search repo <package-name>` searches a package from the added repo

`helm repo list` lists the repos in the node

`helm install <release-name> <chart-name>` install a helm chart from a repo

`helm install <release-name> .` installs from the downloaded package if you are inside a package directory with `values.yaml`

`helm list` lists the installed package

`helm uninstall <release-name>`

`hell pull --untar <package-name>` downloads a package but not install

