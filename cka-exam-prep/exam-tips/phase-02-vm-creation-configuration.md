- Exam clusters are small (1 control plane, 2 workers). If etcd runs out of memory, the entire cluster dies. Know how to check etcd memory usage: 
```
etcdctl endpoint status
```

- The control plane node was assigned a new IP by DHCP. Fix the cluster.
```
You'd need to:
1. Identify failed components (systemctl status kubelet, journalctl -u kubelet)
2. Update kubeconfigs with new IP
3. Regenerate API server certs
4. Restart kubelet/docker/containerd
5. Rejoin worker nodes
```
