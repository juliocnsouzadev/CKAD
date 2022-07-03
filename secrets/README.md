# Secrets
<hr/><br/><br/>

## Imperative approach
```bash
kubectl create secret generic my-secrets \
    --from-literal=SOME_KEY=some-value \
    --from-literal=ANOTHER_KEY=another-value
```

## Declarative approach
```bash
kubectl create secret generic -f my-secrets.yaml
kubetclt create -f pod-definition.yaml
```