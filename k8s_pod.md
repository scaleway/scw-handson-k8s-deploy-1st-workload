---
layout: page
title: "Pods"
permalink: /k8s_pod
nav_order: 5
---
# Materials
**Finished code for this lesson is available on the Instance Tool at the folder /home/ubuntu/03-pod/ .**
# Tasks
## Create a single container pod
A POD can be created  using the command line or using a yaml file.
1. Command Line 
```
kubectl run nginx-pod-command-line --image=nginx:latest
```
2. Yaml Description
You need to first create a file with the content below
```
apiVersion: v1
kind: Pod
metadata:
 creationTimestamp: null
 labels:
   run: nginx-pod-yaml
 name: nginx-pod-yaml
spec:
 containers:
 - image: nginx:latest
   name: nginx-pod-yaml
   resources: {}
```
then you can create Kubernetes resources using that yaml descriptor.
```
kubectl create -f single-container-pod.yaml
```
![Astuce icon](assets/images/astuce_icon.png) Using the dry-run capability of kubectl, you could generate  a spec for the pod and write it into a file 

```
kubectl run nginx-pod --image=nginx:latest  --dry-run=client -o yaml > nginx-pod.yaml
```
## Create a multi container pod
```
kubectl create -f multi-container-pod.yaml
```
```
apiVersion: v1
kind: Pod
metadata:
 labels:
  app: nginx-multi-container
 name: nginx-multi-container
spec:
 containers:
  - image: nginx:latest
    name: nginx-container
  - image: redis:latest
    name: redis-container
```
## Get Pods Information
- Get all pods
```
kubectl get pods
```
- Get a specific pod
```
kubectl get pod nginx-pod-yaml
```
![Astuce icon](assets/images/pods/kubectl_get_pods.png)

<br/>

![Astuce icon](assets/images/astuce_icon.png) The **wide** output allows you to request for more details regarding the outputs of the request
```
kubectl get pod nginx-pod-yaml -o wide
```
![Astuce icon](assets/images/pods/kubectl_get_specific_pods_wiide.png)

<br/>

![Astuce icon](assets/images/astuce_icon.png) The json/yaml outputs allows you  to retrieve all objects configuration respectively in json and yaml format.
```
kubectl get pod nginx-pod-yaml -o json
```
![Astuce icon](assets/images/pods/kubectl_get_specific_pods_json.png)

- Describe pod

We can use the kubectl describe command to extract all information about a kubernetes resource  (here a pod).
```
kubectl describe pod nginx-multi-container
```
![Kubectl describe](assets/images/pods/kubectl_describe_pod.png)
- Get logs 
```
kubectl logs nginx-single-container-resource
```
![PODS display logs](assets/images/pods/kubectl_logs_pods.png)
## Configure POD
- Specify POD Resources
```
kubectl create -f single-container-pod-resources.yaml
```
<br/>

```
apiVersion: v1
kind: Pod
metadata:
 labels:
   app: nginx-single-container-resource
 name: nginx-single-container-resource
spec:
 containers:
  - image: nginx:latest
    name: nginx-container
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
- Define a command and Args for a container
```
 kubectl create -f command-container-pod.yaml
```

<br/>

```
apiVersion: v1
kind: Pod
metadata:
 labels:
   app: command-container-pod
 name: command-container-pod
spec:
 containers:
 - image: busybox:latest
   name: busybox-container
   # This command display the message each 5 seconds
   command: ["sh"]
   args: ["-c", "while true; do echo Welcome to Scaleway Kubernetes Handson; sleep 5;done"]
   resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
<br/>

![Command POD logs](assets/images/pods/kubectl_cmd_container_logs.png)

- Inject Environment variables in POD
```
kubectl create -f environment-container-pod.yaml
```
<br/>

```
apiVersion: v1
kind: Pod
metadata:
 labels:
   app: environment-container-pod
 name: environment-container-pod
spec:
 containers:
 - image: busybox:latest
   name: busybox-container
   env:
   - name: MESSAGE
     value: Welcome to Scaleway Kubernetes Hands-on (from env!!!)
   # This command display the message each 5 seconds
   command: ["sh"]
   args: ["-c", "while true; do echo $MESSAGE; sleep 5;done"]
   resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```
![Environment Container logs](assets/images/pods/kubectl_env_container_logs.png)

## Connect to a POD
We can rely on kubectl exec to connect to a pod or to trigger command from a pod.
It can be useful for debugging purpose (network issues, or image issue) or for utility purpose.
```
kubectl exec -it nginx-pod-command-line -- cat /usr/share/nginx/html/index.html
```
**NB** : With the command above, we read the content of the home page of one of the pod we previously deployed.
![Environment Exec command](assets/images/pods/kubectl_exec_pod.png)

<br/>

![Astuce icon](assets/images/astuce_icon.png) You can also use the exec command to launch a shell within the pod
```
kubectl exec -it nginx-pod-command-line -- sh
```
![Environment Exec Shell](assets/images/pods/kubectl_exec_shell.png)
## Destroy a POD
```
kubectl delete pod nginx-pod-command-line
```
![Delete POD](assets/images/pods/kubectl_delete_pods.png)

# Reference Documentation
- [Kubernetes Pod Official Documentation](https://kubernetes.io/docs/concepts/workloads/pods/)
- [Kubectl commands documentation](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
