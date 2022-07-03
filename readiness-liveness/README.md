# Readiness and Liveness
<hr/><br/><br/>

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