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

#### Step 2: Customize your basic-auth credentials

In the first step, we used the default credentials. Now customize your basic-auth credentials. Use for this a `Secret` Ressource and reference the secret to the environment variables

1. Adjust the file `todo-app-deployment.yaml` and add a `Secret` Ressource with the name `todo-app-basic-auth`
1. Add the following information to the `data` section. Take care, to add the values as base64 encoded
   1. Key: `username` Value: `admin`
   1. Key: `password` Value: `password123`
1. Apply the secret to the kubernetes cluster with the command `kubectl apply -f todo-app-deployment.yaml`
1. Verify if the secret has been created successfully with the command `kubectl get secrets`
1. Now add two environment variables `TODO_APP_AUTH_USERNAME` and `TODO_APP_AUTH_PASSWORD` reference the values from the `todo-app-basic-auth` secret (see example)
1. After that, apply the change `kubectl apply -f todo-app-deployment.yaml`
1. Verify if the `todo-app-frontend` pod has been restarted
2. Open the todo-app in your browser and see, if you're able to authentificate with your defined credentials.

**Secret example**
```
apiVersion: v1
kind: Secret
metadata:
  name: <SECRET_NAME>
data:
  username: YWRtaW4=
  password: c2VjcmV0
```

**Environment Secret Reference**
```yaml
       env:
       - name: CUSTOM_USERNAME
         valueFrom:
           secretKeyRef:
             name: <SECRET_NAME>
             key: <KEY_IN_SECRET_UNDER_DATA>
```


## Tips

### Secrets

[Secret Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)

[Kubernetes YAML Secret Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#secret-v1-core)
