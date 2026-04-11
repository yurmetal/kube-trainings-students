# Persistence

## Tasks

### Step 1: Verify the persistence of your todo-app

Containers are stateless. But we should also to be able to fetch our data after a container restart. Let's try if we can see our todos in the todo-app after a container restart.

1. Add some todos in the todo-app
1. Restart all `todo-app-backend-xxxx` Pods. Just run the command `kubectl delete pod <TODO_APP_BACKEND_POD>` to force the deployment to re-create the pods (so we force, to restart it)
1. Verify if you can see the todos in your todo-app (Hopefully not :))

### Step 2: Deploy a postgres database

To persist the todos, we need a database. In this step we will deploy a database. All necessary definitions file already prepared. Just deploy it and verify, if the application starts.

1. Run `kubectl create -f todo-app-postgres-database-secret.yaml` to create the necessary secret
1. Run `kubectl create -f todo-app-postgres-database-service.yaml` to create the database service
1. Run `kubectl create -f todo-app-postgres-database-deployment.yaml` to deploy the database deployment
1. Run `kubectl apply -f todo-app-deployment.yaml` to update the existing `todo-app-backend` deployment to connect to the database
1. Verify if the `todo-app-postgres-database` and `todo-app-backend` deployment is running. If it's not running, verify the issue and fix it

### Step 3: Verify the persistence of your todos

1. Add in the todo-app some todos (via your Browser)
1. Restart your `todo-app-backend` pods with the command `kubectl delete pod <TODO_APP_BACKEND_POD_NAME>`
1. Check if you can still see your todos?
   1. We expect here, that you can see still your todos => Todos are stored in the database

1. Restart your `todo-app-postgres-database` pods with the command `kubectl delete pod <TODO_APP_POSTGRES_DATABASE_POD_NAME>`
1. Check if you can still see your todos?
   1. We expect here, that you cannot see your todos => Your database is still stateless

### Step 4: Create a PVC for your database

The postgres database is still stateless. Create a PVC and mount it into your `todo-app-postgres-database` deployment to make it stateful

1. Create a file `todo-app-postgres-database-pvc.yaml ` with  a PVC based on the example below.
1. Apply a unique name for your persistent volume claim
1. Add a storage size of `1Gi`
1. Deploy your persistent volume claim
1. Check your persistent volume claim status with the command `kubectl get pvc <yourPVCName>`


**Example**
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <YOUR_UNIQUE_PVC_NAME>
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: <STORAGE_SIZE>
```

### Step 5: Mount your PVC to the postgres database

1. Update the file `todo-app-postgres-database-deployment.yaml` and mount the PVC as a volume and mount it
   1. Mount Path `/var/lib/postgresql/data`
   1. Subpath `postgres`
   1. Take care, that you use the PVC Name from Step 4
1. Apply the changes in the `todo-app-postgres-database.yaml` file to the kubernetes cluster with the command `kubectl apply -f todo-app-postgres-database-deployment.yaml` 
1. Verify, if your `todo-app-postgres-database` pods has been restarted and are in state `RUNNING`. If not, try to figure out the issue and fix it.

**Example**
```
...
    spec:
      containers:
      - name: postgres-datbase
        image: postgres:11.2
        volumeMounts:
        - name: %VolumeName%
          mountPath: %MountPath%
          subPath: postgres
      volumes:
      - name: %VolumeName%
        persistentVolumeClaim:
          claimName: %YourPersistenceVolumeClaimName%
...

```


### Step 6: Verify if the postgres database stores the data in the PVC

1. Add some todos in the todo app (via Browser)
   1. Hint: If you're getting error while adding new todos, just restart the backend. The due the restart of the database, the backend loses the connection. We have to restart this
2. Restart your `todo-app-postgres-database` pods with the command `kubectl delete pod <TODO_APP_POSTGRES_DATABASE_POD_NAME>`
3. Check if you can still see your todos?
   1. We expect here, that the todos are still there => Postgres database is statefull now

### Step 7: Scale your postgres database

Sometimes, we need to scale our database. Let's try it

1. Update the file `todo-app-postgres-database-deployment.yaml` and increase `spec.replicas` to 2 and apply the change to the kubernetes cluster with the command `kubectl apply -f todo-app-postgres-database-deployment.yaml`
2. Verify, if you have now two `todo-app-postgres-database-xxxx` pods. If not try to figure out why it's not scaling? You can use here the `kubectl describe pod <TODO_APP_POSTGRES_DATABASE_POD_NAME>` command
   1. Hint: We do not expect a second pod here ;)


## Links

[Kubernetes Documentation](https://kubernetes.io/docs/concepts/storage/volumes/)

[Kubernetes YAML Persistent Volume Claim (PVC) Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#persistentvolumeclaim-v1-core)

[Kubernetes YAML Persistent Volume (PV) Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#persistentvolume-v1-coree)
