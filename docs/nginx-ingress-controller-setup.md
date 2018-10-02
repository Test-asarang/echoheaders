# NGINX Controller Setup

This guide creates the nginx ingress controller which uses an internal load balancer. Based on [this guide](https://github.com/kubernetes/ingress-nginx/blob/master/docs/deploy/index.md).

Deploy all required config maps, namespace, nginx ingress controller, etc.

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/mandatory.yaml
```

internal-load-balancer-for-nginx-ingresses.yaml (fka, cloud-generic.yaml)
```yaml
kind: Service
apiVersion: v1
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
  labels:
    app: ingress-nginx
  annotations:
    cloud.google.com/load-balancer-type: "Internal"
spec:
  externalTrafficPolicy: Local
  loadBalancerSourceRanges:
  - 10.0.0.0/8  # defaults to 0.0.0.0/0
  type: LoadBalancer
  selector:
    app: ingress-nginx
  ports:
  - name: http
    port: 80
    targetPort: http
  - name: https
    port: 443
    targetPort: https

```

Create the internal load balancer for nginx ingress controller
```
kubectl apply -f internal-load-balancer-for-nginx-ingresses.yaml
```

Create a secret with the TLS/SSL certificate and key.
```
kubectl create secret tls tls-secret --key tls.key --cert tls.crt
```

Ideally, this secret is stored in hashicorp vault and placed into your gke cluster.
