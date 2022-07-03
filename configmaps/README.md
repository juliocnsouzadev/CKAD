# Config Maps
<hr/><br/><br/>

## Imperative approach
```bash
kubectl create configmap my-config-map \
    --from-literal=SOME_KEY=some-value
```


## Yaml approach

```bash
kubectl create configmap my-config-map --from-file=my-cofig-map.yml
kubectl get configmaps
kubectl describe configmaps
kubectl create -f pod_definiton.yml
```

