# Deployment

## Exercise

In this exercise, you will create a Deployment based on the Pod definition from the previous task.  
You will also explore some Deployment features and then split the application into two separate Deployments to reflect a more realistic setup.

### Step 1
Create a Deployment definition based on the Pod definition from Task 1.

1. Extend the file `todo-app-deployment.yaml` with a Pod template based on the Pod definition from Task 1.

   **Example:**
   ```yaml
   spec:
      containers:
      - name: frontend
        image: <YOUR_FRONTEND_IMAGE>
        ports:
          - containerPort: 9080
      - name: backend
        image: <YOUR_BACKEND_IMAGE>
        ports:
          - containerPort: 8080
      hostAliases:
         - ip: "127.0.0.1"
           hostnames:
           - "todo-app-backend"
   ```

2. Create the Deployment in the Kubernetes cluster:  

   ```bash
   kubectl create -f todo-app-deployment.yaml
   ```

3. Verify that the Deployment and Pod were created successfully:  

   ```bash
   kubectl get deployments
   ```  
   
   ```bash
   kubectl get pods
   ```

4. If you get an error, inspect the Deployment definition, fix the issue, and deploy it again.

#### Hint

> In the best case, the Deployment is created without errors.  
> If an error occurs, review the YAML definition carefully and correct it before continuing.

### Step 2
 
In real-world scenarios, a Deployment usually contains only one application component.
Running both frontend and backend in the same Deployment is not considered good practice.

Split the todo-app Deployment in your `todo-app-deployment.yaml` file into two separate Deployments:
   - `todo-app-frontend`
   - `todo-app-backend`

You can keep both Deployments in the same file and separate them with `---`.  

1. Delete the existing todo-app Deployment:  

   ```bash
   kubectl delete -f todo-app-deployment.yaml
   ```

2. Split the todo-app Deployment into two separate Deployments:

   - Frontend: `todo-app-frontend`
   - Backend: `todo-app-backend`  

   **Example:**
      ```yaml
      apiVersion: apps/v1
      kind: Deployment
      metadata:
      name: todo-app-frontend
      spec:

      ---

      apiVersion: apps/v1
      kind: Deployment
      metadata:
      name: todo-app-backend
      spec:
      ```

3. Make sure each Deployment uses unique labels.  

   - Adjust the label `app.kubernetes.io/component` so that it clearly identifies the application part:
      - `app.kubernetes.io/component: frontend`
      - `app.kubernetes.io/component: backend`  

   - Keep the label `app.kubernetes.io/name` the same for both Deployments:
      - `app.kubernetes.io/name: todo-app`
4. Create the new Deployments:  

   ```bash
   kubectl create -f todo-app-deployment.yaml
   ```

5. Verify that both Deployments are running successfully:  

   ```bash
   kubectl get deployments
   ```  

   ```bash
   kubectl get pods
   ```

   You should now see two Deployments, each with one Pod.

### Step 3

After splitting the frontend and backend into separate Deployments, you can scale them independently.

Now scale the backend from 1 Pod to 3 Pods.

1. In the file `todo-app-deployment.yaml`, update the `spec.replicas` value of the `todo-app-backend` Deployment to `3`.
2. Apply the updated configuration:  

   ```bash
   kubectl apply -f todo-app-deployment.yaml
   ```

3. Verify that three backend Pods are running:  

   ```bash
   kubectl get pods
   ```  

   You should see three Pods with names starting with: `todo-app-backend-<xxxx>`
4. There are also other ways to scale a Deployment without editing the YAML file directly.  
   These changes are applied directly in the cluster and may be overwritten later by the manifest.  
   Scale the Deployment to `3` replicas using one of the following commands:  

   ```bash
   kubectl scale deployment todo-app-backend --replicas=3
   ```  

   or  

   ```bash
   kubectl edit deployment todo-app-backend
   ```  

   Then update the `replicas` field to `3` in the editor.

1. Update in the `todo-app-deployment.yaml` file in your `todo-app-backend` Deployment the key `spec.replicas` to `3`
2. Apply the change to the Kubernetes Cluster with the command  

   ```bash
   kubectl apply -f todo-app-deployment.yaml
   ```

3. Verify, if you have 3 pods starting with the name `todo-app-backend-<xxxx>`. 
4. There is also another way to scale the deployment. Let's figure it out. Instead of editing the `todo-app-deployment.yaml` file, you can scale the deployment directly in the kubernetes cluster (Note: This is just a temporary change)  

   ```bash
   kubectl scale deployment todo-app-backend --replicas=3
   ```  

   or  

   ```bash
   kubectl edit deployment todo-app-backend
   ```  

   and increase in the editor the `replicas` count to `3`

#### Hint

> The application must be designed to run in multiple instances before scaling it.  
> For example, it should not depend on local in-memory state that would break when multiple replicas are running.

## Links

[Deployment Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

[Kubernetes YAML Deployment Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#deployment-v1-apps)
