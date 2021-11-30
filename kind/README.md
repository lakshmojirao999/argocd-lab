# cluster creation
```
kind create cluster --config kind.yaml

```
# NGINX Ingress installation might differ for your k8s provider
kubectl apply \
    --filename https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml

export INGRESS_HOST=192.168.1.10   
