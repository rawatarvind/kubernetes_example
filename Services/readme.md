# Services

services are used to exposing network application that are running one or more pods in cluster of kubernetes


there are four types of services.

**1:- ClusterIP**
**2:- Nodeport**
**3:- Load Balancer**
**4:- ExternalName**

**ClusterIP:**- Expose the service on ClusterIP, this makes service only reachable within the cluster.
if you don't tell the type of service it will take by default ClusterIP  service.
using ingress or Gateway, service can be accessed  to the  internet.


**NodePort:**- To expose service on each Node's IP at a  static,
to expose the application on each node ip addresses.

**Load Balancer:**- expose service exteranlly using external load balancer. kubenetes does not offer directly load balancer component.
you must provide one or you can integrate your kubernets cluster with   a cloud provider.

**ExternalName:-**  Map the service to the contents of the externalName field (for example, hostname api.foo.example.com)



