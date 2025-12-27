- Exam clusters are small (1 control plane, 2 workers). If etcd runs out of memory, the entire cluster dies. Know how to check etcd memory usage: 
```
etcdctl endpoint status
```
