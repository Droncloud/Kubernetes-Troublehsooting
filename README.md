# Kubernetes-Troublehsooting

# 1. Control Panel Components 
This section consists of security recommendation for the direct configuration of Kubernetes control plane processes. These recommendations may not be directly applicable for cluster operators in environments where these components are managed by a 3rd party. 

# 1.1 Master Node Configuration Files

# 1.1.1 API server

- Ensure that the API server pod specification file permissions are set to 644 or more restrictive
- Ensure that the API server pod specification file ownership is set to root:root
- The API server pod specification file controls various parameters that set the behavior of the API server. 
- It validates and configures data for the api objects which include pods, services, replicationcontrollers, and others.
- You should restrict its file permissions to maintain the integrity of the file. The file should be writable by only the administrators on the system. 
  and the file should be owned by root:root. 

# Audit
Run the below command (based on the file location on your system) on the master node. and verify that the permissions are 644 or more restrictive.
```
stat -c %a /etc/kubernetes/manifests/kube-apiserver.yaml
```
Run the below command (based on the file location on your system) on the master node. and verify that the ownership is set to root:root.
```
stat -c %U:%G /etc/kubernetes/manifests/kube-apiserver.yaml
```

# Remediation
Run the below command (based on the file location on your system) on the master node.
```
chmod 644 /etc/kubernetes/manifests/kube-apiserver.yaml
```
Run the below command (based on the file location on your system) on the master node.
```
chown root:root /etc/kubernetes/manifests/kube-apiserver.yaml
```

# 1.1.2 Control manager

- Ensure that the controller manager pod specification file has permissions of 644 or more restrictive.
- Ensure that the controller manager pod specification file ownership is set to root:root.
- The controller manager pod specification file controls various parameters that set the behavior of various components of the master node. 
- It is a control loop that watches the shared state of the cluster through the apiserver and makes changes attempting to move the 
  current state towards the  desired state.
- You should restrict its file permissions to maintain the integrity of the file. The file should be writable by only the administrators on the system. 
  and the file should be owned by root:root. 
  
# Audit
Run the below command (based on the file location on your system) on the master node. and Verify that the permissions are 644 or more restrictive.
```
stat -c %a /etc/kubernetes/manifests/kube-controller-manager.yaml
```
Run the below command (based on the file location on your system) on the master node. and Verify that the ownership is set to root:root.
```
stat -c %U:%G /etc/kubernetes/manifests/kube-controller-manager.yaml
```

# Remediation
Run the below command (based on the file location on your system) on the master node.
```
chmod 644 /etc/kubernetes/manifests/kube-controller-manager.yaml
```
Run the below command (based on the file location on your system) on the master node.
```
chown root:root /etc/kubernetes/manifests/kube-controller-manager.yaml
```

# 1.1.3 Schedular

- Ensure that the scheduler pod specification file has permissions of 644 or more restrictive.
- Ensure that the scheduler pod specification file ownership is set to root:root.
- The Kubernetes scheduler is a control plane process which assigns Pods to Nodes. The scheduler determines which Nodes are valid placements for each Pod in 
  the scheduling queue according to constraints and available resources.
- The scheduler then ranks each valid Node and binds the Pod to a suitable Node.
- It controls various parameters that set the behavior of the kube-scheduler service in the master node.
- You should restrict its file permissions to maintain the integrity of the file. The file should be writable by only the administrators on the system. 
  and the file should be owned by root:root.
   
# Audit
Run the below command (based on the file location on your system) on the master node. and Verify that the permissions are 644 or more restrictive.
```
stat -c %a /etc/kubernetes/manifests/kube-scheduler.yaml
```
Run the below command (based on the file location on your system) on the master node. and Verify that the ownership is set to root:root.
```
stat -c %U:%G /etc/kubernetes/manifests/kube-scheduler.yaml
```

# Remediation
Run the below command (based on the file location on your system) on the master node.
```
chmod 644 /etc/kubernetes/manifests/kube-scheduler.yaml
```
Run the below command (based on the file location on your system) on the master node.
```
chown root:root /etc/kubernetes/manifests/kube-scheduler.yaml
```

