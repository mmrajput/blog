## 1: Why kubeadm? - Do you know why we're using kubeadm instead of kubespray/k3s/microk8s for CKA prep?

**k3s/microk8s**: These ARE multi-node capable, but they're lightweight distributions with batteries-included features (simplified, opinionated)

**kubespray**: This is actually a production-grade Ansible installer for Kubernetes (used by enterprises)

**kubeadm**: This is the official Kubernetes installer - minimal, educational, standard

---

## 2: Bootstrap tokens - Do you understand what happens during kubeadm init and why workers can join with a token?

```
kubeadm init
├── 1. Generates CA certificates (cluster trust foundation)
├── 2. Creates static pod manifests for control plane
│   ├── kube-apiserver
│   ├── kube-controller-manager
│   ├── kube-scheduler
│   └── etcd
├── 3. Generates kubeconfig files (admin authentication)
├── 4. Creates bootstrap token (for workers to join)
└── 5. Installs essential addons (kube-proxy, CoreDNS)
```

**Why workers can join with a token:**
1. Control plane generates a **secret bootstrap token** (valid 24h by default)
2. Worker runs: `kubeadm join <control-plane-ip>:6443 --token <token>`
3. Worker uses token to authenticate to API server
4. API server validates token and issues **TLS client certificate** to worker
5. Worker kubelet registers node with cluster

**CKA exam scenario:** You'll troubleshoot expired tokens, re-generate them, debug join failures.

---
## 3: Static Pods - After installation, where will you find the control plane components (API server, scheduler, etc.)?

Static Pods location:
```
/etc/kubernetes/manifests/
├── kube-apiserver.yaml
├── kube-controller-manager.yaml
├── kube-scheduler.yaml
└── etcd.yaml
```
Why this matters for CKA:

- Static pods = Managed directly by kubelet, NOT by API server
- Kubelet watches /etc/kubernetes/manifests/ directory
- If you put a pod YAML in that directory, kubelet auto-starts it
- Delete the file → kubelet auto-stops the pod

### Common exam task:
```
"The control plane is broken. Fix it without using kubectl."
Solution: Edit YAML files in /etc/kubernetes/manifests/, kubelet will restart components

Enterprise parallel: This is how managed K8s (EKS/GKE/AKS) protects control plane - you can't accidentally delete API server because it's a static pod.
```