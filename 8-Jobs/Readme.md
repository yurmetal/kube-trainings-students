# CronJobs

## Exercise

In this exercise, you will learn how to create and manage a Kubernetes CronJob.  
You will create a simple CronJob that prints the current date and a message, and then explore how CronJobs behave over time.

## Step 1
Create a simple CronJob.  
You will use the file `cronjob.yaml` and the `busybox` image.  

1. Edit the CronJob definition and configure the container to run the following command:  
    ```yaml
    command:
      - /bin/sh
      - -c
      - date; echo Hello from Kubernetes cluster
    ```
2. Create the CronJob:  
    ```bash
    kubectl apply -f cronjob.yaml
    ```
3. Verify that the CronJob was created:  
    ```bash
    kubectl get cronjobs
    ```
4. Wait for the CronJob to run and create a Job and Pod.
5. Check the created Jobs:  
    ```bash
    kubectl get jobs
    ```
6. Check the Pods created by the Jobs:  
    ```bash
    kubectl get pods
    ```
7. View the logs of one of the Pods:  
    ```bash
    kubectl logs <POD_NAME>
    ```

## Step 2
Observe CronJob behavior.  

1. What do you observe after multiple executions?  
    - How many Pods are created?
    - What happens to completed Pods?
2. By default, Kubernetes keeps finished Jobs and their Pods.

## Step 3
Limit the number of stored Jobs.  
To avoid too many completed Jobs, you can configure the CronJob to keep only a limited number of successful and failed Jobs.  

1. Edit the CronJob and add the following fields:
    ```yaml
    successfulJobsHistoryLimit: 1
    failedJobsHistoryLimit: 1
    ```
2. Apply the changes:
    ```bash
    kubectl apply -f cronjob.yaml
    ```
3. Observe how old Jobs and Pods are cleaned up automatically.

## Tips

### Useful Commands
```bash
kubectl get cronjobs
kubectl get jobs
kubectl get pods
kubectl logs <POD_NAME>
kubectl describe cronjob <CRONJOB_NAME>
```

### Cron Expression Help
If you are not familiar with Cron expressions, use: https://crontab.guru/

## Links

[Cronjob](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)
