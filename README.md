# Voting-app-on-minikube
Voting app on minikube

This app is for understading on creating an demo application on kubernetes. We are deploying this application on minikube.

# Minikube Start

```minikube start```

# Minikube Dashboard
```minikube dashboard```

# Steps
 1. Deploy pod

    First create all pods yaml definitions files. Which are voting-app, result-app, redis, postgres-db & worker-app.

 2. Internal Services

    Next we will create services to expose these pods to connect to each other except for the worker pod(which is not receiving any request only sending request to redis & DB. Hence not requires any service).

    First will create one of the internal service which is redis service.
    Second internal service will be postgres service.

 3. External Services

    We need to create external facing services, which is voting serivice & result service. Both these will expose the voting pod & result pod to external world(users) respectively.

# Deploy all yaml definations related to pods & services

```
kubectl apply -f Pod-Defination/voting-app-pod.yaml
kubectl appyy -f Services/voting-app-service.yaml

kubectl apply -f Pod-Defination/result-app-pod.yaml
kubectl appyy -f Services/result-app-service.yaml

kubectl apply -f Pod-Defination/redis-pod.yaml
kubectl apply -f Services/redis-service.yaml

kubectl apply -f Pod-Defination/postgres-db.yaml
kubectl apply -f Services/postgres-service.yaml

kubectl apply -f Pod-Defination/worker-app-pod.yaml
```

After all this you will check pods and services
```
 kubectl get pods,svc -n deployment
NAME                 READY   STATUS             RESTARTS   AGE
pod/postgres-pod     1/1     Running            0          17m
pod/redis-pod        1/1     Running            0          17m
pod/result-app-pod   1/1     Running            0          15m
pod/voting-app-pod   1/1     Running            0          21m
pod/worker-app-pod   1/1     Running            0          3m27s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.96.37.133     <none>        5432/TCP       17m
service/redis            ClusterIP   10.105.230.167   <none>        6379/TCP       17m
service/result-service   NodePort    10.104.231.112   <none>        80:30005/TCP   12m
service/voting-service   NodePort    10.111.216.157   <none>        80:30004/TCP   28m
```
Note that type of internal services redis & db is ClusterIP. And voting-app & result-app were access by external end user so it is created as NodePort.

# Access the web pages
```
minikube service voting-service --url
http://192.168.59.102:30004

minikube service result-service --url
http://192.168.59.102:30005

```

## Note: Deplyoying application as pods comes with chalanges of sacling & rolling updates. So right approach is using deployments.


# Using Deployments

1. Create the deployment and services.
These serivices will be same, if you haven't deleted old one, keep the same. But delete the old pods.

```
kubectl apply -f Deployment/voting-app-deployment.yaml

kubectl apply -f Deployment/redis-deployment.yaml 

kubectl apply -f Deployment/worker-app-deployment.yaml 

kubectl apply -f Deployment/postgres-db-deployment.yaml 

kubectl apply -f Deployment/result-app-deployment.yaml 
```

```
kubectl get all

NAME                                     READY   STATUS             RESTARTS   AGE
pod/postgres-deploy-6bd6b67f74-wlstr     1/1     Running            0          4m54s
pod/redis-deploy-7f878dfd46-wwqfg        1/1     Running            0          5m3s
pod/result-app-deploy-8f446b4-9j4k8      1/1     Running            0          4m40s
pod/voting-app-deploy-85bfc69b8f-4vqjr   1/1     Running            0          5m40s
pod/worker-app-deploy-56f699bdd5-s8v6x   0/1     Running       0          4m58s

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service/db               ClusterIP   10.104.59.77     <none>        5432/TCP       4m29s
service/redis            ClusterIP   10.109.202.207   <none>        6379/TCP       4m24s
service/result-service   NodePort    10.104.202.133   <none>        80:30005/TCP   4m14s
service/voting-service   NodePort    10.105.214.89    <none>        80:30004/TCP   4m20s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/postgres-deploy     1/1     1            1           4m54s
deployment.apps/redis-deploy        1/1     1            1           5m3s
deployment.apps/result-app-deploy   1/1     1            1           4m40s
deployment.apps/voting-app-deploy   1/1     1            1           5m40s
deployment.apps/worker-app-deploy   1/1     1            1           4m58s

NAME                                           DESIRED   CURRENT   READY   AGE
replicaset.apps/postgres-deploy-6bd6b67f74     1         1         1       4m54s
replicaset.apps/redis-deploy-7f878dfd46        1         1         1       5m3s
replicaset.apps/result-app-deploy-8f446b4      1         1         1       4m40s
replicaset.apps/voting-app-deploy-85bfc69b8f   1         1         1       5m40s
replicaset.apps/worker-app-deploy-56f699bdd5   1         1         0       4m58s
```

You can see new deployments are created, according to then pods are created and replica set is also created. Now we can scale our application and rollout the changes.

