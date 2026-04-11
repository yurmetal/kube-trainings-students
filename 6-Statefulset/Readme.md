# StatefulSet

## Exercise

In the previous task, you created a PersistentVolumeClaim (PVC) manually for the PostgreSQL database.  
In this exercise, you will use a StatefulSet with a `volumeClaimTemplates` section so that Kubernetes can create PVCs automatically for each Pod.

### Step 1
Clean up the resources from the previous task. 

1. Delete the PostgreSQL Deployment from the previous task:  
    ```bash
    kubectl delete -f todo-app-postgres-database-deployment.yaml
    ```
2. Delete the manually created PVC:  
    ```bash
    kubectl delete -f todo-app-postgres-database-pvc.yaml
    ```
3. Verify that both resources were deleted successfully:  
    ```bash
    kubectl get pvc
    ```  
    ```bash
    kubectl get pods
    ```

### Step 2
Create a statefulset with a volumeClaimTemplate.  
Now update the file `todo-app-postgres-database-statefulset.yaml` so that each PostgreSQL Pod automatically gets its own PVC.

1. Add a volumeClaimTemplates section to the StatefulSet with the following configuration:  
    - Name: `postgres-pvc`
    - Access mode: `ReadWriteOnce`
    - Storage size: `1Gi`
2. Mount the volume in the PostgreSQL container with the following settings:  
    - Volume name: `postgres-pvc`
    - mountPath: `/var/lib/postgresql/data`
    - subPath: `postgres`
3. Create the StatefulSet:  
    ```bash
    kubectl create -f todo-app-postgres-database-statefulset.yaml
    ```
4. Verify the status of the StatefulSet and its Pod:  
    ```bash
    kubectl get statefulsets
    ```  
    ```bash
    kubectl get pods
    ```  
5. Verify that a PersistentVolumeClaim was created automatically:  
    ```bash
    kubectl get persistentvolumeclaims
    ```  
    or
    ```bash
    kubectl get pvc
    ```  

**Example: volumeClaimTemplates**
```yaml
spec:
  volumeClaimTemplates:
    - metadata:
        name: postgres-pvc
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 1Gi
```

### Step 3
Scale up the StatefulSet.  
Imagine that the database is under heavy load and needs to be scaled.  
Scale up the PostgreSQL StatefulSet and observe what happens.  

1. Increase the number of replicas to `2` using one of the following methods:  
    ```bash
    kubectl scale statefulset todo-app-postgres-database --replicas=2
    ```
    or  
    ```bash
    kubectl edit statefulset todo-app-postgres-database
    ```  
    or update `spec.replicas` in `todo-app-postgres-database-statefulset.yaml` and apply the change:
    ```bash
    kubectl apply -f todo-app-postgres-database-statefulset.yaml
    ```
2. Check the StatefulSet:  
    ```bash
    kubectl get statefulsets
    ```
3. Check the Pods:  
    ```bash
    kubectl get pods
    ```
4. Check how many PersistentVolumeClaims currently exist:  
    ```bash
    kubectl get pvc
    ```
5. Scale the StatefulSet back down to `1` replica.
6. Check how many PersistentVolumeClaims still exist and what state they are in:  
    ```bash
    kubectl get pvc
    ```
7. Delete the StatefulSet:  
    ```bash
    kubectl delete -f todo-app-postgres-database-statefulset.yaml
    ```
8. Check whether the automatically created PVCs still exist:  
    ```bash
    kubectl get pvc
    ```

### Observation
A StatefulSet creates stable Pod identities and can automatically create one PVC per Pod through volumeClaimTemplates.  
When scaling down or deleting the StatefulSet, the PVCs are usually not deleted automatically.  
This protects persistent data from being removed unintentionally.  

## Links

[Statefulset Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)  

[PersistentVolumeClaim Documentation](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
