# State Persistence
<hr/><br/><br/>

## Useful commands
```bash
kubectl get persistentvolumes
kubetl get persistentvolumeclaims
```

## Volumes and Mounts

### Local Volume per Pod
```bash
kubeclt create -f pod_local_volume.yaml
```

### External Volume per Pod
```bash
kubeclt create -f pod_external_volume.yaml
```

## Persistent Volumes

1 - Create a Persistent Volume
```bash
kubeclt create -f persistent_volume.yaml
```
2 - Create a Persistent Volume Claim
```bash
kubeclt create -f persistent_volume_claim.yaml
```
3a - Create Pod Claiming Persistent Volume
```bash
kubeclt create -f pod_definition_pvc.yaml
```
3b - Create Deployment Claiming Persistent Volume
```bash
kubeclt create -f deployment_pvc.yaml
```