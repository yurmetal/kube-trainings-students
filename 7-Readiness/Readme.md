# Configuration

## Tasks

In this step we will add a readiness & liveness check for the frontend, backend and database. 
For each container we will use a different option TCP, HTTP, Command

### Exercises


#### Step 1: Create Readiness & Liveness for Frontend over TCP

1.  Add a ReadinessProbe using TCPSocket.
2.  Next add a livenessProbe via TCPSocket
3.  What happens if you use the wrong port for the readinessProbe?
4.  What happens if you use a wrong port for livenessProbe (change the initialDelaySeconds, periodSeconds to a low number)?
5.  Change both ports back and make sure the frontend is liviness and ready.

#### Step 2: Create readiness & liveness for backend via HTTP

##### Backend has the following API path
    1. /api/task - GET - GET Task
    2. /api/task/{taskId} - DELETE - DELETE TASK
    3. /api/task - post - create new TASK
    4. /api/info - GET - return "We <3 Kubernetes".

1. Add readinessProbe to the backend via HTTP.
2. Add livenessProbe to the backend via HTTP.
3. What happens if you clear the statefulset of the postgres container. (Why statefulset? What happens to the data?)
4. 
#### Step 3: Create Readiness & Liveness for postgres via command
1. Now we want to add readiness & liveness check via commands for database.
With the following command you can check if the postgres database is ready.
```console
foo@bar:~$ pg_isready -d <db_name> -h <host_name> -p <port_number> -U <db_user>                      
```
1. Add the readinessProbe to the database using CMD.
2. Add livinessProbe to the database.
3. What happens if you change the database.
 ```Console
ALTER DATABASE kubernetestraining RENAME TO notkubernetestraining;      
ALTER DATABASE notkubernetestraining RENAME TO kubernetestraining;      
    
``` 
4.Alter DATABASE to the old value