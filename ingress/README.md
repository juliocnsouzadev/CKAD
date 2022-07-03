# Ingress
<hr/><br/><br/>

## Ingress Controller

Imperative way to create an ingress
```bash
# Format
kubectl create ingress <ingress-name> --rule="host/path=service:port"

#Example
kubectl create ingress ingress-test --rule="wear.my-online-store.com/wear*=wear-service:80"
```	

Declarative
```bash
kubectl create -f ingress.yml
```

Get all ingress
```bash
kubectl get ingress --all-namespace
```	
