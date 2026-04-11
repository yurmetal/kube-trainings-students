# Pods

## Exercise

In this exercise, you will learn how to deploy a simple Pod with two containers and how to inspect and fix problems.

## Prerequisite

Make sure you are working in the correct namespace:

### Step 1
Create the Pod and investigate why one of the containers is failing.

1. Create the Pod in the Kubernetes cluster:  
    ```bash
    kubectl create -f todo-app-pod.yaml
    ```
2. Check whether the Pod is running:  
    ```bash
    kubectl get pods
    ```
3. Identify the failed container and investigate the problem:  
    ```bash
    kubectl describe pod todo-app
    ```
4. Look at the Events section at the bottom of the output.
5. Inspect the todo-app-pod.yaml file and fix the error.
6. Redeploy the Pod so that your changes take effect:
    ```bash
    kubectl delete -f todo-app-pod.yaml
    ```
    ```bash
    kubectl create -f todo-app-pod.yaml
    ```
7. Verify that your fix solved the problem.
8. Once both containers are running, read the logs of the backend container:  
    ```bash
    kubectl logs -f todo-app -c backend
    ```

#### Hint

> The Docker images used in todo-app-pod.yaml are available in Dockerhub.
> If you suspect that the image name or tag is incorrect, check Dockerhub and verify that the image and version exist.
>
> [Dockerhub Registry - Backend](https://hub.docker.com/repository/docker/ikirakosyan/demo_backend/tags)
>
> [Dockerhub Registry - Frontend](https://hub.docker.com/repository/docker/ikirakosyan/demo_frontend/tags)

### Step 2
Access the frontend container and test the backend endpoint /api/info on port 8080.

1. Open a shell inside the frontend container:  
    ```bash
    kubectl exec -it POD_NAME -c frontend -- /bin/sh
    ```  
    Replace **POD_NAME** with the name of your Pod.
2. If a Pod contains multiple containers, you must specify the target container with -c **CONTAINER_NAME**.  
    Otherwise, Kubernetes will use the default container.
3. Inside the container, test whether the backend is reachable:  
    ```bash
    curl localhost:8080/api/info
    ```
4. If the response contains: `We <3 Kubernetes` then the backend is reachable.

#### Hint

> You can use the following pattern to test whether an application endpoint is accessible: `curl HOST:PORT/PATH`

### Step 3
You no longer need this Pod. Delete it to prepare for the next exercise:  
    ```bash
    kubectl delete -f todo-app-pod.yaml
    ```

## Links

You can find helpful information under the following links

[Kubernetes Documentation](https://kubernetes.io/docs/concepts/workloads/pods/pod/)

[Kubernetes YAML Pod Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#pod-v1-core)
