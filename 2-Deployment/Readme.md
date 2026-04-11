# Deployment

## Tasks

In this exercise, we will create a deployment definition based on the pod definition in task 1, try some features of a deployment and split this into two deployments to show the real life usage of a deployment

### Exercise

#### Step 1

Create a deployment definition based on the pod definition from task 1

1. Extend the file `todo-app-deployment.yaml` by a pod template based pod definition of task 1
1. Run `kubectl create -f todo-app-deployment.yaml` to create your extended deployment definition in the Kubernetes Cluster. 

>In best case you should not see an error. If you got an error after executing the command above, you should verify the deployment definition and fix it. You can verify the status of your deployment with the command `kubectl get deployment` and `kubectl get pod`

#### Step 2
 
Only in very few cases a deployment contains multiple container. Deploying a backend and frontend in the same deployment is not a good practice. So split the Deployment in your `todo-app-deployment.yaml` into 2 Deployments. One of them should be with the Deployment name `todo-app-frontend` and the other one `todo-app-backend`. You can put the two Deployment it in the same file. Just separate it with `---`

1. Undeploy the `todo-app` with the command `kubectl delete -f todo-app-deployment.yaml`
1. Separate your `todo-app` Deployment into two Deployments and rename it
   1. Frontend: `todo-app-frontend`
   1. Backend: `todo-app-backend`
   1. Also take care to keep for each deployment the labels unique. For example adjust the `app.kubernetes.io/component` label for each service
1. Deploy your deployments with the `kubectl create -f ...` command and verify, if they're running without issues. You should have two deployments (each deployment 1 pod).

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
