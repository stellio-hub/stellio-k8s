# Introduction

The deployment files have been tested on a Minikube cluster (for the moment). Other users have reported successful tests
on rancher RKE, RKE2, OVH managed K8S, Kubernetes 1.20, 1.24...

# Quick start on a Minikube instance

For instructions on how to install Minikube, please follow https://minikube.sigs.k8s.io/docs/start/.

* Start your cluster

```shell
minikube start
```

* Create a secret for the credentials to use to connect to PostgreSQL

```
kubectl create secret generic pg-credentials \
    --from-literal=POSTGRES_USER='stellio' \
    --from-literal=POSTGRES_PASS='stellio_password'
```

* Register the config maps
 
```shell
kubectl apply -f stellio-configmap.yaml \
-f kafka-configmap.yaml
```

* Create the persistent volumes (in a production setup, they have been created beforehand by an admin)

```
kubectl apply -f stellio-pv.yaml
```

* Start all pods
  
```shell
kubectl apply -f api-gateway-deployment.yaml \
-f kafka-deployment.yaml \
-f postgres-deployment.yaml \
-f search-service-deployment.yaml \
-f subscription-service-deployment.yaml
```

* Start ingress

```shell
kubectl apply -f stellio-ingress.yaml # started in ingress-nginx namespace
```

* Enable and configure Ingress addon for Minikube

```shell
minikube addons enable ingress
minikube tunnel
```

* Check you can access Stellio with a sample HTTP request

```shell
http http://localhost/ngsi-ld/v1/entities?type=Device
```

* Add a json-ld context server using kubectl -k => kustomize

```shell
kubectl apply -k
```

* To follow the status of the pods

```shell
kubectl get pods
```

* To see the logs of a pod

```shell
kubectl logs -f <pod_name>
```
