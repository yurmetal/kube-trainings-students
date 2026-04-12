# Persistence

## Exercise

In this exercise, you will test whether your todo app data survives container restarts.  
You will then add a PostgreSQL database, connect the backend to it, and finally make the database persistent by using a PersistentVolumeClaim (PVC).

### Step 1
Verify the current persistence behavior of the todo app.  
Containers are stateless by default.  
Let's first verify whether the todo data is still available after restarting the backend Pods.

1. Open the todo app in your browser and create a few todos.
2. Restart all backend Pods by deleting them one by one:  
   
   ```bash
   kubectl get pods
   ```  
   
   ```bash
   kubectl rollout restart deployment <TODO_APP_BACKEND_DEPLOYMENT>
   ```  

    Replace <TODO_APP_BACKEND_DEPLOYMENT> with the name of a backend Deployment.  

3. Refresh the todo app in your browser.
4. Check whether your todos are still visible.  

   > At this point, the todos will most likely be gone.

### Step 2
Deploy a PostgreSQL database.  
To persist the todo data, the backend needs a database.  
All required definition files are already prepared.  

1. Create the Secret for the PostgreSQL database:  
    
    ```bash
    kubectl create -f todo-app-postgres-database-secret.yaml
    ```

2. Create the PostgreSQL Service:  
    
    ```bash
    kubectl create -f todo-app-postgres-database-service.yaml
    ```

3. Create the PostgreSQL Deployment:  
    
    ```bash
    kubectl create -f todo-app-postgres-database-deployment.yaml
    ```

4. Update the existing backend Deployment so that it connects to the database:  
    
    ```bash
    kubectl apply -f todo-app-deployment.yaml
    ```

5. Verify that both the PostgreSQL database and the backend are running:  
    
    ```bash
    kubectl get deployments
    ```  

    ```bash
    kubectl get pods
    ```

6. If one of them is not running, inspect the problem and fix it.

### Step 3
Verify whether the todos are stored in the database

1. Open the todo app in your browser and create a few todos.
2. Restart the backend Pods:  

    ```bash
    kubectl get pods
    ```  

    ```bash
    kubectl delete pod <TODO_APP_BACKEND_POD>
    ```

3. Refresh the todo app and check whether the todos are still visible.  

    > At this point, the todos should still be available because they are now stored in the database.
4. Restart the PostgreSQL Pod:  

    ```bash
    kubectl get pods
    ```  

    ```bash
    kubectl rollout restart deployment <TODO_APP_POSTGRES_DATABASE_DEPLOYMENT_NAME>
    ```  

5. Refresh the todo app and check whether the todos are still visible.

### Step 4
Create a PersistentVolumeClaim for the database.  
The PostgreSQL database is still stateless.  
To make it persistent, create a PersistentVolumeClaim (PVC) and later mount it into the PostgreSQL Pod.  

1. Create a file named `todo-app-postgres-database-pvc.yaml`.
2. Define a `PersistentVolumeClaim` based on the example below.
3. Use a unique name for your PVC.  
4. Request a storage size of 1Gi.  
5. Deploy the PVC:  

    ```bash
    kubectl apply -f todo-app-postgres-database-pvc.yaml
    ```

6. Verify that the PVC was created successfully:  

    ```bash
    kubectl get pvc
    ```

**Example: PersistentVolumeClaim**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <YOUR_UNIQUE_PVC_NAME>
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

### Step 5
Mount the PVC into the PostgreSQL Deployment.  
Now update the PostgreSQL Deployment so that it uses the PVC as persistent storage.  

1. Edit the file `todo-app-postgres-database-deployment.yaml`.
2. Mount the PVC as a volume in the PostgreSQL container with the following settings:  

    - mountPath: /var/lib/postgresql/data
    - subPath: postgres
    - claimName: use the PVC name from Step 4

3. Apply the updated Deployment:  

    ```bash
    kubectl apply -f todo-app-postgres-database-deployment.yaml
    ```

4. Verify that the PostgreSQL Pod has been restarted and is in the Running state:  

    ```bash
    kubectl get pods
    ```

5. If the Pod is not running, inspect the issue and fix it.  

**Example: Volume Mount**
```yaml
spec:
  containers:
    - name: postgres-database
      image: postgres:11.2
      volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
          subPath: postgres
  volumes:
    - name: postgres-storage
      persistentVolumeClaim:
        claimName: <YOUR_UNIQUE_PVC_NAME>
```

### Step 6
Verify if the postgres database stores the data in the PVC. 

1. Open the todo app in your browser and create a few todos.  

    > If you get an error while adding new todos, restart the backend Pod.  
    > After the database restart, the backend may temporarily lose its database connection.

2. Restart the PostgreSQL Pod:  

    ```bash
    kubectl get pods
    ```  

    ```bash
    kubectl delete pod <TODO_APP_POSTGRES_DATABASE_POD_NAME>
    ```

3. Refresh the todo app and check whether the todos are still visible.  

    > At this point, the todos should still be available because PostgreSQL now stores its data on the PVC.

### Step 7
Scale the PostgreSQL database.  
Sometimes applications need to scale.  
Now try to scale the PostgreSQL Deployment and observe what happens.  

1. Edit the file `todo-app-postgres-database-deployment.yaml` and set `spec.replicas` to `2`.
2. Apply the change:  

    ```bash
    kubectl apply -f todo-app-postgres-database-deployment.yaml
    ```

3. Verify whether two PostgreSQL Pods are created:  

    ```bash
    kubectl get pods
    ```

4. If scaling does not work as expected, inspect one of the Pods:  

    ```bash
    kubectl describe pod <TODO_APP_POSTGRES_DATABASE_POD_NAME>
    ```  

    > A second PostgreSQL Pod is not expected to run successfully in this setup.

## Links

[Kubernetes Documentation](https://kubernetes.io/docs/concepts/storage/volumes/)

[Kubernetes YAML Persistent Volume Claim (PVC) Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#persistentvolumeclaim-v1-core)

[Kubernetes YAML Persistent Volume (PV) Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#persistentvolume-v1-coree)
