# Probes

## Exercise

In this exercise, you will add readiness and liveness probes to the frontend, backend, and database.  
Each container will use a different probe type:  
  - Frontend: TCP probe
  - Backend: HTTP probe
  - Database: command-based probe

## Step 1
Add readiness and liveness probes to the frontend using TCP. 

1. Delete the existing `frontend` and `backend` Deployments:  

  ```bash
  kubectl delete deployment <TODO_APP_FRONTEND_DEPLOYMENT_NAME>
  ```  

  ```bash
  kubectl delete deployment <TODO_APP_BACKEND_DEPLOYMENT_NAME>
  ```

2. Delete the existing PersistentVolumeClaims (PVCs):   

  ```bash
  kubectl delete pvc <PVC_NAME>
  ```  

3. Deploy the updated resources:  

  ```bash
  kubectl apply -f task-database.yaml
  ```  

  ```bash
  kubectl apply -f task-deployment.yaml
  ```  

4. Edit the frontend Deployment and add a `readinessProbe` and `livenessProbe` using `tcpSocket`.

    **Example: TCP Probe**
    ```yaml
    spec:
      template:
        spec:
          containers:
            - name: frontend
              image: ...
              ports:
                - containerPort: 9080
              readinessProbe:
                tcpSocket:
                  port: 9080
                initialDelaySeconds: 5
                periodSeconds: 5
              livenessProbe:
                tcpSocket:
                  port: 9080
                initialDelaySeconds: 10
                periodSeconds: 10
    ```

5. Test what happens if you configure the wrong port for the `readinessProbe`.
6. Test what happens if you configure the wrong port for the `livenessProbe`.  
    To observe the effect faster, temporarily reduce values such as:  
      
      - `initialDelaySeconds`
      - `periodSeconds`

7. Restore the correct port values and verify that the frontend Pod becomes both `Ready` and `Running`.

### Step 2
Add readiness and liveness probes to the backend using HTTP. 
The backend provides the following API endpoints:  

  - `GET /api/task` - list tasks
  - `DELETE /api/task/{taskId}` - delete a task
  - `POST /api/task` - create a new task
  - `GET /api/info` - returns **We <3 Kubernetes**

1. Add a `readinessProbe` and `livenessProbe` to the backend using HTTP.  

    **Example: HTTP Probe**
    ```yaml
    readinessProbe:
      httpGet:
        path: /api/info
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 5
    livenessProbe:
      httpGet:
        path: /api/info
        port: 8080
      initialDelaySeconds: 10
      periodSeconds: 10
    ```

2. Use a suitable backend endpoint for the probe configuration.
3. Apply the changes and verify that the backend Pod becomes Ready and Running.
4. Verify the backend API manually from inside the **Backend** Pod:  

    ```bash
    kubectl get pods
    ```  

    ```bash
    kubectl exec -it <TODO_APP_BACKEND_POD> -- /bin/sh
    ```  

    Inside the container, test the endpoint:  

    ```bash
    curl http://localhost:8080/api/info
    ```  

    You should see:  

    ```text
    We <3 Kubernetes
    ```

### Step 3
Delete all resources:  

```bash
kubectl delete -f task-database.yaml
```  

```bash
kubectl delete -f task-deployment.yaml
```  

```bash
kubectl delete pvc postgres-pvc-postgres-database-x
```

## Links

[Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)  

[Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
