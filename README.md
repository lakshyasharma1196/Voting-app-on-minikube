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
kubectl apply -f voting-app.yaml
kubectl appyy -f voting-app-service.yaml

kubectl apply -f result-app.yaml
kubectl appyy -f result-app-service.yaml

kubectl apply -f redis.yaml
kubectl apply -f redis-service.yaml

kubectl apply -f postgres-db.yaml
kubectl apply -f postgres-service.yaml

kubectl apply -f worker-app.yaml
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









