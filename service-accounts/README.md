# Service Account
<hr/><br/><br/>

```bash
kubectl create serviceaccount my-service-account
kubectl describe serviceaccount my-service-account
kubectl get secret
kubectl describe secret # get the token
```
Examples of deployment definition and pod definition using "my-service-account":
- [deployment.yaml](./deployment.yaml)
- [pod.yaml](./pod.yaml)

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