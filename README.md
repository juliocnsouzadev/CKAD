# CKAD
## CKAD - CERTIFIED KUBERNETES APPLICATION DEVELOPER
<hr/><br/><br/>

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