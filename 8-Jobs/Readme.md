# Configuration

## Tasks
In this exercise we will take a closer look at cronjobs. We will create a simple cronjob that simply prints the date.  

We will then add some properties to this job.
### Exercises
 
First we will use cronjob-simple.yaml. In this step we will use the busybox image.
1. Create the cronjob using the command
```console
date; echo Hello from Kubernetes cluster 
```
2. Access the output
3. What can we observe? What happens to the finished pods?
4. How can we change the cronjobs so that only failure and last completed are visible?
5.  How to change the command
```console
date; sleep 300; echo Hello from Kubernetes cluster 
```
What happens now?
6. How can we avoid the job running multiple times?

#### Tips

If you are not familiar with cronjob expression. You can use [crontab.guru](https://crontab.guru/).
[Cronjob Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

