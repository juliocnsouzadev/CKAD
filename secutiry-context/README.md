# Security Context
<hr/><br/><br/>

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