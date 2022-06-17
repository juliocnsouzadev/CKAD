# CKAD
## CKAD - CERTIFIED KUBERNETES APPLICATION DEVELOPER
<hr/><br/><br/>

# Run commands inside pod:
```bash
kubectl exec -it podname -- command
```
```bash
kubectl exec -it simple-webapp-1 -- sh ./root/curl-test.sh
```

# Configuration
## Commands and arguments

Given the Dockefile bellow:
```Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

Can run like this:
```bash
docker run ubuntu-sleeper #runs with default 5 seconds 
docker run ubuntu-sleeper 10 #runs with explicit 10 seconds 
```

Translating to a Kubernetes pod definition yaml:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
    containers:
     - name: ubuntu-sleeper
       image: ubuntu:latest
       command: ["sleep"] #command is docker ENTRYPOINT
       args: ["10"] #args is docker CMD
```
or
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
    containers:
     - name: ubuntu-sleeper
       image: ubuntu:latest
       command: 
        - sleep
        - "10"
```

# Config Maps

## Imperative approach
```bash
kubectl create configmap my-config-map \
    --from-literal=SOME_KEY=some-value
```

## Yaml approach
<i>my-cofig-map.yaml</i>
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config-map
data:
    SOME_KEY: some-value
```
```bash
kubectl create configmap my-config-map --from-file=my-cofig-map.yaml
kubectl get configmaps
kubectl describe configmaps
```
<i>my-pod-definition.yaml</i>
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
    containers:
    - name: my-app
        image: my-app:latest
        envFrom:
        - configMapRef:
            name: my-config-map
```

# Secrets

## Imperative approach
```bash
kubectl create secret generic my-secrets \
    --from-literal=SOME_KEY=some-value \
    --from-literal=ANOTHER_KEY=another-value
```

## Declarative approach
<i>my-secrets.yaml</i>
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secrets
data:
    SOME_KEY: some-value #base64 encoded >> echo -n some-value | base64
```
```bash
kubectl create -f my-secrets.yaml
kubectl get secrets
kubectl describe secrets
```
<i>my-pod-definition.yaml</i>
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
    containers:
    - name: my-app
        image: my-app:latest
        envFrom:
        - configMapRef:
            name: my-config-map
        - secretRef: 
            name: my-secrets
```

# Security Context

## Pod Level Security Context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
    securityContext:
      runAsUser: 1000
    containers:
    - name: my-app
        image: my-app:latest
        envFrom:
        - configMapRef:
            name: my-config-map
        - secretRef: 
            name: my-secrets
```

## Container Level Security Context
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
    containers:
    - name: my-app
        image: my-app:latest
        securityContext:
            runAsUser: 1000
            privileged: true #allowed only on container level
            capabilities: #allowed only on container level
                add: ["NET_ADMIN"]
        envFrom:
        - configMapRef:
            name: my-config-map
        - secretRef: 
            name: my-secrets
```
# Resources Limits
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: rabbit
  namespace: default
spec:
  containers:
  - args:
    - sleep
    - "1000"
    image: ubuntu
    imagePullPolicy: Always
    name: cpu-stress
    resources: # resources section is only allowed on container level
      limits:
        cpu: "2" # cpu limit in millicores starting from 0.1
        memory: "2Gi" # memory limit in bytes starting from 1Mi
        disk: "2Gi" # disk limit in bytes starting from 1Mi
      requests:
        cpu: "1"	
        memory: "1Gi"
        disk: "1Gi"
```	
# Service Account
```bash
kubectl create serviceaccount my-service-account
kubectl describe serviceaccount my-service-account
kubectl get secret
kubectl describe secret # get the token
```
Examples of deployment definition and pod definition using "my-service-account":
- [deployment.yaml](./service-accounts/deployment.yaml)
- [pod.yaml](./service-accounts/pod.yaml)

# Taints and Tolerations
- taints are placed in the node
- tolerations are placed in the pod

```bash
kubectl taint nodes my-node taint-key=taint-value:taint-effect
```
<b>Taint effects:</b>
- NoSchedule
- PreferNoSchedule
- NoExecute

```bash
kubectl taint nodes application-node app=blue:NoSchedule
```

pod-definition.yaml
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
  tolerations:
  - key: "app" # needs to be between quotes
    value: "my-app" # needs to be between quotes
    effect: "NoSchedule" # needs to be between quotes
```

see taints:
```bash	
kubectl describe nodes node-name | grep Taint # any node
kubectl describe nodes kubemaster | grep Taint # kubemaster node -> has taint to prevent any pod from being scheduled on it
```
rmeove taint
```bash
kubectl taint nodes application-node app=blue:NoSchedule-
```

# Node Selectors

## Label Nodes
```bash
kubectl label nodes node-1 size=Large	
kubectl get node node01 --show-labels
```

## Add node selector to pod definition
```yaml
apiVersion: v1
kind: Pod
...
spec:
  nodeSelector: # need to label the node
    size: Large
