# Introduction
Samples for Microservices in Practice: Deployment should not be an afterthought article
https://dzone.com/articles/microservices-in-practice-deployment

# Demo

## Checkout souce code
git clone https://github.com/ftntming/microservices-in-practice-deployment

cd microservices-in-practice-deployment

## Deploy mysql
kubectl apply -f resources/mysql.yaml

## Deploy metrics server
helm install mc bitnami/metrics-server

## Build & Deploy 
```
ballerina build -a
kubectl apply -f target/kubernetes/ordermgt
kubectl apply -f target/kubernetes/billing
kubectl apply -f target/kubernetes/shipping
kubectl apply -f target/kubernetes/inventory
kubectl apply -f target/kubernetes/cart
kubectl apply -f target/kubernetes/admin
```

## Deploy Prometheus and Grafana
```
kubectl create configmap ecommerce --from-file resources/prometheus.yml
kubectl apply -f resources/prometheus-deploy.yaml
kubectl apply -f resources/grafana-datasource-config.yaml
kubectl apply -f resources/grafana-deploy.yaml
```

## Expose & Access Admin Service

kubectl port-forward services/admin-svc 8085:8085

curl http://localhost:8085/Admin/invsearch/mango

## Expose & Access Prometheus and Grafana

kubectl port-forward service/prometheus 9090:9090

kubectl port-forward services/grafana 3000:3000

# Cleanup
Run the following to cleanup cluster
```
kubectl delete -f resources/mysql.yaml
kubectl delete -f target/kubernetes/ordermgt
kubectl delete -f target/kubernetes/billing
kubectl delete -f target/kubernetes/shipping
kubectl delete -f target/kubernetes/inventory
kubectl delete -f target/kubernetes/cart
kubectl delete -f target/kubernetes/admin
kubectl delete configmap ecommerce
kubectl delete -f resources/prometheus-deploy.yaml
kubectl delete -f resources/grafana-datasource-config.yaml
kubectl delete -f resources/grafana-deploy.yaml
helm delete mc
```

# Fix for HPA
The original HPA def in generated kubernetes yaml requires the following changes to work,

Add the following,
```
        resources:
          limits:
            memory: "2Gi"
            cpu: "1000m"
          requests: 
            memory: "1Gi"
            cpu: "500m"
```

under  name: "billing" in target/kubernetes/billing/billing.yaml

under  name: "ordermgt" in target/kubernetes/ordermgt/ordermgt.yaml

under  name: "shipping" in target/kubernetes/shipping/shipping.yaml

And re-deploy









