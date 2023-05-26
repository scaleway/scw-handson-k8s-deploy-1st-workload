---
layout: page
title: "Storage"
permalink: /storage
nav_order: 9
---
# Prerequisites
**Finished code for this lesson is available on the Instance Tool at the folder /home/ubuntu/exercice-files/07-storage/ .**

```
cd /home/ubuntu/exercice-files/07-storage/
```

# Tasks
## Storage Classes

```
kubectl get sc
```
![Storage Classes List](assets/images/storage/storage_classes_list.png)
<br/>

![Astuce icon](assets/images/astuce_icon.png) Scaleway Kapsule integrates with Scaleway CSI to natively allow dynamic provisioning of R/W only storages.

## Persistent Volume Claims
### Creation
We  use here the yaml file **/home/ubuntu/exercice-files/07-storage/pvc.yaml**.

- `cat pvc.yaml`

```
kubectl create -f pvc.yaml
```
The block created is available but not yet attached to an instance as it is not claim by a pod.
![Block Volume Not attached](assets/images/storage/block_volume_not_attached.png)

### Retrieve a PVC
```
kubectl get pvc -o wide
```
### POD Attachment
We will use our PVC to claim storage for a postgres database. 
We  use here the yaml file **/home/ubuntu/exercice-files/07-storage/postgres-deployment.yaml**.

- `cat postgres-deployment.yaml`

```
kubectl create -f postgres-deployment.yaml
```
The volume is created and associated with an instance (see below.)
![Block Volume  attached](assets/images/storage/block_volume_attached.png)

## Persistence Testing
We use here a psql client to ensure that the database information is stored and kept even after pod deletion. 
We  use here the yaml file **/home/ubuntu/exercice-files/07-storage/psql-client.yaml**.

- `cat psql-client.yaml`

```
kubectl create -f psql-client.yaml
```

2. We connect to the database (using kubectl exec) and execute sql requests against our database
 
```
kubectl exec -it psql-client -- sh
```
```
psql -h postgres-svc -U postgres
```

![Astuce icon](assets/images/astuce_icon.png) The database password is postgres.

```
CREATE TABLE CLOUD_PROVIDER(ID SERIAL PRIMARY KEY NOT NULL,NAME TEXT NOT NULL);
INSERT INTO CLOUD_PROVIDER(NAME) VALUES('scaleway');
```

1. Finally we delete the pod and ensure that the data we previously created remains.
   
```
kubectl delete pod postgresql*  #autocomplete could be used here to find the right pod
psql -h postgres-svc -U postgres
SELECT * from CLOUD_PROVIDER
```

# Reference documentation
[Persistent Volumes](https://kubernetes.io/fr/docs/concepts/storage/persistent-volumes/)
[Scaleway CSI](https://github.com/scaleway/scaleway-csi)
