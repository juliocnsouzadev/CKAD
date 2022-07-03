# State Persistence
<hr/><br/><br/>

## Volumes and Mounts

Local volumes per pod
```yaml	
apiVersion: v1
kind: Pod
metadata:
  name: webapp
spec:
  containers:
  - name: event-simulator
    image: kodekloud/event-simulator
    env:
    - name: LOG_HANDLERS
      value: file
    volumeMounts:
    - mountPath: /log
      name: log-volume

  volumes:
  - name: log-volume
    hostPath:
      # directory location on host
      path: /var/log/webapp
      # this field is optional
      type: Directory
 ```

 Volumes external services - Shared by All Pods in all nodes
 ```yaml	
 apiVersion: v1
 kind: Pod
 metadata:
   name: my-app-pod
spec:
  containers:
  - name: my-app-container
    image: my-app:latest
    volumeMounts:
    - name: my-app-volume
      mountPath: /my-app-volume
      readOnly: true
  volumes:
  - name: my-app-volume
    awsElasticBlockStore:
      volumeID: vol-12345678
      fsType: ext4
 ```	

 ## Persistent Volumes

  ```yaml	
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-vol1
spec:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 1Gi
  awsElasticBlockStore:
    volumeID: vol-12345678
    fsType: ext4
```	

## Persistent Volume Claims

Define a PVC
 ```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-vol1
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  selector:
    accessModes: 
     - ReadWriteOnce
    resources:
      requests:
        storage: 500Mi
 ```	

 Use PVC in POD Definition
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

  Used in Deployment
  ```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-dashboard
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      name: web-dashboard
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        name: web-dashboard
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
