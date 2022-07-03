# Resources Limits
<hr/><br/><br/>


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