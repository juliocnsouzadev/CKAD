# Access
<hr/><br/><br/>

## Identifying Auth Modes
```bash
kubectl describe pod pod-name -n name-space | grep authorization-mode
```

## Checking Roles
```bash
kubectl get roles
```

## Account a Role is assinged to
```bash
kubectl describe rolebinding role-name
```

## Create a Role
```bash
kubectl create role developer --namespace=default --verb=list,create,delete --resource=pods
```

## Create a Role Binding
```bash
kubectl create rolebinding dev-user-binding --namespace=default --role=developer --user=dev-user
```

## Edit a Role Binding
```bash
kubectl edit role role-name -n name-space
```
