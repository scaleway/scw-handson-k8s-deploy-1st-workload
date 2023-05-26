---
layout: page
title: "Secrets"
permalink: /secrets
nav_order: 11
---
# Prerequisites
**Finished code for this lesson is available on the Instance Tool at the folder /home/ubuntu/exercice-files/09-secret/ .**
```
cd /home/ubuntu/exercice-files/09-secret/
```

# Tasks
## Lifecycle Management
### Create a secret
```
kubectl create secret generic my-secret --from-literal=name=scaleway_trainee --from-literal=password=P@ssword
```
### Read Secret
```
kubectl get secret my-secret
kubectl get secret my-secret -o yaml
```
![Read Secret](assets/images/secrets/read_secret_yaml.png)

![Astuce icon](assets/images/astuce_icon.png) Secrets are not natively encrypted within kubernetes. Values are just base64 encoded . We could leverage jsonpath output from kubectl to extract one of the values of the secret and decode it using the base64 utility tool.
```
kubectl get secret my-secret -o jsonpath='{.data.name}'|base64 --decode
```
### Delete Secret
```
kubectl delete secret my-secret
```
## Secrets Injection
We first start by creating the secret that will be used. It hold data about user connection.
```
kubectl create secret generic db-secret --from-literal=app_username=scaleway_trainee --from-literal=app_password=P@ssword
```
### As environment variable
1. We create here a pod on which we will inject the secret (db-secret) previously created as environment variables. The container will only display the username and the password in its logs.

We  use here the yaml file **/home/ubuntu/exercice-files/07-storage/pod-secrets-env.yaml**.

- `cat pod-secrets-env.yaml`

```
kubectl create -f pod-secrets-env.yaml
```

1. We can then visualize the logs and ensure that the environment variables are the right one
   
```
kubectl logs pod-secrets-env -f
```

![Secret display logs](assets/images/secrets/secret_pod_logs.png)

### As volume
1. Here we will create a pod , on which secret values will be mounted as volume. 

We  use here the yaml file **/home/ubuntu/exercice-files/07-storage/pod-secrets-volume.yaml**.

- `cat pod-secrets-volume.yaml`

```
kubectl create -f pod-secrets-volume.yaml
```

1. We display the content of the mounted files using kubectl exec.

```
kubectl exec pod-secrets-volume -- cat /db-config/app_password
```

## Image Pull Secrets
In order to pull an image from a private container registry, kubernetes needs credentials. These credentials are stored as “Image Pull Secrets”.

![Astuce icon](assets/images/astuce_icon.png) Environment variables used here have been pre provisioned to ease your work See [Attendee Environment](prerequisites#overview) 
<br/>
1. We first create the kubernetes secret with the username corresponding to the registry name and the password to our scaleway secret key (These data have already been generated in the instance tool as environment variables).
```
kubectl create secret docker-registry registry-secret --docker-server=${SCW_REGISTRY_SERVER} --docker-username=$SCW_REGISTRY_NAME --docker-password=$SCW_SECRET_KEY
```
1. We then indicate the new image when we create the workload object (pod, deployments, …).
```
kubectl run my-first-image --image=${SCW_REGISTRY_SERVER}/$SCW_REGISTRY_NAME/my-first-image:0.1
```
![Image pull registry](assets/images/secrets/image_pull_registry_pod_deployment.png)
Our custom image being an nginx application, we could validate its deployment by looking for the welcome page.
```
kubectl exec -it my-first-image -- curl localhost
```
![Image pull registry test](assets/images/secrets/image_pull_registry_deployment_test.png)

# Reference documentation
- [Kubernetes Secret](https://kubernetes.io/fr/docs/concepts/configuration/secret/)
- [Secret injection Tutorial](https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/)
- [Image Pull Secrets - Scaleway Tutorial](https://www.scaleway.com/en/docs/containers/kubernetes/how-to/deploy-image-from-container-registry/)
- [Image Pull Secrets - Official Kubernetes Tutorial](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)