# Networking

## Exercise

In this exercise, you will gradually increase the visibility of the todo app until it becomes accessible from a browser.

## Deploy the Todo App

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
2. Verify that the Service todo-app-backend was created successfully:
    ```bash
    kubectl get services
    ```
3. Test whether the backend Service is reachable.  
    First, open a shell in one of the todo-app-frontend Pods:  
   ```bash
   kubectl get pods
   ```
   ```bash
   kubectl exec -it <TODO_APP_FRONTEND_POD> -- /bin/sh
   ```  
   Replace `<TODO_APP_FRONTEND_POD>` with the name of your frontend Pod.
4. Inside the container, test the backend endpoint:  
    ```bash
    curl http://todo-app-backend:80/api/info
    ```
5. If the response contains the following text, the backend Service is working correctly: `We <3 Kubernetes`

### Frontend Service

Now create a Service for the todo-app-frontend Pods.  
The file `todo-app-frontend-service.yaml` already exists, but it is not complete yet.

1. Edit the file `todo-app-frontend-service.yaml`.
2. Adjust the following fields:  
    - spec.selector so that it matches the labels of your frontend Pods
    - spec.ports[].targetPort so that it matches the port exposed by the frontend container
3. Create the frontend Service:  
    ```bash
    kubectl create -f todo-app-frontend-service.yaml
    ```
4. Verify that the Service was created successfully:  
    ```bash
    kubectl get services
    ```
5. Test whether the frontend Service is reachable.  
   First, open a shell in one of the todo-app-backend Pods:  
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

#### Ingress

If you have successfully completed the previous points, your applications (frontend and backend) are accessible within the Kubernetes Cluster.Finally an Ingress has to be created so that your application is accessible outside the Kubernetes Cluster, i.e. via the Internet.

> Due the usage of Traefik Ingress Controller, we have to use the Ressource `Middleware`. In this case just replace the placeholder `/%YOURPATH%` with the exact same path as in the IngressRoute

1. Adjust the file `todo-app-ingress.yaml`
    1. Adjust `metadata.name`. **Hint** This name has to be unique over the **whole** kubernetes cluster!
    1. Define the `spec.routes.services` section to point to the `todo-app-frontend` service you created above.
1. Visit the browser page on the path you chose and verify if you can see the todo-app
    1. URL: `https://what.ever.host/<%YourUniquePath%>/

> Do not miss the / at the end of the URL, otherwise you will just see a blank page!

### Question: What happens, if you restart the backend?

1. Try it with `kubectl delete pod demo-backend-xxxx`
    1. Replace xxxx with the unique id
2. Refresh the ToDo App

## Tips

### Commands

Command | Use
--- | ---
`kubectl get po` | get pods and their IDs
`kubectl exec -it PODNAME /bin/sh` | exec into a container
`nslookup some.dns.name` | look up IP for a service (Make sure to be inside a container in Kubernetes)
`curl -vvvk http://some.dns.name/path/to/resource` | to make a get request to the resource

## Links

[Service Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)

[Kubernetes YAML Service Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#service-v1-core)

[Kubernetes YAML Ingress Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#ingress-v1beta1-extensions)
