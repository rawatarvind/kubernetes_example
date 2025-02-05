                        We are going to learn scheduling on kubernetes Cluster.

# how to to schedule the pods over nodes.

by default scheduler schedule the pods on nodes, 

but if we wants to deploy pod on perticular node or nodes.

**how could we do ?**

we can schedule pods in   different ways.

1. nodeName 
2. nodeSelector
3. Taint & toleration
4. Affinity & Anti-affinity

**nodeName**:- this is  the easiest way to schedule the pod on nodes.

**nodeSelector**:-  nodeSelector is used to schedule the pod over nodes on basis of selector labels.


**Taint & Toleration**:- taint is used in node while toleration is used in pod.

there are three type of taint & toleration.

a) **NoSchedule**:- can't be scheduled if they can't have toleration.

b) **PreferSchedule**:- can be scheduled if they can't have toleration.

c) **NoExecute** :- delete existing pods if they don't have toleration.


**Affinity & Anti-affinity**:- affinity is also used to schedule the pod over nodes it's alternative of nodeSelector with advance option like (lt,Exist,in, etc)

there two type of Affinity .

a) **Node Affinity** :- node affinity is used to schedule the pod over node

b) **Pod Affinity** :- pod affinity is used to co-locate the pod with pod  topologyKey.

**Anti-affinity**:- anti-affinity is used to voice-versa of pod-affinity.

how  to assign labels on nodes ?

 kubectl get node --show-labels

      kubectl label node node-name disk=ssd

 **How to taint a node ?**

      kubectl taint node node1 env=prod:NoSchedule

 and 

      kubectl taint node node2 disk=ssd:NoExecute

 and 

      kubectl taint node node3 env=test:PreferSchedule 

 **How to untaint the node ?**

     kubectl taint node node1 env=prod:NoSchedule-
     kubectl taint node node2 disk=ssd:PreferSchedule-
     kubectl taint node node3 env=test:NoExecute-

 **Node affinity weight**

we can specify a wight 1 and 100 for each instance of the preferredDuringSchedulingIgnoredDuringExecution affinity type.
If there are two possible nodes that match the preferredDuringSchedulingIgnoredDuringExecution rule, one with the label-1:key-1 label and another with the label-2:key-2 label, the scheduler considers the weight of each node and adds the weight to the other scores for that node, and schedules the Pod onto the node with the highest final score.

**requiredDuringSchedulingIgnoredDuringExecution**: the scheduler can't schedule the pod  unless the rule is met. this function like nodeSelector but  with more expressive syntax.

**referredDuringSchedulingIgnoredDuringExecution****: The Scheduler tries to find node that meets the rule. if a matching node is not available, the scheduler still 
schedules the Pod.


**Pod affinity example**

you could use requiredDuringSchedulingIgnoredDuringExecution affinity to tell the scheduler to co-locate Pods of two services in the same cloud provider zone because they communicate with each other a lot. Similarly, you could use preferredDuringSchedulingIgnoredDuringExecution anti-affinity to spread Pods from a service across multiple cloud provider zones.


Note:
Pod anti-affinity requires nodes to be consistently labeled, in other words, every node in the cluster must have an appropriate label matching topologyKey. If some or all nodes are missing the specified topologyKey label, it can lead to unintended behavior.

You express the topology domain (X) using a topologyKey, which is the key for the node label that the system uses to denote the domain.