```	

# Node Affinity

## Use operators to match the node:
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: size
          operator: In # In is used to match the value of the node selector 
          values:
          - Large
          - Medium
```	

## Same as above
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: size
          operator: NotIn # NotIn is used to match the value of the node selector 
          values:
          - Small
```	
** check documentation for more info about operators

## Node Affinity Types

### Required -> Pod will be scheduled on a node that matches the required node selector, if not it will be rejected
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
...
```	
### Preferred -> Pod will be scheduled on a node that matches the preferred node selector, if not it will be placed on the first available node
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    preferedDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
...
```	

# Readiness Probe

## Test Using HttpGet
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
    readinessProbe:
      httpGet:
        path: /myapp/actuator/health/readiness
        port: 8080
      initialDelaySeconds: 5 # delay before the first probe
      periodSeconds: 5 # interval between the probes
      failureThreshold: 3 # number of consecutive failures before the pod is considered unhealthy
```

## Test Using Command
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
    readinessProbe:
      exec:
        command:
        - sh
        - -c
        - "echo 'hello world'"
```
## Test TCP Port
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
    readinessProbe:
      tcpSocket: # usualy used for database connections
        port: 3306 # port to check
```

# Liveness Probe

## Test Using HttpGet
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
    readinessProbe:
      httpGet:
        path: /myapp/actuator/health/readiness
        port: 8080
      initialDelaySeconds: 5 # delay before the first probe
      periodSeconds: 5 # interval between the probes
      failureThreshold: 3 # number of consecutive failures before the pod is considered unhealthy
    livenessProbe:
      httpGet:
        path: /myapp/actuator/health/liveness
        port: 8080
      initialDelaySeconds: 5 # delay before the first probe
      periodSeconds: 5 # interval between the probes
      failureThreshold: 3 # number of consecutive failures before the pod is considered unhealthy
```
# Logging
```bash
kubectl logs -f my-app-pod my-app-container
```	

# Pod Design

## Labels and Selectors && Annotations
Standard method to group things together.

Creating labels for pods definition
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
  labels:
    app: my-app
    env: production
    function: frontend
spec:
  containers:
...
```

Select pods from labels
```bash
kubectl get pods -l app=my-app # single label
kubectl get pods --selector env=prod,bu=finance,tier=frontend # many labels
```

Select on replicasets
```yaml
apiVersion: v1
kind: ReplicaSet
metadata:
  name: my-app-rs
  labels:
    app: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - my-app-container
       image: my-app:latest
...
```

Select on services
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-sv
  labels:
    app: my-app
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
...
```

## Annotations
Used to store additional information about a resource.
```yaml
apiVersion: v1
kind: ReplicaSet
metadata:
  name: my-app-rs
  labels:
    app: my-app
  annotations:
    builversion: "1.0.0"
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
...
```

# Rolling Updates && Rollbacks

Rollout command
```bash
kubectl rollout status deployment/my-app-deployment	
kubectl rollout history deployment/my-app-deployment	
```

# Jobs

## Job

job-definition.yaml
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: my-app-job
spec:
  completions: 6 # number of times the job will be run
  parallelism: 3 # number of pods that can run at the same time
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app:latest
      restartPolicy: Never
```

create the job
```bash	
kubectl create -f job-definition.yaml
kubectl get jobs
```	

## CronJob

cronjob-definition.yaml
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: my-app-cronjob
spec:
  schedule: "*/5 * * * *" # every 5 minutes
  jobTemplate:
    spec:
      completions: 6 # number of times the job will be run
      parallelism: 3 # number of pods that can run at the same time
      template:
        metadata:
          labels:
            app: my-app
        containers:
        - name: my-app-container
          image: my-app:latest
        restartPolicy: Never
```

create the job
```bash	
kubectl create -f cronjob-definition.yaml
kubectl get cronjobs
```	

# Services

## NodePort
```yaml	
apiVersion: v1
kind: Service
metadata:
  name: myapp-np-service
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
  selector:
    app: myapp
    type: frontend 
```

## LoadBalancer
```yaml	
apiVersion: v1
kind: Service
metadata:
  name: myapp-lb-service
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: myapp
    type: frontend 
```
## Cluter IP
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-ci-be-service
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: myapp
    type: backend 
```	

# Network Policies

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      name: internal
  policyTypes:
  - Egress
  - Ingress
  ingress:
    - {}
  egress:
  - to:
    - podSelector:
        matchLabels:
          name: mysql
    ports:
    - protocol: TCP
      port: 3306

  - to:
    - podSelector:
        matchLabels:
          name: payroll
    ports:
    - protocol: TCP
      port: 8080

  - ports:
    - port: 53
      protocol: UDP
    - port: 53
      protocol: TCP
```	 