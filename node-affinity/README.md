# Node Affinity
<hr/><br/><br/>

## Use operators to match the node:
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: size
          operator: In # In is used to match the value of the node selector 
          values:
          - Large
          - Medium
```	

## Same as above
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: size
          operator: NotIn # NotIn is used to match the value of the node selector 
          values:
          - Small
```	
** check documentation for more info about operators

## Node Affinity Types

### Required -> Pod will be scheduled on a node that matches the required node selector, if not it will be rejected
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
...
```	
### Preferred -> Pod will be scheduled on a node that matches the preferred node selector, if not it will be placed on the first available node
```yaml
apiVersion: v1
kind: Pod
...
affinity:
  nodeAffinity:
    preferedDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
...
```	