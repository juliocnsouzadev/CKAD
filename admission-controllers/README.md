# Admssion Controllers

Functions of admission controller
- help us implement better security measures
- validate configuration
- perform additional operations before the pod gets created


## Enabling admission controllers

Edit /etc/kubernetes/manifests/kube-apiserver.yaml and add NamespaceAutoProvision admission controller to --enable-admission-plugins list

```bash
vim /etc/kubernetes/manifests/kube-apiserver.yaml
```

```yaml
NamespaceAutoProvapiVersion: v1
kind: Pod
metadata:
  annotations:
    kubeadm.kubernetes.io/kube-apiserver.advertise-address.endpoint: 10.53.40.9:6443
  creationTimestamp: null
  labels:
    component: kube-apiserver
    tier: control-plane
  name: kube-apiserver
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-apiserver
    - --advertise-address=10.53.40.9
    - --allow-privileged=true
    - --authorization-mode=Node,RBAC
    - --client-ca-file=/etc/kubernetes/pki/ca.crt
    - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvisionision
```

Note that the NamespaceExists and NamespaceAutoProvision admission controllers are deprecated and now replaced by NamespaceLifecycle admission controller.

The NamespaceLifecycle admission controller will make sure that requests to a non-existent namespace is rejected and that the default namespaces such as default, kube-system and kube-public cannot be deleted.