# 1.1.4 ETCD

- The etcd pod specification file /etc/kubernetes/manifests/etcd.yaml controls various parameters that set the behavior of the etcd service in the master node. 
- etcd is a highly available key-value store which Kubernetes uses for persistent storage of all of its REST API object.
- You should restrict its file permissions to maintain the integrity of the file. 
- The file should be writable by only the administrators on the system.

# Audit
Run the below command (based on the file location on your system) on the master node. and Verify that the permissions are 644 or more restrictive.
```
stat -c %a /etc/kubernetes/manifests/etcd.yaml
```
Run the below command (based on the file location on your system) on the master node. and Verify that the ownership is set to root:root.
```
stat -c %U:%G /etc/kubernetes/manifests/etcd.yaml
```

# Remediation
Run the below command (based on the file location on your system) on the master node.
```
chmod 644 /etc/kubernetes/manifests/etcd.yaml
```
Run the below command (based on the file location on your system) on the master node.
```
chown root:root /etc/kubernetes/manifests/etcd.yaml
```

# 1.2 API Server

# This section contains recommendations relating to API server configuration flags.

# 1.2.1 --authorization-mode argument

- The API Server, can be configured to allow all requests. This mode should not be used on any production cluster.
- Do not always authorize all requests.
- The Node authorization mode only allows kubelets to read Secret, ConfigMap, PersistentVolume, and PersistentVolumeClaim objects associated with their nodes.
- In short you are Restricting kubelet nodes to reading only objects associated with them.
- By default, Node authorization is not enabled.
- Turn on Role Based Access Control.

**RBAC - Role-based access control (RBAC)**
- It is a method of regulating access to computer or network resources based on the roles of individual users within your organization.
- allows fine-grained control over the operations that different entities can perform on different objects in the cluster. It is recommended to use the 
  RBAC authorization mode.
 
 **Example of RBAC : ** https://kubernetes.io/docs/reference/access-authn-authz/rbac/

# Audit
- Run the following command on the master node and Verify that the --authorization-mode argument exists and is not set to AlwaysAllow. and argument exists and is set 
  to a value to include Node and it include RBAC.
```
ps -ef | grep kube-apiserver
``` 

# Remediation

- Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml on the master node and set the --authorization-mode parameter 
  to values other than AlwaysAllow. and set the --authorization-mode parameter to a value that includes Node and RBAC.
```
--authorization-mode=Node,RBAC
```
# Impact
- Only authorized requests will be served.
- When RBAC is enabled you will need to ensure that appropriate RBAC settings (including Roles, RoleBindings and ClusterRoleBindings) are configured to allow appropriate access.

# 1.2.2 --anonymous-auth argument

- When enabled, requests that are not rejected by other configured authentication methods are treated as anonymous requests. These requests are then served by the 
  API server. 
- You should rely on authentication to authorize access and disallow anonymous requests.

# Audit
- Run the following command on the master node and Verify that the --anonymous-auth argument is set to false.
```
ps -ef | grep kube-apiserver
``` 

# Remediation

- Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml on the master node and set the below parameter.
```
--anonymous-auth=false
```
# Impact
Anonymous requests will be rejected.

# 1.2.3 Admission control plugin

- admission plugins that should be enabled in addition to default enabled ones.
- Limit the Node and Pod objects that a kubelet could modify.
- Using the NodeRestriction plug-in ensures that the kubelet is restricted to the Node and Pod objects that it could modify as defined. Such kubelets will only 
  be allowed to modify their own Node API object, and only modify Pod API objects that are bound to their node.
  
# Audit

- Run the following command on the master node. and Verify that the --enable-admission-plugins argument is set to a value that includes NodeRestriction.
```
ps -ef | grep kube-apiserver
```
# Remediation

- Edit the API server pod specification file /etc/kubernetes/manifests/kube-apiserver.yaml on the master node and set the --enable-admission-plugins 
  parameter to a value that includes NodeRestriction.
 ``` 
--enable-admission-plugins= NodeRestriction
```
