# Kubectl Cheat Sheet for CKA Exam Preparation

**Complete Reference Guide for Kubernetes Administrators**

---

## Table of Contents

1. [Essential Kubectl Basics](#essential-kubectl-basics)
2. [Cluster Management](#cluster-management)
3. [Working with Pods](#working-with-pods)
4. [Deployments and ReplicaSets](#deployments-and-replicasets)
5. [Services and Networking](#services-and-networking)
6. [ConfigMaps and Secrets](#configmaps-and-secrets)
7. [Storage: PV and PVC](#storage-pv-and-pvc)
8. [Namespaces](#namespaces)
9. [RBAC and Security](#rbac-and-security)
10. [Troubleshooting and Debugging](#troubleshooting-and-debugging)
11. [Advanced Operations](#advanced-operations)
12. [Key Command Comparisons](#key-command-comparisons)
13. [Best Practices](#best-practices)
14. [CKA Exam Tips](#cka-exam-tips)

---

## Essential Kubectl Basics

### Configuration and Context

**Set kubeconfig file**
```bash
export KUBECONFIG=/path/to/kubeconfig
```
Use a specific configuration file for kubectl commands.

**View current context**
```bash
kubectl config current-context
```
Shows which cluster you're currently connected to.

**List all contexts**
```bash
kubectl config get-contexts
```
Displays all available contexts in your kubeconfig.

**Switch context**
```bash
kubectl config use-context <context-name>
```
Changes to a different cluster context.

**Set default namespace for context**
```bash
kubectl config set-context --current --namespace=<namespace>
```
Changes the default namespace for current context to avoid typing -n repeatedly.

**View cluster information**
```bash
kubectl cluster-info
```
Shows cluster endpoints and services.

**Check API versions**
```bash
kubectl api-versions
```
Lists all available API versions in the cluster.

**Check API resources**
```bash
kubectl api-resources
```
Shows all resource types available with their short names.

---

## Cluster Management

### Node Operations

**List all nodes**
```bash
kubectl get nodes
kubectl get nodes -o wide
```
Shows nodes in cluster with extended information including IPs and OS.

**Describe node**
```bash
kubectl describe node <node-name>
```
Shows detailed information about a node including capacity, conditions, and pods.

**Label a node**
```bash
kubectl label node <node-name> key=value
```
Adds or updates labels on nodes for pod scheduling.

**Remove label from node**
```bash
kubectl label node <node-name> key-
```
Removes a specific label from node.

**Taint a node**
```bash
kubectl taint nodes <node-name> key=value:NoSchedule
```
Prevents pods from scheduling on node unless they have matching toleration.

**Remove taint from node**
```bash
kubectl taint nodes <node-name> key:NoSchedule-
```
Removes specific taint from node.

**Cordon node (mark unschedulable)**
```bash
kubectl cordon <node-name>
```
Marks node as unschedulable, preventing new pods from being scheduled.

**Uncordon node**
```bash
kubectl uncordon <node-name>
```
Marks node as schedulable again.

**Drain node**
```bash
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
```
Safely evicts all pods from node for maintenance. Use before node shutdown.

**View node resource usage**
```bash
kubectl top node
kubectl top node <node-name>
```
Shows CPU and memory usage for nodes (requires metrics-server).

---

## Working with Pods

### Creating and Managing Pods

**Run a pod imperatively**
```bash
kubectl run nginx --image=nginx
kubectl run nginx --image=nginx --port=80
```
Creates a pod quickly from command line.

**Run pod with specific command**
```bash
kubectl run busybox --image=busybox --command -- sleep 3600
```
Creates pod with custom command override.

**Generate pod YAML without creating**
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
```
Generates pod manifest for later modification and application.

**Create pod from YAML**
```bash
kubectl apply -f pod.yaml
kubectl create -f pod.yaml
```
Creates pod from manifest file.

**List pods**
```bash
kubectl get pods
kubectl get pods -n <namespace>
kubectl get pods --all-namespaces
kubectl get pods -A
```
Shows pods in current namespace, specific namespace, or all namespaces.

**List pods with more details**
```bash
kubectl get pods -o wide
```
Shows additional information like node name and pod IP.

**Describe pod**
```bash
kubectl describe pod <pod-name>
```
Shows detailed information including events, conditions, and container states.

**View pod logs**
```bash
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> --previous
kubectl logs <pod-name> --follow
kubectl logs <pod-name> --tail=50
```
Views logs from pod containers. Use -c for multi-container pods, --previous for crashed containers, --follow for live streaming, --tail for last N lines.

**Execute command in pod**
```bash
kubectl exec <pod-name> -- <command>
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/sh
```
Runs commands in pod containers. Use -it for interactive shell.

**Delete pod**
```bash
kubectl delete pod <pod-name>
kubectl delete pod <pod-name> --force --grace-period=0
```
Deletes pod gracefully or forcefully if needed.

**Copy files to/from pod**
```bash
kubectl cp <pod-name>:/path/to/file /local/path
kubectl cp /local/file <pod-name>:/path/in/pod
```
Copies files between local system and pod.

**Port forward to pod**
```bash
kubectl port-forward <pod-name> 8080:80
```
Forwards local port 8080 to pod port 80 for testing.

**View pod resource usage**
```bash
kubectl top pod
kubectl top pod <pod-name>
```
Shows CPU and memory usage for pods.

**Edit pod**
```bash
kubectl edit pod <pod-name>
```
Opens pod spec in editor. Note: many fields are immutable after creation.

**Get pod YAML**
```bash
kubectl get pod <pod-name> -o yaml
kubectl get pod <pod-name> -o yaml > pod-backup.yaml
```
Exports pod definition to YAML format.

---

## Deployments and ReplicaSets

### Creating Deployments

**Create deployment imperatively**
```bash
kubectl create deployment nginx --image=nginx
kubectl create deployment nginx --image=nginx --replicas=3
```
Creates deployment with specified replica count.

**Generate deployment YAML**
```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml
```
Generates deployment manifest for customization.

**Create deployment from YAML**
```bash
kubectl apply -f deployment.yaml
```
Creates or updates deployment from manifest.

### Managing Deployments

**List deployments**
```bash
kubectl get deployments
kubectl get deploy
kubectl get deployments -o wide
```
Shows deployments with basic or extended information.

**Describe deployment**
```bash
kubectl describe deployment <deployment-name>
```
Shows detailed deployment information including replica status and events.

**Scale deployment**
```bash
kubectl scale deployment <deployment-name> --replicas=5
```
Changes number of replicas for deployment.

**Autoscale deployment**
```bash
kubectl autoscale deployment <deployment-name> --min=2 --max=10 --cpu-percent=80
```
Creates HorizontalPodAutoscaler for automatic scaling based on CPU usage.

**Update deployment image**
```bash
kubectl set image deployment/<deployment-name> <container-name>=<new-image>:<tag>
```
Updates container image in deployment, triggering rolling update.

**View rollout status**
```bash
kubectl rollout status deployment/<deployment-name>
```
Shows progress of deployment rollout.

**View rollout history**
```bash
kubectl rollout history deployment/<deployment-name>
kubectl rollout history deployment/<deployment-name> --revision=2
```
Shows deployment revision history and details of specific revision.

**Rollback deployment**
```bash
kubectl rollout undo deployment/<deployment-name>
kubectl rollout undo deployment/<deployment-name> --to-revision=2
```
Rolls back to previous revision or specific revision number.

**Pause deployment rollout**
```bash
kubectl rollout pause deployment/<deployment-name>
```
Pauses ongoing deployment rollout.

**Resume deployment rollout**
```bash
kubectl rollout resume deployment/<deployment-name>
```
Resumes paused deployment rollout.

**Edit deployment**
```bash
kubectl edit deployment <deployment-name>
```
Opens deployment spec in editor for modification.

**Delete deployment**
```bash
kubectl delete deployment <deployment-name>
```
Deletes deployment and all associated pods.

### Working with ReplicaSets

**List ReplicaSets**
```bash
kubectl get replicasets
kubectl get rs
```
Shows ReplicaSets managing pod replicas.

**Describe ReplicaSet**
```bash
kubectl describe rs <replicaset-name>
```
Shows detailed ReplicaSet information.

**Scale ReplicaSet**
```bash
kubectl scale rs <replicaset-name> --replicas=3
```
Manually scales ReplicaSet (not recommended, use deployments instead).

**Delete ReplicaSet**
```bash
kubectl delete rs <replicaset-name>
```
Deletes ReplicaSet and managed pods.

---

## Services and Networking

### Creating Services

**Expose pod as service**
```bash
kubectl expose pod <pod-name> --port=80 --target-port=8080 --name=<service-name>
```
Creates service for existing pod.

**Expose deployment as service**
```bash
kubectl expose deployment <deployment-name> --port=80 --target-port=8080 --type=NodePort
kubectl expose deployment <deployment-name> --port=80 --type=LoadBalancer
kubectl expose deployment <deployment-name> --port=80 --type=ClusterIP
```
Creates service for deployment with specified type (ClusterIP, NodePort, or LoadBalancer).

**Create service imperatively**
```bash
kubectl create service clusterip my-svc --tcp=80:8080
kubectl create service nodeport my-svc --tcp=80:8080 --node-port=30080
```
Creates service of specific type.

**Generate service YAML**
```bash
kubectl create service clusterip my-svc --tcp=80:8080 --dry-run=client -o yaml > service.yaml
```
Generates service manifest for customization.

**Create service from YAML**
```bash
kubectl apply -f service.yaml
```
Creates or updates service from manifest.

### Managing Services

**List services**
```bash
kubectl get services
kubectl get svc
kubectl get svc -o wide
```
Shows services with basic or extended information.

**Describe service**
```bash
kubectl describe svc <service-name>
```
Shows detailed service information including endpoints.

**Get service endpoints**
```bash
kubectl get endpoints <service-name>
kubectl get ep <service-name>
```
Shows backend pod IPs that service routes traffic to.

**Edit service**
```bash
kubectl edit svc <service-name>
```
Opens service spec in editor for modification.

**Delete service**
```bash
kubectl delete svc <service-name>
```
Deletes service.

### Ingress Resources

**List ingresses**
```bash
kubectl get ingress
kubectl get ing
```
Shows ingress resources for HTTP/HTTPS routing.

**Describe ingress**
```bash
kubectl describe ingress <ingress-name>
```
Shows detailed ingress information including rules and backend services.

**Create ingress from YAML**
```bash
kubectl apply -f ingress.yaml
```
Creates or updates ingress resource.

**Edit ingress**
```bash
kubectl edit ingress <ingress-name>
```
Opens ingress spec in editor.

**Delete ingress**
```bash
kubectl delete ingress <ingress-name>
```
Deletes ingress resource.

### Network Policies

**List network policies**
```bash
kubectl get networkpolicies
kubectl get netpol
```
Shows network policies controlling pod traffic.

**Describe network policy**
```bash
kubectl describe netpol <policy-name>
```
Shows detailed policy rules for ingress and egress traffic.

**Create network policy from YAML**
```bash
kubectl apply -f networkpolicy.yaml
```
Creates or updates network policy.

**Delete network policy**
```bash
kubectl delete netpol <policy-name>
```
Deletes network policy.

---

## ConfigMaps and Secrets

### ConfigMaps

**Create ConfigMap from literal values**
```bash
kubectl create configmap <name> --from-literal=key1=value1 --from-literal=key2=value2
```
Creates ConfigMap with key-value pairs.

**Create ConfigMap from file**
```bash
kubectl create configmap <name> --from-file=config.txt
kubectl create configmap <name> --from-file=key=config.txt
```
Creates ConfigMap from file contents. Optional key parameter names the data entry.

**Create ConfigMap from directory**
```bash
kubectl create configmap <name> --from-file=/path/to/dir/
```
Creates ConfigMap with data entry for each file in directory.

**Create ConfigMap from env file**
```bash
kubectl create configmap <name> --from-env-file=config.env
```
Creates ConfigMap from key=value pairs in env file.

**Generate ConfigMap YAML**
```bash
kubectl create configmap <name> --from-literal=key=value --dry-run=client -o yaml > cm.yaml
```
Generates ConfigMap manifest for customization.

**List ConfigMaps**
```bash
kubectl get configmaps
kubectl get cm
```
Shows ConfigMaps in namespace.

**Describe ConfigMap**
```bash
kubectl describe cm <name>
```
Shows ConfigMap data and metadata.

**View ConfigMap data**
```bash
kubectl get cm <name> -o yaml
```
Displays complete ConfigMap definition including data.

**Edit ConfigMap**
```bash
kubectl edit cm <name>
```
Opens ConfigMap in editor for modification.

**Delete ConfigMap**
```bash
kubectl delete cm <name>
```
Deletes ConfigMap.

### Secrets

**Create Secret from literal values**
```bash
kubectl create secret generic <name> --from-literal=username=admin --from-literal=password=secret123
```
Creates generic secret with key-value pairs.

**Create Secret from file**
```bash
kubectl create secret generic <name> --from-file=ssh-key=~/.ssh/id_rsa
```
Creates secret from file contents.

**Create Docker registry secret**
```bash
kubectl create secret docker-registry <name> \
  --docker-server=<registry> \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email>
```
Creates secret for pulling images from private registry.

**Create TLS secret**
```bash
kubectl create secret tls <name> --cert=path/to/tls.crt --key=path/to/tls.key
```
Creates TLS secret for ingress or pod TLS configuration.

**Generate Secret YAML**
```bash
kubectl create secret generic <name> --from-literal=key=value --dry-run=client -o yaml > secret.yaml
```
Generates secret manifest. Values are base64 encoded.

**List Secrets**
```bash
kubectl get secrets
```
Shows secrets in namespace.

**Describe Secret**
```bash
kubectl describe secret <name>
```
Shows secret metadata without revealing values.

**View Secret data**
```bash
kubectl get secret <name> -o yaml
kubectl get secret <name> -o jsonpath='{.data.password}' | base64 --decode
```
Displays secret definition. Use jsonpath and base64 decode to view actual values.

**Edit Secret**
```bash
kubectl edit secret <name>
```
Opens secret in editor. Remember values must be base64 encoded.

**Delete Secret**
```bash
kubectl delete secret <name>
```
Deletes secret.

---

## Storage: PV and PVC

### Persistent Volumes (PV)

**List Persistent Volumes**
```bash
kubectl get pv
kubectl get persistentvolumes
```
Shows cluster-wide persistent volumes with capacity and status.

**Describe Persistent Volume**
```bash
kubectl describe pv <pv-name>
```
Shows detailed PV information including claim binding and access modes.

**Create PV from YAML**
```bash
kubectl apply -f pv.yaml
```
Creates persistent volume from manifest.

**Delete Persistent Volume**
```bash
kubectl delete pv <pv-name>
```
Deletes persistent volume.

**View PV details**
```bash
kubectl get pv <pv-name> -o yaml
```
Displays complete PV definition.

### Persistent Volume Claims (PVC)

**List Persistent Volume Claims**
```bash
kubectl get pvc
kubectl get persistentvolumeclaims
kubectl get pvc -A
```
Shows PVCs in namespace or all namespaces.

**Describe Persistent Volume Claim**
```bash
kubectl describe pvc <pvc-name>
```
Shows detailed PVC information including bound volume and events.

**Create PVC from YAML**
```bash
kubectl apply -f pvc.yaml
```
Creates persistent volume claim from manifest.

**Delete Persistent Volume Claim**
```bash
kubectl delete pvc <pvc-name>
```
Deletes PVC and may delete associated PV depending on reclaim policy.

**View PVC details**
```bash
kubectl get pvc <pvc-name> -o yaml
```
Displays complete PVC definition.

### Storage Classes

**List Storage Classes**
```bash
kubectl get storageclass
kubectl get sc
```
Shows available storage classes for dynamic provisioning.

**Describe Storage Class**
```bash
kubectl describe sc <storage-class-name>
```
Shows storage class parameters and provisioner.

**Create Storage Class from YAML**
```bash
kubectl apply -f storageclass.yaml
```
Creates storage class from manifest.

**Set default Storage Class**
```bash
kubectl patch storageclass <name> -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
Marks storage class as default for PVCs without explicit class.

**Delete Storage Class**
```bash
kubectl delete sc <storage-class-name>
```
Deletes storage class.

---

## Namespaces

### Managing Namespaces

**List namespaces**
```bash
kubectl get namespaces
kubectl get ns
```
Shows all namespaces in cluster.

**Create namespace**
```bash
kubectl create namespace <namespace-name>
kubectl create ns <namespace-name>
```
Creates new namespace.

**Generate namespace YAML**
```bash
kubectl create namespace <name> --dry-run=client -o yaml > namespace.yaml
```
Generates namespace manifest.

**Describe namespace**
```bash
kubectl describe ns <namespace-name>
```
Shows namespace details including resource quotas and limit ranges.

**Delete namespace**
```bash
kubectl delete namespace <namespace-name>
```
Deletes namespace and all resources within it. Use with caution.

**Set default namespace for context**
```bash
kubectl config set-context --current --namespace=<namespace-name>
```
Changes default namespace for current context.

**List all resources in namespace**
```bash
kubectl get all -n <namespace-name>
```
Shows pods, services, deployments, and replicasets in namespace.

### Resource Quotas

**List resource quotas**
```bash
kubectl get resourcequota
kubectl get quota
```
Shows resource quotas limiting namespace resource usage.

**Describe resource quota**
```bash
kubectl describe quota <quota-name>
```
Shows quota limits and current usage.

**Create resource quota from YAML**
```bash
kubectl apply -f resourcequota.yaml
```
Creates resource quota for namespace.

**Delete resource quota**
```bash
kubectl delete quota <quota-name>
```
Deletes resource quota.

### Limit Ranges

**List limit ranges**
```bash
kubectl get limitrange
kubectl get limits
```
Shows limit ranges enforcing resource constraints.

**Describe limit range**
```bash
kubectl describe limits <limitrange-name>
```
Shows default limits, requests, and constraints.

**Create limit range from YAML**
```bash
kubectl apply -f limitrange.yaml
```
Creates limit range for namespace.

**Delete limit range**
```bash
kubectl delete limits <limitrange-name>
```
Deletes limit range.

---

## RBAC and Security

### Service Accounts

**List service accounts**
```bash
kubectl get serviceaccounts
kubectl get sa
```
Shows service accounts for pod authentication.

**Create service account**
```bash
kubectl create serviceaccount <name>
kubectl create sa <name>
```
Creates service account.

**Describe service account**
```bash
kubectl describe sa <name>
```
Shows service account details including secrets and tokens.

**Delete service account**
```bash
kubectl delete sa <name>
```
Deletes service account.

### Roles and RoleBindings

**List roles**
```bash
kubectl get roles
kubectl get roles -A
```
Shows namespace-scoped roles defining permissions.

**Create role**
```bash
kubectl create role <role-name> --verb=get,list --resource=pods
kubectl create role <role-name> --verb=get --resource=pods --resource-name=<pod-name>
```
Creates role with specific permissions.

**Describe role**
```bash
kubectl describe role <role-name>
```
Shows role permissions and rules.

**Delete role**
```bash
kubectl delete role <role-name>
```
Deletes role.

**List role bindings**
```bash
kubectl get rolebindings
kubectl get rolebindings -A
```
Shows role bindings associating roles with subjects.

**Create role binding**
```bash
kubectl create rolebinding <name> --role=<role-name> --user=<username>
kubectl create rolebinding <name> --role=<role-name> --serviceaccount=<namespace>:<sa-name>
```
Binds role to user or service account.

**Describe role binding**
```bash
kubectl describe rolebinding <name>
```
Shows role binding subjects and role reference.

**Delete role binding**
```bash
kubectl delete rolebinding <name>
```
Deletes role binding.

### ClusterRoles and ClusterRoleBindings

**List cluster roles**
```bash
kubectl get clusterroles
kubectl get clusterroles | grep -v system
```
Shows cluster-wide roles. Filter to hide system roles.

**Create cluster role**
```bash
kubectl create clusterrole <name> --verb=get,list --resource=nodes
```
Creates cluster-scoped role.

**Describe cluster role**
```bash
kubectl describe clusterrole <name>
```
Shows cluster role permissions.

**Delete cluster role**
```bash
kubectl delete clusterrole <name>
```
Deletes cluster role.

**List cluster role bindings**
```bash
kubectl get clusterrolebindings
```
Shows cluster role bindings.

**Create cluster role binding**
```bash
kubectl create clusterrolebinding <name> --clusterrole=<role-name> --user=<username>
kubectl create clusterrolebinding <name> --clusterrole=<role-name> --serviceaccount=<namespace>:<sa-name>
```
Binds cluster role to user or service account cluster-wide.

**Describe cluster role binding**
```bash
kubectl describe clusterrolebinding <name>
```
Shows cluster role binding details.

**Delete cluster role binding**
```bash
kubectl delete clusterrolebinding <name>
```
Deletes cluster role binding.

### Authorization Checks

**Check if you can perform action**
```bash
kubectl auth can-i create pods
kubectl auth can-i delete deployments --namespace=<namespace>
kubectl auth can-i '*' '*' --all-namespaces
```
Verifies if current user has permission for specific action.

**Check what user can do**
```bash
kubectl auth can-i --list
kubectl auth can-i --list --namespace=<namespace>
```
Lists all actions current user can perform.

**Impersonate user**
```bash
kubectl get pods --as=<username>
kubectl get pods --as=system:serviceaccount:<namespace>:<sa-name>
```
Runs command as different user or service account for testing RBAC.

### Security Contexts

**View pod security context**
```bash
kubectl get pod <pod-name> -o jsonpath='{.spec.securityContext}'
```
Shows security settings for pod.

**View container security context**
```bash
kubectl get pod <pod-name> -o jsonpath='{.spec.containers[0].securityContext}'
```
Shows security settings for specific container.

---

## Troubleshooting and Debugging

### Viewing Logs

**View pod logs**
```bash
kubectl logs <pod-name>
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> --all-containers=true
```
Views logs from pod or specific container.

**Stream logs in real-time**
```bash
kubectl logs <pod-name> -f
kubectl logs <pod-name> -c <container-name> --follow
```
Follows logs as they're generated.

**View previous container logs**
```bash
kubectl logs <pod-name> --previous
kubectl logs <pod-name> -c <container-name> -p
```
Shows logs from crashed or restarted container.

**View logs with timestamp**
```bash
kubectl logs <pod-name> --timestamps
```
Adds timestamp prefix to each log line.

**View last N lines of logs**
```bash
kubectl logs <pod-name> --tail=50
kubectl logs <pod-name> --tail=100 --follow
```
Shows most recent log lines.

**View logs from multiple pods**
```bash
kubectl logs -l app=nginx
kubectl logs -l app=nginx --all-containers=true
```
Shows logs from all pods matching label selector.

**View logs since time**
```bash
kubectl logs <pod-name> --since=1h
kubectl logs <pod-name> --since=10m
```
Shows logs from past duration (s=seconds, m=minutes, h=hours).

### Debugging with Exec

**Execute command in pod**
```bash
kubectl exec <pod-name> -- <command>
kubectl exec <pod-name> -- ls /app
kubectl exec <pod-name> -- env
```
Runs command in pod without interactive shell.

**Open interactive shell**
```bash
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec -it <pod-name> -- /bin/sh
```
Opens interactive shell session in pod.

**Execute in specific container**
```bash
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```
Opens shell in specific container of multi-container pod.

**Run network debugging commands**
```bash
kubectl exec <pod-name> -- curl http://service-name
kubectl exec <pod-name> -- nslookup service-name
kubectl exec <pod-name> -- ping 10.96.0.1
```
Tests network connectivity from within pod.

### Events and Status

**View events**
```bash
kubectl get events
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl get events --field-selector involvedObject.name=<pod-name>
kubectl get events --field-selector type=Warning
```
Shows cluster events for troubleshooting. Filter by object or type.

**Watch events in real-time**
```bash
kubectl get events --watch
kubectl get events -w
```
Monitors events as they occur.

**View events for specific namespace**
```bash
kubectl get events -n <namespace>
```
Shows events in specific namespace.

### Resource Inspection

**Describe resource**
```bash
kubectl describe pod <pod-name>
kubectl describe node <node-name>
kubectl describe service <service-name>
```
Shows detailed information including events and status.

**Get resource YAML**
```bash
kubectl get pod <pod-name> -o yaml
kubectl get deployment <deployment-name> -o yaml
```
Exports complete resource definition.

**Get resource JSON**
```bash
kubectl get pod <pod-name> -o json
```
Exports resource definition in JSON format.

**Extract specific fields with JSONPath**
```bash
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{.items[*].status.podIP}'
kubectl get pod <pod-name> -o jsonpath='{.status.phase}'
```
Extracts specific fields from resource JSON.

**Use custom columns**
```bash
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP
```
Displays resources with custom column format.

### Debugging Running Pods

**Check pod status**
```bash
kubectl get pod <pod-name>
kubectl get pod <pod-name> -o jsonpath='{.status.phase}'
```
Shows current pod phase (Pending, Running, Failed, etc.).

**Check container restart count**
```bash
kubectl get pod <pod-name> -o jsonpath='{.status.containerStatuses[0].restartCount}'
```
Shows how many times container has restarted.

**Check pod conditions**
```bash
kubectl get pod <pod-name> -o jsonpath='{.status.conditions[*].type}'
```
Shows pod readiness conditions.

**Check resource usage**
```bash
kubectl top pod <pod-name>
kubectl top pod <pod-name> --containers
```
Shows CPU and memory usage for pod and containers.

### Debug with Ephemeral Containers

**Add debug container to running pod**
```bash
kubectl debug <pod-name> -it --image=busybox --target=<container-name>
```
Adds ephemeral debug container sharing process namespace with target.

**Create copy of pod for debugging**
```bash
kubectl debug <pod-name> -it --copy-to=<new-pod-name> --container=<container-name> -- sh
```
Creates copy of pod with modified container for debugging.

**Debug node using pod**
```bash
kubectl debug node/<node-name> -it --image=busybox
```
Creates privileged pod on node for node-level debugging.

### Network Debugging

**Test DNS resolution**
```bash
kubectl run test-dns --image=busybox:1.28 --rm -it -- nslookup kubernetes.default
```
Creates temporary pod to test DNS resolution.

**Test service connectivity**
```bash
kubectl run test-curl --image=curlimages/curl --rm -it -- curl http://service-name
```
Tests HTTP connectivity to service.

**Check service endpoints**
```bash
kubectl get endpoints <service-name>
```
Verifies service has backend pod IPs.

**Port forward for local testing**
```bash
kubectl port-forward pod/<pod-name> 8080:80
kubectl port-forward service/<service-name> 8080:80
```
Forwards local port to pod or service for direct access.

---

## Advanced Operations

### Labels and Selectors

**Add label to resource**
```bash
kubectl label pod <pod-name> env=production
kubectl label pod <pod-name> app=nginx version=v1
```
Adds or updates labels on resource.

**Remove label from resource**
```bash
kubectl label pod <pod-name> env-
```
Removes label from resource.

**Show labels**
```bash
kubectl get pods --show-labels
kubectl get pods -L app,version
```
Displays all labels or specific label columns.

**Filter by label**
```bash
kubectl get pods -l app=nginx
kubectl get pods -l 'env in (production,staging)'
kubectl get pods -l app=nginx,version=v1
kubectl get pods -l 'app!=nginx'
```
Filters resources using label selectors.

### Annotations

**Add annotation to resource**
```bash
kubectl annotate pod <pod-name> description="My test pod"
```
Adds metadata annotation to resource.

**Remove annotation**
```bash
kubectl annotate pod <pod-name> description-
```
Removes annotation from resource.

**View annotations**
```bash
kubectl get pod <pod-name> -o jsonpath='{.metadata.annotations}'
```
Shows all annotations on resource.

### Patching Resources

**Patch resource with JSON**
```bash
kubectl patch pod <pod-name> -p '{"spec":{"containers":[{"name":"nginx","image":"nginx:1.21"}]}}'
```
Updates resource using JSON patch.

**Patch resource with YAML**
```bash
kubectl patch deployment <deployment-name> --type merge -p '
spec:
  replicas: 5
'
```
Updates resource using YAML patch.

**Strategic merge patch**
```bash
kubectl patch deployment <deployment-name> --type strategic --patch '{"spec":{"replicas":3}}'
```
Uses strategic merge patch (default for most resources).

**JSON patch (RFC 6902)**
```bash
kubectl patch pod <pod-name> --type json -p '[{"op":"replace","path":"/spec/containers/0/image","value":"nginx:1.21"}]'
```
Uses JSON patch operations (add, remove, replace).

### Working with Multiple Resources

**Get multiple resource types**
```bash
kubectl get pods,services,deployments
kubectl get all
```
Shows multiple resource types in single command.

**Delete multiple resources**
```bash
kubectl delete pod <pod1> <pod2> <pod3>
kubectl delete pods --all
kubectl delete all --all
```
Deletes multiple resources or all resources of type.

**Apply multiple manifests**
```bash
kubectl apply -f directory/
kubectl apply -f file1.yaml -f file2.yaml
kubectl apply -R -f directory/
```
Applies all YAML files in directory or multiple files. Use -R for recursive.

### Watching Resources

**Watch resource changes**
```bash
kubectl get pods --watch
kubectl get pods -w
```
Monitors resources for changes in real-time.

**Watch with custom columns**
```bash
kubectl get pods --watch -o custom-columns=NAME:.metadata.name,STATUS:.status.phase
```
Watches resources with custom output format.

### Output Formats

**Common output formats**
```bash
kubectl get pods -o wide
kubectl get pods -o yaml
kubectl get pods -o json
kubectl get pods -o name
```
Shows resources in different formats.

**JSONPath output**
```bash
kubectl get pods -o jsonpath='{.items[*].metadata.name}'
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.podIP}{"\n"}{end}'
```
Extracts specific data using JSONPath expressions.

**Custom columns**
```bash
kubectl get pods -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,NODE:.spec.nodeName,IP:.status.podIP
```
Creates custom table with selected fields.

### Jobs and CronJobs

**Create job**
```bash
kubectl create job test-job --image=busybox -- echo "Hello Kubernetes"
```
Creates one-time job.

**List jobs**
```bash
kubectl get jobs
```
Shows jobs and their completion status.

**Delete job**
```bash
kubectl delete job <job-name>
```
Deletes job and associated pods.

**Create CronJob**
```bash
kubectl create cronjob test-cronjob --image=busybox --schedule="*/5 * * * *" -- echo "Hello"
```
Creates scheduled job with cron syntax.

**List CronJobs**
```bash
kubectl get cronjobs
kubectl get cj
```
Shows scheduled jobs.

**Suspend CronJob**
```bash
kubectl patch cronjob <cronjob-name> -p '{"spec":{"suspend":true}}'
```
Pauses CronJob execution.

**Delete CronJob**
```bash
kubectl delete cronjob <cronjob-name>
```
Deletes CronJob.

### DaemonSets and StatefulSets

**List DaemonSets**
```bash
kubectl get daemonsets
kubectl get ds
```
Shows DaemonSets running on all nodes.

**Describe DaemonSet**
```bash
kubectl describe ds <daemonset-name>
```
Shows DaemonSet details and pod distribution.

**List StatefulSets**
```bash
kubectl get statefulsets
kubectl get sts
```
Shows StatefulSets with ordered pod deployment.

**Describe StatefulSet**
```bash
kubectl describe sts <statefulset-name>
```
Shows StatefulSet details and pod status.

**Scale StatefulSet**
```bash
kubectl scale sts <statefulset-name> --replicas=5
```
Changes number of StatefulSet replicas.

---

## Key Command Comparisons

### kubectl apply vs kubectl create

**kubectl create**
- Imperative command
- Creates resource only if it doesn't exist
- Fails if resource already exists
- Good for one-time resource creation
- Example: `kubectl create -f deployment.yaml`

**kubectl apply**
- Declarative command
- Creates resource if it doesn't exist
- Updates resource if it exists
- Maintains last-applied-configuration annotation
- Good for GitOps and continuous deployment
- Example: `kubectl apply -f deployment.yaml`

**Best practice**: Use `kubectl apply` for managing resources over time.

### kubectl delete vs kubectl drain

**kubectl delete pod**
- Immediately deletes pod
- No graceful eviction
- Pod is recreated by controller (deployment, replicaset)
- Use for removing specific pods
- Example: `kubectl delete pod nginx-abc123`

**kubectl drain node**
- Gracefully evicts all pods from node
- Marks node as unschedulable
- Respects PodDisruptionBudgets
- Use before node maintenance
- Example: `kubectl drain node-1 --ignore-daemonsets`

**Best practice**: Use `drain` for node maintenance, `delete` for removing stuck pods.

### kubectl expose vs creating Service YAML

**kubectl expose**
- Quick imperative command
- Creates service automatically
- Limited customization options
- Good for simple services
- Example: `kubectl expose deployment nginx --port=80 --type=NodePort`

**Service YAML**
- Complete control over service specification
- Can define advanced selectors and session affinity
- Reusable and version-controlled
- Required for complex services
- Example: `kubectl apply -f service.yaml`

**Best practice**: Use `expose` for quick testing, YAML for production services.

### kubectl logs vs kubectl exec

**kubectl logs**
- Views application logs
- Non-interactive
- Shows stdout/stderr output
- Cannot modify container state
- Example: `kubectl logs nginx-abc123`

**kubectl exec**
- Runs commands inside container
- Can be interactive with -it
- Full shell access
- Can modify container state
- Example: `kubectl exec -it nginx-abc123 -- /bin/bash`

**Best practice**: Use `logs` for viewing output, `exec` for active troubleshooting.

### kubectl get -o yaml vs kubectl describe

**kubectl get -o yaml**
- Shows complete resource definition
- Machine-readable format
- Includes all fields and status
- Good for backup and recreation
- Example: `kubectl get pod nginx-abc123 -o yaml`

**kubectl describe**
- Human-readable summary
- Shows related events
- Formatted for readability
- Good for quick diagnosis
- Example: `kubectl describe pod nginx-abc123`

**Best practice**: Use `describe` for troubleshooting, `get -o yaml` for exporting.

### Namespace-specific vs Cluster-wide commands

**Role/RoleBinding** (namespace-scoped)
- Permissions within single namespace
- Use for namespace-specific access control
- Example: `kubectl create role pod-reader --verb=get,list --resource=pods`

**ClusterRole/ClusterRoleBinding** (cluster-wide)
- Permissions across all namespaces
- Use for cluster-wide access or non-namespaced resources
- Example: `kubectl create clusterrole node-reader --verb=get,list --resource=nodes`

**Best practice**: Use Roles for namespace isolation, ClusterRoles for cluster admins.

---

## Best Practices

### General kubectl Usage

**Use kubectl aliases**
Set common aliases in your shell for efficiency:
```bash
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
alias kl='kubectl logs'
alias kex='kubectl exec -it'
```

**Set context and namespace**
Always set default namespace for your context to avoid typing -n repeatedly:
```bash
kubectl config set-context --current --namespace=my-namespace
```

**Use labels effectively**
Labels are critical for organizing and selecting resources:
- Use consistent naming conventions (app, env, version, component)
- Apply labels at creation time
- Use label selectors for bulk operations
- Example: `kubectl get pods -l app=nginx,env=production`

**Leverage dry-run for YAML generation**
Generate YAML manifests quickly without creating resources:
```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yaml
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml > deployment.yaml
```

**Use --all-namespaces carefully**
The -A flag shows resources across all namespaces but can be overwhelming:
```bash
kubectl get pods -A  # Shows all pods in cluster
kubectl get pods -A | grep my-app  # Filter output
```

### Resource Management

**Always specify resource requests and limits**
Define resources in pod specs for proper scheduling:
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

**Use declarative configuration**
Store YAML manifests in version control and use kubectl apply:
```bash
kubectl apply -f manifests/
```
This approach supports GitOps workflows and infrastructure as code.

**Test configurations before applying**
Use --dry-run=server to validate without creating resources:
```bash
kubectl apply -f deployment.yaml --dry-run=server
```

**Implement proper health checks**
Always define liveness and readiness probes:
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 30
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
```

### Security Best Practices

**Use RBAC for access control**
Implement least privilege principle:
- Create specific roles for each use case
- Use service accounts for pod authentication
- Regularly audit role bindings
- Avoid using cluster-admin unnecessarily

**Never put sensitive data in ConfigMaps**
Use Secrets for sensitive information:
```bash
kubectl create secret generic db-password --from-literal=password=secretpass
```
Consider external secret management solutions for production.

**Implement Network Policies**
Control pod-to-pod communication:
```bash
kubectl apply -f networkpolicy.yaml
```
Default deny all traffic, then explicitly allow required connections.

**Use Pod Security Standards**
Apply pod security policies or admission controllers to enforce security:
- No privileged containers
- No host network access
- Drop unnecessary capabilities
- Run as non-root user

### Troubleshooting Best Practices

**Check events first**
Events often reveal the root cause:
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl describe pod <pod-name>  # Events at bottom
```

**Use labels for log aggregation**
View logs from multiple pods:
```bash
kubectl logs -l app=nginx --all-containers=true
```

**Save resource definitions before modification**
Back up before making changes:
```bash
kubectl get deployment nginx -o yaml > nginx-backup.yaml
```

**Use port-forward for local testing**
Test services without exposing them:
```bash
kubectl port-forward service/nginx 8080:80
```

**Monitor resource usage**
Regularly check resource consumption:
```bash
kubectl top nodes
kubectl top pods -A
```

### Deployment Best Practices

**Use rolling updates**
Configure deployments for zero-downtime updates:
```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 1
    maxUnavailable: 0
```

**Implement proper rollback strategy**
Test rollbacks in staging:
```bash
kubectl rollout history deployment/nginx
kubectl rollout undo deployment/nginx --to-revision=2
```

**Use readiness gates**
Ensure pods are ready before receiving traffic:
```yaml
readinessGates:
  - conditionType: "www.example.com/feature-1"
```

**Tag images with specific versions**
Never use :latest in production:
```yaml
image: nginx:1.21.6  # Good - specific version
# image: nginx:latest  # Bad - unpredictable
```

### Namespace Management

**Organize resources by namespace**
Use namespaces for logical separation:
- development, staging, production environments
- Different teams or projects
- Isolate resources with resource quotas

**Apply resource quotas**
Prevent resource exhaustion:
```bash
kubectl apply -f resourcequota.yaml -n development
```

**Use limit ranges**
Set default resource limits:
```bash
kubectl apply -f limitrange.yaml -n development
```

### Documentation and Versioning

**Document your cluster configuration**
Maintain documentation for:
- Cluster architecture
- Namespace purposes
- Network policies
- RBAC configurations

**Version control all manifests**
Store YAML files in Git:
- Track changes over time
- Enable collaboration
- Support rollback to previous configurations

**Use meaningful names**
Name resources descriptively:
- nginx-web-deployment (not nginx-dep-1)
- postgres-database-service (not postgres-svc)
- prod-namespace (not ns1)

---

## CKA Exam Tips

### Exam Environment Preparation

**Set up aliases immediately**
First commands in exam:
```bash
alias k=kubectl
alias kg='kubectl get'
alias kd='kubectl describe'
alias kdel='kubectl delete'
export do="--dry-run=client -o yaml"
export now="--grace-period=0 --force"
```

**Configure vim for YAML**
Add to ~/.vimrc:
```
set tabstop=2
set expandtab
set shiftwidth=2
```

**Enable kubectl autocompletion**
```bash
source <(kubectl completion bash)
complete -F __start_kubectl k
```

**Set default namespace**
Change namespace for specific contexts:
```bash
kubectl config set-context --current --namespace=<namespace>
```

### Time Management Strategies

**Read all questions first**
Quickly scan all questions to identify:
- Easy questions (do first)
- Complex questions (allocate more time)
- Weighted questions (prioritize by points)

**Use --dry-run for speed**
Generate YAML quickly:
```bash
k run nginx --image=nginx $do > pod.yaml
k create deployment nginx --image=nginx --replicas=3 $do > deployment.yaml
k expose deployment nginx --port=80 --type=NodePort $do > service.yaml
```

**Know imperative commands**
Create resources quickly without YAML:
```bash
k create secret generic mysecret --from-literal=password=secret123
k create configmap myconfig --from-file=config.txt
k create sa myserviceaccount
```

**Don't waste time on stuck questions**
If spending >5 minutes without progress, flag question and move on.

### Common Exam Scenarios

**Pod troubleshooting pattern**
```bash
k get pod <name>  # Check status
k describe pod <name>  # Check events
k logs <name>  # Check logs
k logs <name> --previous  # If restarted
k get events --field-selector involvedObject.name=<name>
```

**Node troubleshooting pattern**
```bash
k get nodes  # Check status
k describe node <name>  # Check conditions
k top node <name>  # Check resources
ssh <node-ip>  # May need to SSH to node
systemctl status kubelet  # Check kubelet
journalctl -u kubelet  # Check kubelet logs
```

**Service troubleshooting pattern**
```bash
k get svc <name>  # Check service
k get endpoints <name>  # Check endpoints
k describe svc <name>  # Check selector
k get pods -l <selector>  # Verify pods match
k port-forward svc/<name> 8080:80  # Test connectivity
```

### Quick Reference Commands

**Pod creation**
```bash
k run nginx --image=nginx
k run busybox --image=busybox --rm -it -- sh
k run nginx --image=nginx --labels=app=web,env=prod
k run nginx --image=nginx --port=80 --expose
```

**Deployment management**
```bash
k create deploy nginx --image=nginx --replicas=3
k scale deploy nginx --replicas=5
k set image deploy nginx nginx=nginx:1.21
k rollout status deploy nginx
k rollout undo deploy nginx
```

**Service creation**
```bash
k expose deploy nginx --port=80 --type=NodePort
k expose pod nginx --port=80 --name=nginx-svc
k create svc clusterip mysvc --tcp=80:8080
```

**ConfigMap and Secret**
```bash
k create cm myconfig --from-literal=key=value
k create cm myconfig --from-file=config.txt
k create secret generic mysecret --from-literal=password=pass123
k create secret docker-registry regcred --docker-server=<server> --docker-username=<user> --docker-password=<pass>
```

### Exam-Specific Tips

**Understand the scoring**
- Typically 15-20 questions
- Questions weighted differently (2-13%)
- Passing score usually 66%
- Some questions may have multiple parts

**Know where documentation is**
You can use official Kubernetes documentation during exam:
- kubernetes.io/docs
- Learn to search efficiently
- Bookmark frequently used pages
- Practice finding information quickly

**Practice with time limits**
Time yourself during practice:
- Complete tasks in 5-10 minutes
- Full practice exams in 2 hours
- Build muscle memory for common commands

**Master JSONPath**
Extract specific data efficiently:
```bash
k get pods -o jsonpath='{.items[*].metadata.name}'
k get pods -o jsonpath='{.items[*].status.podIP}'
k get node <name> -o jsonpath='{.status.capacity.cpu}'
```

**Know cluster upgrade procedure**
Understand kubeadm upgrade process:
- Upgrade control plane first
- Upgrade worker nodes one at a time
- Drain nodes before upgrade
- Uncordon after upgrade

**Understand backup and restore**
Know how to:
- Backup etcd cluster
- Restore etcd from backup
- Save and apply resource configurations

### Common Gotchas

**Watch for namespace**
Always check which namespace question refers to:
```bash
k get pods -n <namespace>
k config set-context --current --namespace=<namespace>
```

**Check resource names carefully**
Typos waste time. Use tab completion:
```bash
k get pod ngi<TAB>  # Autocompletes to nginx-xxx
```

**Verify before submitting**
Always verify resource was created correctly:
```bash
k get pods  # Verify pod is running
k describe pod <name>  # Check for errors
```

**Clean up after imperative commands**
Delete test pods after use:
```bash
k run test --image=busybox --rm -it -- sh  # Automatically deleted
```

**Remember dry-run doesn't validate**
Server-side dry-run validates but doesn't execute:
```bash
k apply -f pod.yaml --dry-run=server
```

### Essential Keyboard Shortcuts

**Vim shortcuts for YAML editing**
- `dd` - Delete line
- `yy` - Copy line
- `p` - Paste after cursor
- `u` - Undo
- `Ctrl+r` - Redo
- `:%s/old/new/g` - Replace all occurrences
- `:set paste` - Paste mode for indentation

**Terminal shortcuts**
- `Ctrl+r` - Search command history
- `Ctrl+a` - Move to beginning of line
- `Ctrl+e` - Move to end of line
- `Ctrl+w` - Delete word backward
- `Ctrl+u` - Delete to beginning of line
- `Ctrl+l` - Clear screen

### Pre-Exam Checklist

**Week before exam**
- Complete multiple full practice exams
- Time yourself strictly
- Review missed questions
- Practice with official documentation only
- Simulate exam environment

**Day before exam**
- Review command aliases and shortcuts
- Practice common scenarios
- Get good rest
- Prepare workspace (quiet, stable internet)
- Check exam requirements and ID

**During exam**
- Read questions carefully
- Flag difficult questions
- Verify namespace context
- Double-check work before moving on
- Leave time for review at end

### Must-Know Topics for CKA

**Core concepts (19%)**
- Cluster architecture
- API primitives
- Services and networking
- Pods, deployments, replicasets

**Workloads & Scheduling (15%)**
- Deployments and rolling updates
- DaemonSets, StatefulSets, Jobs
- Resource limits and quotas
- Manual scheduling and node affinity

**Services & Networking (20%)**
- Services (ClusterIP, NodePort, LoadBalancer)
- Ingress controllers and rules
- Network policies
- DNS resolution

**Storage (10%)**
- Persistent Volumes and Claims
- Storage Classes
- Volume modes and access modes
- StatefulSet storage

**Troubleshooting (30%)**
- Application failures
- Control plane failures
- Worker node failures
- Networking issues

**Cluster Management (6%)**
- Cluster upgrades
- etcd backup and restore
- Node maintenance
- Certificate management

---

## Additional Resources

### Official Documentation
- Kubernetes.io Documentation: https://kubernetes.io/docs/
- Kubectl Reference: https://kubernetes.io/docs/reference/kubectl/
- API Reference: https://kubernetes.io/docs/reference/kubernetes-api/

### Practice Environments
- Killer.sh CKA Simulator (included with exam registration)
- Kubernetes Playground on Katacoda
- Local clusters: minikube, kind, k3s, k3d

### Study Resources
- CNCF CKA Curriculum: https://github.com/cncf/curriculum
- Kubernetes The Hard Way (Kelsey Hightower)
- CKA exam tips and tricks on Medium and Dev.to
- KodeKloud CKA Course and Labs

### Tools to Master
- kubectl with autocompletion
- vim or nano for YAML editing
- tmux for terminal multiplexing (allowed in exam)
- grep, awk, sed for text processing
- systemctl and journalctl for node debugging

### Community Resources
- Kubernetes Slack: https://kubernetes.slack.com
- r/kubernetes on Reddit
- Stack Overflow kubernetes tag
- CNCF YouTube channel

---

**Good luck with your CKA exam preparation! Practice regularly, understand concepts deeply, and build muscle memory for common commands. Remember, the exam tests practical skills, so hands-on practice is essential.**

---

*Last Updated: December 2025*
*Kubernetes Version: 1.31+*
*Document Version: 1.0*
