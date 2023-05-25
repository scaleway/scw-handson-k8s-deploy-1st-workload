---
layout: page
title: "Namespaces"
permalink: /namespaces
nav_order: 8
---
# Materials
**Finished code for this lesson is available on the Instance Tool at the folder /home/ubuntu/06-namespaces/ .**
# Tasks
## Lifecycle Management
- Create a namespace
```
kubectl create ns ns1
```
- Get all namespaces
```
kubectl get ns
```
- Delete a namespace
```
kubectl delete ns ns1
```
## Resources Management
We will demonstrate here how we can create resources within namespaces. 
1. We start by creating two different namespaces that will be used later.
```
kubectl create ns ns1
kubectl create ns ns2
```
2. Thanks to the -n option, we can specify the namespace targeted by a kubectl request.
   - **Retrieve all pods within a namespace**
```
kubectl get pods -n ns1
```
![Pods in namespaces](/assets/images/namespaces/ns1_without_pod.png)
   - **Deploy a pod within a namespace**
```
kubectl run  nginx-ns1 -n ns1 --image=nginx:latest
kubectl get pods -n ns1
```
![Pods in namespaces](/assets/images/namespaces/ns1_with_pod.png)
   - **Deploy a service within a namespace**
```
kubectl expose pod nginx-ns1 -n ns1 --target-port=80  --port=80 --type=ClusterIP
kubectl get svc -n ns1
```
![Pods in namespaces](/assets/images/namespaces/ns1_with_services.png)
## Service DNS 

- Request a service from the same namespace 
```
kubectl run busybox -it --tty --rm --restart=Never -n ns1  --image=busybox:latest -- wget -S -O - nginx-ns1 # OK
```
- Fail to request service from different namespaces
```
kubectl run busybox -it --tty --rm --restart=Never -n ns2  --image=busybox:latest -- wget -S -O - nginx-ns1 # KO
```
- Succeed to request service from different namespaces

```
kubectl run busybox -it --tty --rm --restart=Never -n ns2  --image=busybox:latest -- wget -S -O - nginx-ns1.ns1.svc.cluster.local #OK
```

![Astuce icon](assets/images/astuce_icon.png) The default namespace could be overridden in kubectl configuration so that you don’t need to specify the namespace (with the -n option) for each request.
```
kubectl config set-context --current --namespace=ns1
```
```
kubectl get pods
```
![Set Context Namespace](/assets/images/namespaces/namespace_set_context.png)
# Reference Documentation
[Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
[POD DNS ](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
[Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)