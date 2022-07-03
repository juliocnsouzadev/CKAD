# Pod Design
<hr/><br/><br/>

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