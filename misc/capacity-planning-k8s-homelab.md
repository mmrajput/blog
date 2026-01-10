# Capacity Planning a Kubernetes Homelab: The RAM Surprise

## TL;DR
I planned 28GB RAM for my homelab. My server has 27GB total. Here's 
what I learned about capacity planning for Kubernetes clusters.

## The Setup
Building a production-grade Kubernetes homelab for CKA certification:
- Hardware: Beelink SER5 Pro (Ryzen 5 5500U)
- Goal: 1 control plane + 2 worker nodes
- Constraint: Limited budget, single physical server

## The First Plan (Naive)
[Include your original allocation table: 4 vCPU, 8GB RAM for CP, etc.]

I assumed:
- 32GB RAM advertised = 32GB usable
- Control plane needs less than workers
- 100% hardware utilization is fine for learning

## The Reality Check
[Paste lscpu/free output showing 27 GiB]

**Surprise #1**: Integrated GPU stole 5GB RAM
**Surprise #2**: Hypervisor needs headroom (not another "VM")
**Surprise #3**: Low-power CPU means less aggressive overcommitment

## The Revised Plan
[Final allocation table with 3 vCPU, 6/7GB RAM]

### Key Decisions
1. **Why 3 vCPU instead of 4?**
   - Ryzen 5500U is 15W TDP (not server-grade)
   - Context switching overhead on low-power chips
   - 75% utilization leaves room for burst workloads

2. **Why 6GB for control plane?**
   - etcd: 2GB (small cluster, <1000 pods)
   - kube-apiserver: 1.5GB (light API traffic)
   - Other components: 2.5GB
   - No need for 8GB until cluster scales to 100+ nodes

3. **Why 7GB for workers?**
   - Longhorn storage: 1GB per node
   - Observability stack: 1GB per node
   - Actual app workloads: 5GB per node

## Production Comparison
[Compare to AWS EKS t3.medium control planes, t3.xlarge workers]

My homelab is equivalent to a "small production cluster" for:
- Internal developer platform (< 50 devs)
- SaaS staging environment
- Side project with moderate traffic

It's NOT equivalent to:
- Netflix-scale production
- Machine learning training
- Big data processing

But for CKA exam? More than enough.

## Lessons for Platform Engineers
1. **Always measure before allocating**
   - Don't trust advertised specs (iGPU tax is real)
   - Hypervisors have overhead (20-30% is normal)

2. **Understand your hardware**
   - Low-power laptop CPUs ≠ server Xeons
   - Thermal throttling affects burst performance

3. **Plan for growth, not just today**
   - I left 26% RAM headroom for monitoring stack
   - Future: Add 3rd worker without hardware upgrade

4. **Document capacity decisions**
   - Write down your reasoning (portfolio material!)
   - Hiring managers love capacity planning skills

## CKA Exam Connection
Exam question I'm now ready for:
> "Given a node with 8GB RAM, can you schedule these pods?
>  - Pod A: 3GB request
>  - Pod B: 3GB request  
>  - Pod C: 3GB request"

Answer: No. 9GB requests > 8GB capacity.
(I learned this by almost overallocating my homelab!)

## What's Next?
Phase 3: Install Kubernetes (kubeadm, kubelet, kubectl)
Phase 4: Initialize cluster with DNS-based control plane endpoint

---

**Tools Used**: Proxmox VE, Ubuntu 24.04 Cloud Images, cloud-init
**Time Spent**: 3 hours (planning), 1 hour (execution)
**Mistakes Avoided**: Overallocation OOM disaster
```

---

## Phase 2 → Phase 3 Transition

You're now ready for Kubernetes installation. Here's what Phase 3 involves:
```
Phase 3: Install Kubernetes Prerequisites
├─ Update Ubuntu packages (security patches)
├─ Install container runtime (containerd, not Docker)
├─ Install Kubernetes components:
│   ├─ kubeadm (cluster bootstrap tool)
│   ├─ kubelet (node agent)
│   └─ kubectl (CLI tool)
├─ Configure kernel parameters (IP forwarding, bridge netfilter)
├─ Disable swap (Kubernetes requirement)
└─ Verify installation (version checks)

Time estimate: 1-2 hours
Automation: Ansible playbook (we'll build together)