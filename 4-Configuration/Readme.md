# Configuration

## Exercise

In this exercise, you will continue working with the application from the previous task.  
The backend Service, frontend Service, and IngressRoute should already be deployed in the Kubernetes cluster.

### Step 1
Enable the basic auth security in your todo-app (Frontend)  
The frontend application supports built-in basic authentication, which can be enabled through environment variables.

1. Edit the file `todo-app-deployment.yaml`.
2. In the `todo-app-frontend` Deployment, add the following environment variables to the `frontend` container:  

    - `TODO_APP_AUTH=on`
    - `TODO_APP_SECURITY_ENABLED=on`

3. Apply the changes to the cluster:  

    ```bash
    kubectl apply -f todo-app-deployment.yaml
    ```

4. Verify that the frontend Pod has been restarted:  

    ```bash
    kubectl get pods
    ```

5. Open the todo app in your browser and check whether a basic authentication prompt appears.
6. Log in using the default credentials:  

    - Username: `admin`
    - Password: `admin`

**Example: Environment Variables**
```yaml
containers:
  - name: frontend
    image: your-image
    env:
      - name: TODO_APP_AUTH
        value: "on"
      - name: TODO_APP_SECURITY_ENABLED
        value: "on"
```

### Step 2
Customize the basic authentication credentials.  
In the previous step, you used the default credentials.  
Now create your own credentials by storing them in a Kubernetes Secret and referencing that Secret from the Deployment.

1. Edit the file `todo-app-deployment.yaml`.
2. Add a `Secret` resource with the name `todo-app-basic-auth`.
3. In the data section of the Secret, add the following base64-encoded values:  

    - Username: `admin`
    - Password: `password123`

    #### Hint

    > Values in the `data` section of a Secret must be base64 encoded.  
    > You can encode and decode values using the following commands:  
    >
    > ```bash
    > echo -n "password123"   | base64        # encode
    > echo "cGFzc3dvcmQxMjM=" | base64 -d     # decode
    > ```

4. Apply the changes:  

    ```bash
    kubectl apply -f todo-app-deployment.yaml
    ```

5. Verify that the Secret was created successfully:  

    ```bash
    kubectl get secrets
    ```

6. In the `todo-app-frontend` Deployment, add the following environment variables to the frontend container:  

    - TODO_APP_AUTH_USERNAME
    - TODO_APP_AUTH_PASSWORD  

    Both values must be loaded from the `todo-app-basic-auth` Secret.
7. Apply the updated Deployment:  

    ```bash
    kubectl apply -f todo-app-deployment.yaml
    ```
8. Verify that the frontend Pod has restarted:  

    ```bash
    kubectl get pods
    ```
9. Open the todo app in your browser and verify that you can log in with your custom credentials.

**Example: Secret**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: todo-app-basic-auth
data:
  username: YWRtaW4=
  password: cGFzc3dvcmQxMjM=
```

**Example: Secret Reference in Environment Variables**
```yaml
env:
  - name: TODO_APP_AUTH
    value: "on"
  - name: TODO_APP_SECURITY_ENABLED
    value: "on"
  - name: TODO_APP_AUTH_USERNAME
    valueFrom:
      secretKeyRef:
        name: todo-app-basic-auth
        key: username
  - name: TODO_APP_AUTH_PASSWORD
    valueFrom:
      secretKeyRef:
        name: todo-app-basic-auth
        key: password
```

## Tips

**Useful Commands**

```bash
kubectl get pods ;
kubectl get secrets ;
kubectl describe secret todo-app-basic-auth
```

## Links

[Secret Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)

[Kubernetes YAML Secret Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#secret-v1-core)
