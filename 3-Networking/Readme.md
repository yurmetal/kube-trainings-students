# Networking

## Exercise

In this exercise, you will gradually increase the visibility of the todo app until it becomes accessible from a browser.

### Deploy the Todo App

Before creating Services and an Ingress, the frontend and backend applications must already be running in the Kubernetes cluster.

1. If you successfully completed **Task 2 - Deployment**, you can continue directly.
2. Otherwise, deploy the provided solution from Task 2:  

    ```bash
    kubectl create -f ../2-Deployment/todo-app-deployment.yaml
    ```
3. Verify that both Deployments and Pods are running:  

    ```bash
    kubectl get deployments
    ```  
    ```bash 
    kubectl get pods
    ```

### Backend Service

For the frontend to communicate with the backend, the backend must be reachable within the cluster network.  
A Service definition for the backend is already provided.

1. Create the backend Service:  

    ```bash
    kubectl create -f todo-app-backend-service.yaml
    ```
2. Verify that the Service `todo-app-backend` was created successfully:

    ```bash
    kubectl get services
    ```
3. Verify that the `todo-app-backend` Service is correctly linked to the backend Pod.  

    ```bash
    kubectl describe svc todo-app-backend
    ```  
    Look for the Endpoints section in the output, for example:

    ```text
    Endpoints: 10.X.X.X:8080
    ```  
    This indicates that the Service is correctly connected to the backend Pod.

4. Test whether the backend Service is reachable.  
    First, open a shell in one of the `todo-app-frontend` Pods:  

   ```bash
   kubectl get pods
   ```
   ```bash
   kubectl exec -it <TODO_APP_FRONTEND_POD> -- /bin/sh
   ```  
   Replace `<TODO_APP_FRONTEND_POD>` with the name of your frontend Pod.
5. Inside the container, test the backend endpoint:  

    ```bash
    curl http://todo-app-backend:80/api/info
    ```
6. If the response contains the following text, the backend Service is working correctly: `We <3 Kubernetes`

    #### Hint

    > In Task 2, the frontend used a custom configuration to reach the backend (e.g. `localhost` or a host mapping).  
    > After separating the applications, this configuration may no longer work.  
    > Think about how the frontend can now access the backend.  

### Frontend Service

Now create a Service for the `todo-app-frontend` Pods.  
The file `todo-app-frontend-service.yaml` already exists, but it is not complete yet.

1. Edit the file `todo-app-frontend-service.yaml`.
2. Adjust the following fields:  
    - `spec.selector` so that it matches the labels of your frontend Pods
    - `spec.ports[].targetPort` so that it matches the port exposed by the frontend container
3. Create the frontend Service:  

    ```bash
    kubectl create -f todo-app-frontend-service.yaml
    ```
4. Verify that the Service was created successfully:  

    ```bash
    kubectl get services
    ```
5. Test whether the frontend Service is reachable.  
   First, open a shell in one of the `todo-app-backend` Pods:  

    ```bash
    kubectl get pods
    ```  
    ```bash
    kubectl exec -it <TODO_APP_BACKEND_POD> -- /bin/sh
    ```
6. Inside the container, test the frontend Service:  

    ```bash
    curl http://todo-app-frontend:80/
    ```
7. If you receive an HTML response, the frontend Service is working correctly.  
    If not, inspect the Service definition and fix the issue.

### Ingress

If you completed the previous steps successfully, both applications are now reachable inside the Kubernetes cluster.  
To make the frontend accessible from outside the cluster, create an IngressRoute for Traefik.

> Since Traefik is used as the Ingress controller, this exercise also uses the Traefik resource Middleware.  
> Replace the placeholder path in the Middleware with exactly the same path that you define in the IngressRoute.

1. Edit the file `todo-app-ingress.yaml`.
2. Adjust the following fields:  
    - `metadata.name` - This name must be unique across the entire Kubernetes cluster.
    - `spec.routes.services` - Configure it to point to the todo-app-frontend Service created earlier.
3. Create or apply the Ingress configuration:  

    ```bash
    kubectl apply -f todo-app-ingress.yaml
    ```
4. Open the application in your browser using the path you defined in the IngressRoute: `https://what.ever.host/<YOUR_UNIQUE_PATH>/`

> Make sure the URL ends with /.  
> Otherwise, the page may not load correctly.

## Tips

### Commands

Command | Purpose
--- | ---
`kubectl get pods` | List Pods and their names
`kubectl exec -it POD_NAME -- /bin/sh` | Open a shell inside a container
`nslookup SERVICE_NAME` | Resolve the IP address of a Service from inside a Pod
`curl -vvvk http://HOST/PATH` | curl -vvvk http://HOST/PATH

## Links

[Service Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)

[Kubernetes YAML Service Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#service-v1-core)

[Kubernetes YAML Ingress Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#ingress-v1beta1-extensions)
