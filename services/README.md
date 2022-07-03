# Services
<hr/><br/><br/>

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