# Role Based Access control (RBAC) 


RBAC is method of regulating access to computer or network resources based on the roles of individual user within your organization.


RBAC API declares four kind of kubenetes objects: Role, ClusterRole, Rolebinding, Clusterrolebinding.


**Role and ClusterRole** 


An RBAC Role or ClusterRole contains the rules that represent set of permissions.

A Role always set of permissions within a perticular namespace. When you create a role then you have to specify the namespace it belongs to it.

ClusterRole by contrast, it’s a non-namespaced resource, the resources have different name(Role and ClusterRole) because kubernetes object always has to be either namespaced or not namespaced; it can’t be both.

ClusterRole has several uses. You can use a ClusterRole to;

1.Define the permission on namespaced resources and be granted to access within individual namespace.
2.Define permission on namespaced resourced and be granted to access across the all namespaces.
3.Define permission on cluste-scoped resources.

If your want to define role within namespace then use Role,and if you want to define role cluster-wide then use Clusterrole.


Role example
Here's an example Role in the "default" namespace that can be used to grant read access to pods:

apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:  
namespace: default  
name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group  
resources: ["pods"]  
verbs: ["get", "watch", "list"]



ClusterRole example 
Clusterrole can be used to grant the same permission as Role.
Because Clusterroles are cluster-wide.

Here is an example of a ClusterRole that can be used to grant read access to secrets in any particular namespace, or across all namespaces


apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:  # "namespace" omitted since ClusterRoles are not namespaced  name: secret-reader
rules:
- apiGroups: [""]  #  # at the HTTP level, the name of the resource for accessing Secret  # objects is "secrets"  
resources: ["secrets"]  
verbs: ["get", "watch", "list"]

**RoleBinding and ClusterRoleBinding**

A Role binding grants the permission defines in a role a user or set of users.
It holds a list of subjects (user, groups or service accounts).

A RoleBinding grants the permission within a specific namespace whereas clusterrolebinding grants the access to  cluster-wide.



**RoleBinding examples**

Here is an example of a RoleBinding that grants the "pod-reader" Role to the user "jane" within the "default" namespace. This allows "jane" to read pods in the "default" namespace.


apiVersion: rbac.authorization.k8s.io/v1
## This role binding allows "jane" to read pods in the "default" namespace.
## You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:  
name: read-pods  
namespace: default
subjects:# You can specify more than one "subject"
- kind: User  
name: jane # "name" is case sensitive  
apiGroup: rbac.authorization.k8s.io
roleRef:  # "roleRef" specifies the binding to a Role / ClusterRole  kind: Role #this must be Role or ClusterRole  
name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to  
apiGroup: rbac.authorization.k8s.io



**ClusterRoleBinding example**

To grant permissions across a whole cluster, you can use a ClusterRoleBinding. The following ClusterRoleBinding allows any user in the group "manager" to read secrets in any namespace.


apiVersion: rbac.authorization.k8s.io/v1
## This cluster role binding allows anyone in the "manager" group to read secrets in any namespace.
kind: ClusterRoleBinding
metadata:  
name: read-secrets-global
subjects:
- kind: Group  
name: manager # Name is case sensitive  
apiGroup: rbac.authorization.k8s.io
roleRef:  
kind: ClusterRole  
name: secret-reader  
apiGroup: rbac.authorization.k8s.io




# **Command-line utilities**


kubectl create role

Creates a Role object defining permissions within a single namespace. Examples:

**1.**Create a Role named "pod-reader" that allows users to perform get, watch and list on pods:

Kubectl create role pod-reader  --verb=get, watch, list --resource=pods


**2.**Create a Role named "pod-reader" with resourceNames specified:

Kubectl create role pod-reaeder --verb=get --resource=pods  --resource-name=readablepod --resource-name=anotherpod

**3.**Create a Role named "foo" with apiGroups specified:
Kubectl create role foo --verb=list,watch,get --resource=replicasets.apps
**4.**Create a Role named "foo" with subresource permissions:
Kubectl create role foo --verb=list,watch,get --resource=pod,	pods/status

kubectl create clusterrole

1.Create a ClusterRole named "pod-reader" that allows user to perform get, watch and list on pods:

Kubectl create clusterrole pod-reader --verb=list,watch,get  --resource=pods
2.Create a ClusterRole named "pod-reader" with resourceNames specified:

Kubectl create clusterrole pod-reader --ver=list,watch,get --resource=pod --resource-name=readablepod --resourece-name=anotherpod

3.Create a ClusterRole named "foo" with apiGroups specified:
Kubectle create clusterrole foo --verb=list,watch,get --resouce=replicasets.apps

4.Create a ClusterRole named "foo" with subresource permissions:

Kubectle create clusterrole foo --verb=list,watch,get --resource=pods,pods/status 
5.Create a ClusterRole named "foo" with nonResourceURL specified:

Kubectl create clusterrole foo --verb=list --non-resource-url=/log/*

5.Create a ClusterRole named "monitoring" with an aggregationRule specified:
Kubectl create clusterrole monitoring --aggregation-rule="rbac.example.com/aggregate-to-monitoring=true"

kubectl create rolebinding

1.Within the namespace "acme", grant the permissions in the "admin" ClusterRole to a user named "bob"

Kubectl create rolebinding bob-admin-binding --clustertole=admin --user=bob --namespace=acme
2.Within the namespace "acme", grant the permissions in the "view" ClusterRole to the service account in the namespace "acme" named "myapp":
Kubectle create rolebinding myapp-view-binding --clusterrole=view --serviceaccount=acme:myapp --namespace=acme
3.Within the namespace "acme", grant the permissions in the "view" ClusterRole to a service account in the namespace "myappnamespace" named "myapp":
Kubectl create rolebinding myappnamespace-myapp-view-binding --clusterrole=view --serviceaccount=myappnamespace:myapp --namespace=acme


kubectl create clusterrolebinding 

Grants a ClusterRole across the entire cluster (all namespaces). Examples


1.Across the entire cluster, grant the permissions in the "cluster-admin" ClusterRole to a user named "root":


Kubectl create clusterolebinding root-cluster-admin-binding --clusterole=cluster-admin --user=root


2.Across the entire cluster, grant the permissions in the "view" ClusterRole to a service account named "myapp" in the namespace "acme":

Kubectl create clusterrolebinding view-clusterrole-binding --clusterrole=view --service-account=acme:myapp






Create or Generate key for user .


## openssl genrsa -out sarvind.key 2048

## openssl req -new -key sarvind.key 	-out sarvind.csr -sub “/CN=sarvind/O=dev/O=example.org”

## openssl x509 -req -in sarvind.csr -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -out sarvind.crt -days 730

O represent to groups.

## openssl x509 -CA ~/.minikube/ca.crt -CAkey ~/.minikube/ca.key -CAcreateserial -days 730 -in sarvind.csr -out sarvind.crt

## kubectl config set-credentials sarvind --client-certificate=sarvind.crt  cleint-key=sarvind.key

## kubectl config get-contexts

## kubectl config set-context sarvind-minikube --cluster=minikube --user=sarvind

## kubectl config get-contexts
## kubectl use-context minikube

## kubectl get pod 

**How to get Verbs in kubernetes of perticulare resource.**


## kubectl api-resources  -o wide  | grep pod 

## kubectl auth can-i list pods --namespace default --as sarvind

## kubectl auth can-i delete pods --namespace default -as sarvind

## kubectl config view --minify 



