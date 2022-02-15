

#############################################################################
# Bitnami Sealed Secrets                                                    #
# How To Store Kubernetes Secrets In Git Repositories Without Getting Fired #
#                                      
#############################################################################

#########
# Setup #
#########

# Create a k8s cluster
```
kind create cluster --config kind.yaml
```
Install `kubeseal` CLI from https://github.com/bitnami-labs/sealed-secrets

# Install `kubeseal` CLI from https://github.com/bitnami-labs/sealed-secrets

kubectl apply \
    --filename https://github.com/bitnami-labs/sealed-secrets/releases/download/v0.13.1/controller.yaml

###################
# Sealing secrets #
###################

kubectl --namespace default \
    create secret \
    generic mysecret \
    --dry-run=client \
    --from-literal foo=bar \
    --output json

kubectl --namespace default \
    create secret \
    generic mysecret \
    --dry-run=client \
    --from-literal foo=bar \
    --output json \
    | kubeseal \
    | tee mysecret.yaml

kubectl create \
    --filename mysecret.yaml

kubectl get secret mysecret \
    --output yaml

kubectl get secret mysecret \
    --output jsonpath="{.data.foo}" \
    | base64 --decode && echo

kubeseal --fetch-cert