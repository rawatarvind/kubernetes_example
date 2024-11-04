# ReplicaSet   vs  Deployment 

Replica Set provides  high availability and scalability while Deployment provides high availability , scalability and Rollback & Rollout.


# ReplicaSet VS  ReplicaControl

**ReplicaControl**:- RC is only support equity base selector 
For Example:- env=production 
in this example env is key and production is value.

**ReplicaSet**:-  RS is only support set base selector
For example:- env=(production, qa)#
in this example env is key and value is production or qa 

Note:- Rolling-updating command is used for updating the replica controller. this replaces the specified replication controller with 
       a new replication controller by updating one pod at a time to use the new pod template.

       Rollout command is used for updating the replica set.
       even though replica set can be used independently it's best used along with deployments which makes them declarative.
