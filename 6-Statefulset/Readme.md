# Statefulset

## Tasks

Until now, we created for every pod a persistent volume claim by hand. Now we want to define a template, how a pvc should look and let them create automaticaly by the statefulset.

### Exercise

#### Step 1: Clean up the previous task

1. Delete the Deployment `todo-app-postgres-database` from the previous task (5-Persistence) with the command `kubectl delete -f todo-app-postgres-database-deployment.yaml`
1. Delete the your created PVC with the command `kubectl delete -f todo-app-postgres-database-pvc.yaml`
1. Verify if the both recources are deleted
   1. `kubectl get pvc` for PVC
   2. `kubectl get po` for Postgres Database

#### Step 2: Create a statefulset with a volumeClaimTemplate

1. Append in the file `todo-app-postgres-database-statefulset.yaml`a volumeClaimTemplate to the statefulset with the following requirements:
    1. Name: postgres-pvc
    1. AccessMode: ReadWriteOnce
    1. Storage Size 1Gi
1. Mount the volume like in the persistent task with the following details.
    1. The name should be matched with pvc name `postgres-pvc`
    1. Mountpath: `/var/lib/postgresql/data`
    1. Subpath: `postgres`
1. Deploy the statefulset with the command `kubectl create -f todo-app-postgres-database-statefulset.yaml`
1. Check the status of your statefulset
    1. `kubectl get statefulset`
    1. `kubectl get pod`
1. Check if a persitent volume claim is created and the status of it!
    1. `kubectl get persistentvolumeclaim` OR
    1. `kubectl get pvc`

**VolumeClaimTemplates Example:**
```
  spec:
    ...
  volumeClaimTemplates:
  - metadata:
      name: my-volume-claim-name
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

#### Step 3: Scale up your StatefulSet

Imagine, our database is overwhelmed and has to be scaled up to process the requests quickly. Scale up your database statefulset and see what happens

1. Scale up the database statefulset like a deployment (you learned it already in the deployment task ;)
    1. `kubectl scale statefulset todo-app-postgres-database --replicas=2` OR
    1. `kubectl edit statefulset todo-app-postgres-database` OR
    2. Adapt `spec.replicas` in the `todo-app-postgres-database-statefulset.yaml` with the command `kubectl apply -f todo-app-postgres-database-statefulset.yaml`
2. Check the statefulset
3. Check the pods
4. Check how many persistent volume claims (pvc) currently exist.
5. Now scale down your statefulset to 1 replica
6. Check how many persistent volume claims (pvc) currently exist and the state each of them.
7. Check what happens, if you delete the statefulset
    1. `kubectl delete -f todo-app-postgres-database-statefulset.yaml`
    2. Check if the automatic created pvc are still available?
