# Kubernetes Training 0:


## Session 1 - Pods:
Pods
A Pod (as in a pod of whales or pea pod) is a group of one or more containers, with shared storage and network resources, and a specification for how to run the containers. 

run
```
vi deploy-nginx0.yml
```
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-prod
spec:
  containers:
  - name: nginx
    image: tcr.aocs-int.t-systems-service.com/training/nginx:1.14.2
    ports:
    - containerPort: 80
```
```
kubectl apply -f  deploy-nginx0.yml
```

Delete 
```
kubectl delete pod nginx-prod -n userX
```
```
kubectl delete -f  deploy-nginx0.yml
```

## Session 2 - Deployments:
Deployments
You describe a desired state in a Deployment, and the Deployment Controller changes the actual state to the desired state at a controlled rate. You can define Deployments to create new ReplicaSets, or to remove existing Deployments and adopt all their resources with new Deployments.

```
vi deploy-nginx1.yml
```
```
kubectl apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1 # tells deployment to run 1 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: tcr.aocs-int.t-systems-service.com/training/nginx:1.14.2
        ports:
        - containerPort: 80

```
```
kubectl apply -f  deploy-nginx1.yml
```


Check rollout status 

```
kubectl rollout status deployment nginx-deployment -n userX
```

```
kubectl get pods -n userX
```

```
vi deploy-nginx1.yml
```

```
kubectl apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 1 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: tcr.aocs-int.t-systems-service.com/training/nginx:1.14.2
        ports:
        - containerPort: 80

```
```
kubectl apply -f  deploy-nginx1.yml
```

```
kubectl get pods -n userX
```

```
kubectl delete --all pods -n userX & watch -n 1 kubectl get pods -n userX
```



## Session 3 - Services

Deployment of test tool
```
kubectl apply -f manifests/deploy-swiss-army-knife
```

```
kubectl exec -it deployment/swiss-army-knife-deployment /bin/bash -n user0
```

````
kubectl get pods -l app=nginx -o yaml -n userX | grep podIP
````

```
kubectl exec -it deployment/swiss-army-knife-deployment /bin/bash -n user0
curl http://podip
exit
```

````
vi deploy-nginx3.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: tcr.aocs-int.t-systems-service.com/training/nginx:1.16.1
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    run: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
```

```
kubectl exec -it deployment/swiss-army-knife-deployment /bin/bash -n user0
nslookup nginx
curl http://nginx
exit
```
