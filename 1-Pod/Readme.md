# Pods

## Exercise

In this exercise, you will learn how to deploy a simple Pod with two containers and how to inspect and fix problems.

## Prerequisite

Make sure you are working in the correct namespace:

### Step 1 - Create the Pod and investigate why one of the containers is failing.
1. Create the Pod in the Kubernetes cluster: `kubectl create -f todo-app-pod.yaml`
2. Check whether the Pod is running: `kubectl get pods`
3. Identify the failed container and investigate the problem: `kubectl describe pod todo-app`
4. Look at the Events section at the bottom of the output.
5. Inspect the todo-app-pod.yaml file and fix the error.
6. Redeploy the Pod so that your changes take effect:
    1. `kubectl delete -f todo-app-pod.yaml`
    2. `kubectl create -f todo-app-pod.yaml`
7. Verify that your fix solved the problem.
8. Once both containers are running, read the logs of the backend container: `kubectl logs -f todo-app -c backend`

#### Hint

> The Docker images used in todo-app-pod.yaml are available in Dockerhub.
> If you suspect that the image name or tag is incorrect, check Dockerhub and verify that the image and version exist.
>
> [Dockerhub Registry - Backend](https://hub.docker.com/repository/docker/ikirakosyan/demo_backend/tags)
>
> [Dockerhub Registry - Frontend](https://hub.docker.com/repository/docker/ikirakosyan/demo_frontend/tags)

### Step 2
Gain access to your `frontend` container and check out the `/api/info` endpoint of our backend on Port `8080`

1. You can use the kubectl exec command to get run a specific command inside a container.
   To switch into the shell of the container, run the command `kubectl exec -it PODName -c frontend -- /bin/sh` 
   **Hint:** Replace PODName with your Pod Name from your todo-app
2. If you have multiple containers running inside your pod, you have to use the option `-c ContainerName` to execute into a specific container.
   Otherwise, the default container will be selected.
3. Run `curl localhost:8080/api/info` or `curl todo-app:8080/api/info` in the executed container, to test if the backend is accessible.
4. If you can see `We <3 Kubernetes` response, then you have successfully checked the interface of backend

#### Hint

> You can use the command `curl HOST:PORT/PATH` to verify if a service like webservice etc. is accessible.

### Step 3

We don't need this pod anymore. Delete your pod with the command `kubectl delete -f todo-app-pod.yaml` to prepare for the next task



## Links

You can find helpful information under the following links

[Kubernetes Documentation](https://kubernetes.io/docs/concepts/workloads/pods/pod/)

[Kubernetes YAML Pod Documentation](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.27/#pod-v1-core)
