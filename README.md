# CKAD
## CKAD - CERTIFIED KUBERNETES APPLICATION DEVELOPER
<hr/><br/><br/>

## Run commands inside pod:
```bash
kubectl exec -it podname -- command
```
```bash
kubectl exec -it simple-webapp-1 -- sh ./root/curl-test.sh
```

## Logging
```bash
kubectl logs -f my-app-pod my-app-container
```	

## Configuration
### Commands and arguments

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

- ## [Config Maps](./configmaps/README.md)

- ## [Secrets](./secrets/README.md)

- ## [Security Context](./security-context/README.md)

- ## [Resources Limits](./resources-limits/README.md)

- ## [Service Account](./service-accounts/README.md)

- ## [Node Selectors](./node-selectors/README.md)

- ## [Node Affinity](./node-affinity/README.md)

- ## [Readiness and Liveness](./readiness-liveness/README.md)

- ## [Pod Design](./pod-design/README.md)

- ## [Rolling Updates && Rollbacks](./rolling-updates-rollbacks/README.md)

- ## [Jobs](./jobs/README.md)

- ## [Services](./services/README.md)

- ## [Network Policies](./network-policies/README.md)

- ## [Ingress](./ingress/README.md)

- ## [State Persistence](./state_persistence/README.md)
  
- ## [Kube Config](./kubeconfig/README.md)

- ## [Access](./access/README.md)

- ## [Cluster Roles](./cluster-roles/README.md)