# Pods

## Exercise
In this exercise you will learn how to deploy a simple pod with two containers and how to inspect problems.

### Prerequisite
> Ensure, you are on the correct namespace with the command `kubectl config view --minify --output 'jsonpath={..namespace}'; echo`

### Step 1
1. Run `kubectl create -f todo-app-pod.yaml` to create your pod in the Kubernetes Cluster
2. Check with the command `kubectl get po`, if your pod and also the containers in it are running.
3. Identify the failed container and fix the problem
    1. Use the command `kubectl describe po todo-app`, to see what happens with your pod
    2. Take a look at the bottom of the output (Section Events)
    3. Inspect and fix the error in the `todo-app-pod.yaml` file
4. Redeploy the pod, to apply the fix which you added
    1. `kubectl delete -f todo-app-pod.yaml`
    2. `kubectl create -f todo-app-pod.yaml`
5. Check, if your fix is solving the problem
6. If all containers are up, try to read the logs from your `backend` container with the command `kubectl logs -f todo-app -c backend`

#### Hint

> The docker images which are used in the todo-app-pod.yaml are available in Gitlab.
> If you notice, that something is wrong with the image, then take a look on Gitlab and verify if your docker images
> and also the version/tags which are in defined the task.yaml exists in dockerHub.
>
> [Gitlab - Backend](https://gitlabci.exxeta.com/kubernetes-schulung/demo_backend/container_registry/336) | [Gitlab - Frontend](https://gitlabci.exxeta.com/kubernetes-schulung/demo_frontend/container_registry/332)
>

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
