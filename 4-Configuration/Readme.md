# Configuration

## Tasks

We will continue with the previous task. The services for backend, frontend and also the ingress should be deployed on the kubernetes cluster.

### Exercises

#### Step 1: Enable the basic auth security in your todo-app (Frontend)

The frontend has an in-built basic-auth, which can be activated with a environment variable.

1. Adjust the file `todo-app-deployment.yaml` and add to your `todo-app-frontend` deployment and frontend container the environment variables with the keys `TODO_APP_AUTH` and `TODO_APP_SECURITY_ENABLED` and value `on`
2. Apply the change to your `todo-app-frontend` deployment with the command `kubectl apply -f todo-app-deployment.yaml`
3. Verify if the `todo-app-frontend` pods has been restarted
4. Open the todo-app in your browser and see, if you're getting a basic-auth request. The default basic-auth credentials are: `username`: `admin` and `password`: `admin`

**Environment example**
```
...
      containers:
      - name: postgres-db
        image: postgres:11.2
        env:
        - name: "TODO_APP_TITLE"
          value: "This is my environment value :)"
...
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
