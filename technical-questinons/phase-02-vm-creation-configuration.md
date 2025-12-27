## 1: Why cloud-init over manual installation for production K8s deployments?

```
Cloud-init is NOT like systemd/init. It's a one-time provisioning tool that runs on first boot only, not on every reboot.
Cloud-init solves the "golden image + variable configuration" problem:

Production Use Case:
├─ You need 100 identical K8s worker nodes
├─ Same OS, same packages, same security hardening
└─ BUT: Different hostnames, IPs, SSH keys, node labels

Without Cloud-Init (Manual):
├─ Install Ubuntu 100 times (hours of clicking)
└─ High risk of configuration drift (typos, missed steps)

With Cloud-Init:
├─ One Ubuntu cloud image (immutable, tested)
├─ 100 different cloud-init configs (YAML files)
└─ Provision 100 VMs in parallel (minutes, not hours)

Why Production Teams Use It:
1. Speed: Spin up VMs in seconds vs. minutes
2. Consistency: Same base image = no "works on my machine"
3. Automation: Integrates with Terraform, Ansible, Proxmox API
4. Declarative: Config is YAML (version controlled, peer reviewed)
```

## 2: Should control plane have less RAM than workers? Why/why not?

```
Control Plane Memory Consumers (Per Component):

1. etcd (Distributed Database)
   ├─ Stores entire cluster state (every pod, service, secret)
   ├─ Minimum: 2GB, Production: 8-16GB+
   └─ Why? Keeps full dataset in RAM for sub-ms reads

2. kube-apiserver (API Gateway)
   ├─ Handles ALL cluster API requests (kubectl, controllers, pods)
   ├─ Minimum: 1GB, High-traffic: 4-8GB
   └─ Why? Caches resources, handles concurrent connections

3. kube-controller-manager (Reconciliation Engine)
   ├─ Runs 30+ controllers (ReplicaSet, Deployment, Node, etc.)
   ├─ Minimum: 512MB, Large clusters: 2-4GB
   └─ Why? Watches all resources, maintains desired state

4. kube-scheduler (Pod Placement)
   ├─ Scores every node for every pod scheduling request
   ├─ Minimum: 512MB, 1000+ nodes: 2-4GB
   └─ Why? In-memory node/pod topology cache

Total Control Plane: ~4GB minimum, 16-32GB production
```
### Your Architecture Re-Evaluated:
Your Plan:
```
├─ Control Plane: 8GB RAM  ✅ GOOD for homelab (allows growth)
└─ Workers: 10GB RAM       ✅ GOOD for app workloads
```
```
Production Pattern (3-node cluster, 100 worker nodes):
├─ Control Plane: 16GB RAM (handles 1000s of API requests/sec)
└─ Workers: 64-128GB RAM   (runs hundreds of pods)
```
- In small clusters (like yours), control plane often needs equal or more RAM than workers.
- In large clusters, workers scale horizontally (more nodes), while control plane scales vertically (bigger nodes).

## 3: What happens if your control plane IP changes after cluster initialization?

```
When Control Plane IP Changes After kubeadm init:

IMMEDIATE FAILURES:
├─ ❌ kubectl commands fail (can't reach API server)
├─ ❌ kubelet on workers can't join cluster (wrong IP in bootstrap config)
├─ ❌ Pods can't call Kubernetes API (service endpoints stale)
└─ ❌ etcd cluster breaks if multi-master (peer URLs hardcoded)

WHY IT BREAKS:
1. kubeadm init writes control plane IP to:
   ├─ /etc/kubernetes/admin.conf (kubectl config)
   ├─ /etc/kubernetes/kubelet.conf (node → API communication)
   ├─ /etc/kubernetes/manifests/kube-apiserver.yaml (--advertise-address)
   └─ PKI certificates (API server cert has IP in SAN field)

2. Worker nodes have hardcoded control plane IP in:
   ├─ /etc/kubernetes/bootstrap-kubeconfig.conf
   └─ /etc/kubernetes/kubelet.conf

3. Kubernetes Service (ClusterIP for API):
   ├─ Points to control plane endpoint
   └─ If IP changes, Service can't route traffic

RESULT: Total cluster failure. Not just SSH issues—ZERO pod communication.
```
How to Fix (Production Disaster Recovery Scenario):

```
Option 1: Use DNS name instead of IP (BEST PRACTICE)
kubeadm init --control-plane-endpoint "k8s-cp.example.com:6443"
# Now change DNS A record, restart components, cluster survives

# Option 2: Regenerate certificates + reconfigure (PAINFUL)
kubeadm init phase certs apiserver --apiserver-advertise-address NEW_IP
kubeadm init phase kubeconfig all
systemctl restart kubelet
# Then update all worker nodes manually.

# Option 3: Rebuild cluster from backup (NUCLEAR OPTION)
```
