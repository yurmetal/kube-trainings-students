# Networking

## Exercise

In this exercise, you will gradually increase the visibility of the todo app until it becomes accessible from a browser.

## Deploy the Todo App

Before creating Services and an Ingress, the frontend and backend applications must already be running in the Kubernetes cluster.

1. If you successfully completed **Task 2 - Deployment**, you can continue directly.
2. Otherwise, deploy the provided solution from Task 2: `kubectl create -f solution/solution-todo-app-deployment.yaml`
3. Verify that both Deployments and Pods are running:  
    1. `kubectl get deployments`
    2. `kubectl get pods`

### Backend Service

For the frontend to communicate with the backend, the backend must be reachable within the cluster network.  
A Service definition for the backend is already provided.

1. Create the backend Service:  
    ```bash
    kubectl create -f todo-app-backend-service.yaml
    ```

1. Run the command `kubectl create -f todo-app-backend-service.yaml` to create a service for the backend
1. Verify with the command `kubectl get service` if your service `todo-app-backend` is created
1. To test your `todo-app-backend` service. We need an environment where we can run the `curl` command. 
   1. So let's execute into the `todo-app-frontend` pod with the comment `kubectl exec -it <TODO_APP_FRONTEND_POD> /bin/sh`. Replace `<TODO_APP_FRONTEND_POD>` with the `todo-app-frontend` pod
   1. Run `curl http://todo-app-backend:80/api/info` and see if you can see the api response `We <3 Kubernetes`

### Frontend Service

Now we need to create a service for our `todo-app-frontend` pods. There is already a file `todo-app-frontend-service.yaml` which contains a template but it's not finished yet. Follow the steps to create your `todo-app-frontend` service

1. Edit the file `todo-app-frontend-service.yaml`
   1. Adjust the labels under `spec.selector` according to your frontend pods
   1. Adjust the targetPort according to your frontend pod port
1. Deploy your `todo-app-frontend` service with the command `kubectl create -f todo-app-frontend-service.yaml`
1. Verify if your `todo-app-frontend` service is created successfully and check if the pods behind it are accessible
    1. So let's execute into the `todo-app-backend` pod with the comment `kubectl exec -it <TODO_APP_BACKEND_POD> /bin/sh`. Replace `<TODO_APP_BACKEND_POD>` with the `todo-app-backend` pod
   1. Run `curl http://todo-app-frontend:80/` and check if you can see the HTML response. If yes, then everything is okay. If not, verify your `todo-app-frontend` service and fix it

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
