# Node Selectors
<hr/><br/><br/>


## Label Nodes
```bash
kubectl label nodes node-1 size=Large	
kubectl get node node01 --show-labels
```

## Add node selector to pod definition
```yaml
apiVersion: v1
kind: Pod
...
spec:
  nodeSelector: # need to label the node
    size: Large
```	
