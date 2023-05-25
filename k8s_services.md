---
layout: page
title: "Services"
permalink: /k8s_services
nav_order: 7
---
# Tasks
## ClusterIP
### Create a Cluster IP
1. We can  create a Cluster IP using following yaml descriptor

```
apiVersion: v1
kind: Service
metadata:
 name: nginx-cluster-ip-service
spec:
 type: ClusterIP
 selector:
   app: nginx-single-container
 ports:
   - port: 80
     targetPort: 80
```

```
kubectl create -f clusterip-service.yml
```


1. The service can be describe using the **kubectl describe** command
```
kubectl describe svc nginx-cluster-ip-service
```   
![Kubernetes describe Cluster ip](assets/images/services/k8s_describe_clusterip_svc.png)

![Astuce icon](assets/images/astuce_icon.png) The endpoints listed here are IP of the pod that are selected by the clusterIP selector. Having no endpoints means that either workload resources (pod, deployment, replicasets) are not deployed or incorrect configuration regarding the selector.

3. We will launch an ephemeral pod to validate that the ClusterIP is reachable from the Kubernetes cluster
```
kubectl run busybox -it --tty --rm --restart=Never  --image=busybox:latest -- wget -S -O - nginx-cluster-ip-service
```
![Kubernetes connect Cluster ip](assets/images/services/k8s_connect_clusterip_svc.png)

## NodePort
1. We can also create service using the **kubectl expose** command 
```
kubectl expose pod nginx-single-container --type=NodePort --target-port=80 --port=80
```
- --type specifies the service type (NodePort/ClusterIP/LoadBalancer)
- --target-port refers to the port used to reach the service from the cluster
- --port  refers to the container port exposed by a pod or deployment

2. We can then get the service to have the port  on which the service will be reached
```
kubectl get svc nginx-single-container 
```
3. We finally get the public ip on which our services will be available
```
kubectl get nodes -o wide # Find Node IP addresses
```
![KubernetesNodePort Public IP](assets/images/services/k8s_nodeport_public_ip_svc.png)
# Display here a screenshot 

## Load Balancer
1. We create the Loadbalancer  using the **kubectl expose** command 
```
kubectl expose pod nginx-single-container --name=nginx-single-container-lb --type=LoadBalancer --target-port=80 --port=80
```
2. The LoadBalancer resource leads to the creation of a Cloud Load balancer.
we can use the following command to retrieve the public IP of the load balancer
```
kubectl get svc nginx-single-container-lb
```
![Load Balancer Public IP](assets/images/services/k8s_get_svc_lb.png)
<br/>

Else you can retrieve it the scaleway console.
![Load Balancer Public IP](assets/images/services/k8s_get_svc_lb_console.png)
# Image access services
# Reference documentation
[Kubernetes Services](https://kubernetes.io/docs/concepts/services-networking/)