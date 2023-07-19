## K8s demo cluster
In this project, I have set up a simple Kubernetes cluster consisting of two nodes, namely MongoDB and a web application. The configuration files are organized into three YAML files: `mongo-config.yml`, `mongo-secret.yml`, and `mongo.yml`, and two YAML files for the web application, `webapp.yml`, and `webapp-service.yml`. 

`mongo-config.yml` defines a ConfigMap and contains the service name of MongoDB, which serves as the endpoint for the MongoDB service. `mongo-secret.yml` contains a Secret which securely stores the credentials for MongoDB, including the base64-encoded username and password. `mongo.yml` sets up a Deployment for MongoDB with one replica, using the official MongoDB Docker image. Similarly, the `webapp.yml` creates a Deployment for the web application, with two replicas, and connects it to the MongoDB Secret using environment variables. Finally, the `webapp-service.yml` creates a NodePort Service to expose the web application on _port 30000_ to the external world.

## How to run
To run the simulation, you will need to have Minikube installed on your machine. If you haven't installed it yet, you can follow the instructions provided by the Minikube documentation for your specific platform. Once Minikube is installed, start the Minikube cluster with the following command:
```
minikube start
```
After Minikube has started, you can use the kubectl CLI to manage the Kubernetes resources. First, check the status of the Pods in the cluster using:
```
kubectl get pod
```
Next, apply each configuration file in the repository to deploy the Kubernetes cluster. The kubectl apply command is used to manage applications through files that define Kubernetes resources. Apply the files in the following order:
```
kubectl apply -f mongo-config.yml
kubectl apply -f mongo-secret.yml
kubectl apply -f mongo.yml
kubectl apply -f webapp.yml
```

### Interacting with K8s cluster
After deploying the Kubernetes cluster with MongoDB and the web application, you can interact with the cluster to check its status and access the services.

To get an overview of all the resources in the cluster, run the following command:
```
kubectl get all
```
This will display information about the pods, services, and deployments in the cluster, along with their status, replicas, and age. Here is the results I got:
```
NAME                                    READY   STATUS    RESTARTS   AGE
pod/mongo-deployment-855b879886-qcnrb   1/1     Running   0          14m
pod/webapp-deployment-9fccd564d-gvrwl   1/1     Running   0          13m
pod/webapp-deployment-9fccd564d-xx2q4   1/1     Running   0          13m

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP          19h
service/mongo-service    ClusterIP   10.107.88.241    <none>        27017/TCP        14m
service/webapp-service   NodePort    10.104.225.187   <none>        3000:30000/TCP   13m

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/mongo-deployment    1/1     1            1           14m
deployment.apps/webapp-deployment   2/2     2            2           13m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/mongo-deployment-855b879886   1         1         1       14m
replicaset.apps/webapp-deployment-9fccd564d   2         2         2       13m
```

To access the web application, which is exposed as a NodePort service, you can use the external IP and port. In the output of the previous command, you will see the webapp-service listed as a NodePort type service with the format 3000:30000/TCP. This means that the web application can be accessed through port 30000.

If you want to see more detailed information about a specific component, you can use the `describe` command. For example, to get details about the mongo-service or pods, run:
```
kubectl describe service mongo-service
kubectl describe pod webapp-deployment-9fccd564d-xx2q4
```
To observe all the logs from a pod and continuously stream the logs as they are generated, you can use the following command:
```
kubectl logs webapp-deployment-9fccd564d-gvrwl -f
```
This will display the logs from the `webapp` container in the specified pod.

In order to get config map information you can run `kubectl get configmap` and `kubectl get secret`.

### How to access to NodePort
The NodePort Service is always accessible on each worker Node's IP address. In our case we just have one, which is minikube. So, we need the IP address of the minikube. In order to get minikube IP address we need to run `minikube ip` or `kubectl get node -o wide` command. Then you can see the application via your browser.

> :warning: **Known issue - Minikube IP is not accessible through your browser**
You can then access the web application by obtaining the external IP and port assigned to the webapp-service using:
```
minikube service webapp-service
```
You can access the web application in your web browser using the provided URL in the command line.

### Stop minikube cluster
```
minikube stop
```

## Acknowledgement
![University of Oulu](https://img.shields.io/badge/University%20of%20Oulu-Distributed%20Systems-blue?style=flat-square&logoColor=white&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACAAAAAgCAQAAADZc7J/AAAAkklEQVRIDbXBAQEAAAABIP6PzgpvzQ0deT0s8TF49WnjgFIIKBxIPJngDPdxGgrJxah8r5mTWTAAETBvFThKKxt6pEsMRAAyK3J49zIkAAAAASUVORK5CYII=)

This project was completed as part of the Distributed Systems course at the University of Oulu. Please note that this project is for educational purposes, and it is recommended to consult the official Kubernetes and Minikube documentation for more complex and production-ready deployments.