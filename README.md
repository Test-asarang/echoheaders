# echoheaders
a simple server that responds with the http headers it received

[Original source lives in kubernetes/contrib](https://github.com/kubernetes/contrib/tree/master/ingress/echoheaders).

This repository demonstrates various ways to expose applications outside of google kubernetes engine (GKE) clusters.

# Quickstart
Deploy the application, node-port, and GCE internal-load-balancer
```
kubectl apply -f deployment.yaml
kubectl apply -f node-port.yaml
kubectl apply -f internal-load-balancer.yaml
```

The above `kubectl` commands create an echoserver deployment and exposes it as a NodePort and an Internal Load Balancer.

## View the Node Port Service
```
kubectl describe service node-port-for-echo-headers
```

## Inspect the Internal Load Balancer
```
kubectl describe service internal-load-balancer-for-echo-headers
```

# Nginx Ingress Examples
First install [the nginx controller](./docs/nginx-ingress-controller-setup.md). Then, follow these steps.

Create secret with self-signed certificate, service, and ingress
```
kubectl -f nginx-ingresses/*.yaml
```

The path from outside the gke cluster to the echo headers pods is as follows; nginx ingress => http-svc service => echoheaders pod

Verify nginx ingress operation
```
LB_IP=$(kubectl get service ingress-nginx -ningress-nginx -ojsonpath='{.status.loadBalancer.ingress[].ip}')

# execute from a colo VM
curl -skv --resolve foo.bar.com:443:${LB_IP} https://foo.bar.com/

# execute from a colo VM
curl -skv --resolve baz.bar.com:80:${LB_IP} http://baz.bar.com/
```
