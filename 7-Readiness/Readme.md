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

4. Edit the frontend Deployment and add a `readinessProbe` using `tcpSocket`.
5. Add a `livenessProbe` using `tcpSocket`.
6. Test what happens if you configure the wrong port for the `readinessProbe`.
7. Test what happens if you configure the wrong port for the `livenessProbe`.  
    To observe the effect faster, temporarily reduce values such as:  
      
      - `initialDelaySeconds`
      - `periodSeconds`

8. Restore the correct port values and verify that the frontend Pod becomes both `Ready` and `Running`.

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

### Step 2
Add readiness and liveness probes to the backend using HTTP. 
The backend provides the following API endpoints:  

  - `GET /api/task` - list tasks
  - `DELETE /api/task/{taskId}` - delete a task
  - `POST /api/task` - create a new task
  - `GET /api/info` - returns **We <3 Kubernetes**

1. Add a readinessProbe to the backend using HTTP.
2. Add a livenessProbe to the backend using HTTP.
3. Use a suitable backend endpoint for the probe configuration.
4. Apply the changes and verify that the backend Pod becomes Ready and Running.
5. Then delete the PostgreSQL StatefulSet Pod and observe what happens to the backend Pod and the application.
6. Think about the following questions:  

    - Why is PostgreSQL running as a StatefulSet?
    - What happens to the data when the PostgreSQL Pod is restarted?

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

### Step 3
Add readiness and liveness probes to PostgreSQL using commands.  
Now add readiness and liveness probes to the PostgreSQL container by executing a command inside the container.  
The following command can be used to check whether PostgreSQL is ready:  

```bash
pg_isready -d <DB_NAME> -h <HOST_NAME> -p <PORT_NUMBER> -U <DB_USER>
```

1. Add a readinessProbe to the PostgreSQL container using exec.  
2. Add a livenessProbe to the PostgreSQL container using exec.
3. Use the correct database name, host, port, and user in the command.
4. Apply the changes and verify that the PostgreSQL Pod becomes Ready and Running.

**Example: Command Probe**
```yaml
readinessProbe:
  exec:
    command:
      - sh
      - -c
      - pg_isready -d kubernetestraining -h localhost -p 5432 -U postgres
  initialDelaySeconds: 5
  periodSeconds: 5

livenessProbe:
  exec:
    command:
      - sh
      - -c
      - pg_isready -d kubernetestraining -h localhost -p 5432 -U postgres
  initialDelaySeconds: 10
  periodSeconds: 10
```

## Links

[Configure Liveness, Readiness and Startup Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)  

[Liveness, Readiness, and Startup Probes](https://kubernetes.io/docs/concepts/configuration/liveness-readiness-startup-probes/)
