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

## Node affinity

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

## CLI

`kubectl run <pod-name> --image=<image=name> --dry-run=client -o yaml > <file-name>.yaml`

`kubectl api-resources` lists short names

`kubectl describe <object-type> <object-name>`

`kubectl get pods -o wide` shows node

`--force` in `kubect delete pod <pod-name> --force` fastens the process

`kubectl create -f <file-name.yaml>` to newly create object

`kubectl apply -f <file-name.yaml>`

To scale ReplicaSet, `kubectl scale replicaset <replicaset-name> --replicas=<integer>`

Use `--all-namespaces` or `-A` if objects spans across different namespaces.

Use `--labels="key=value"` or `-l="key=value"` to set a label

### Linux

`cat <file-name>` to see the text contents of the file

`watch <kubectl-command>` to monitor real-time

`echo -n '<secret-value>' | base64` to encode secrets and `echo -n '<base64-encoded-secret-value>' | base64 --decode` to decode

`alias` checks the existing aliases. `alias k='kubectl'`

`whoami` sees what user you are logged into. `kubectl exec <pod-name> -- whoami` sees the user in the pod

To use service account token in REST API call, `curl https://<endpoint> -insecure --header "Authorization: Bearer <token>`

### Debugging

`kubectl describe pod <pod-name>`

`kubectl explain <object-type>` e.g. `kubectl explain replicaset` shortens `kubectl`

`kubectl logs <pod-name>` inspects logs

`kubectl exec -it <pod-name> -- <linux-command-for-debugging>`

### Update

`kubectl edit rs <replicaset-name>` won't replace old pods with new pods, so we need to delete old pods manually

`kubectl replace --force -f <definition-file.yaml>`

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
