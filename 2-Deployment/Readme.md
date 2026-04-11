# Deployment

## Exercise

In this exercise, you will create a Deployment based on the Pod definition from the previous task.  
You will also explore some Deployment features and then split the application into two separate Deployments to reflect a more realistic setup.

### Step 1
Create a Deployment definition based on the Pod definition from Task 1.

1. Extend the file `todo-app-deployment.yaml` with a Pod template based on the Pod definition from Task 1.
2. Create the Deployment in the Kubernetes cluster: `kubectl create -f todo-app-deployment.yaml`
3. Verify that the Deployment and Pod were created successfully:  
   1. `kubectl get deployments`  
   2. `kubectl get pods`
4. If you get an error, inspect the Deployment definition, fix the issue, and deploy it again.

#### Hint

> In the best case, the Deployment is created without errors.  
> If an error occurs, review the YAML definition carefully and correct it before continuing.

### Step 2
 
In real-world scenarios, a Deployment usually contains only one application component.
Running both frontend and backend in the same Deployment is not considered good practice.

Split the todo-app Deployment in your todo-app-deployment.yaml file into two separate Deployments:
   1. todo-app-frontend
   2. todo-app-backend

You can keep both Deployments in the same file and separate them with `---`.
1. Delete the existing todo-app Deployment: `kubectl delete -f todo-app-deployment.yaml`
2. Split the todo-app Deployment into two separate Deployments:
   1. Frontend: todo-app-frontend
   2. Backend: todo-app-backend
3. Make sure each Deployment uses unique labels.  
   For example, adjust the label `app.kubernetes.io/component` so that it correctly identifies either frontend or backend.

#### Step 3

After splitting the applications into two Deployments, we are able to scale a specific application. So lets scale the backend from 1 pod to 3 pods

1. Update in the `todo-app-deployment.yaml` file in your `todo-app-backend` Deployment the key `spec.replicas` to `3`
2. Apply the change to the Kubernetes Cluster with the command `kubectl apply -f todo-app-deployment.yaml`
3. Verify, if you have 3 pods starting with the name `todo-app-backend-<xxxx>`. 
4. There is also another way to scale the deployment. Let's figure it out. Instead of editing the `todo-app-deployment.yaml` file, you can scale the deployment directly in the kubernetes cluster (Note: This is just a temporary change)
   1. `kubectl scale deployment todo-app-backend --replicas=4` or
   2. `kubectl edit deployment todo-app-backend` and increase in the editor the `replicas` count to 4


**Hint**
> `It should be noted that the scaled service must be designed to operate in multiple instances.`


## Links

[Deployment Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

[Kubernetes YAML Deployment Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#deployment-v1-apps)
