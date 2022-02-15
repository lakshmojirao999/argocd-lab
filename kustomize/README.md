

#######################################################
# Kustomize                                           #
# How to simplify Kubernetes configuration management #
#                                                     #
#######################################################

#########
# Setup #
#########

# Install `kustomize` from https://kubectl.docs.kubernetes.io/installation/kustomize/

kind create cluster --config kind.yaml


# It could be any other Kubernetes cluster with nginx Ingress

################################
# Exploring kustomization.yaml #
################################

git clone https://github.com/vfarcic/argocd-production.git

cd argocd-production

ls -1

ls -1 argo-workflows

cd argo-workflows

ls -1 base/

cat base/kustomization.yaml

# Open https://github.com/argoproj/argo

# Open manifests/base/kustomization.yaml

# Open manifests/base/workflow-controller/workflow-controller-configmap.yaml

cat base/kustomization.yaml

cat base/config.yaml

###########################
# Applying base manifests #
###########################

kustomize build base

kustomize build base \
    | kubectl apply --filename -

#####################
# Applying overlays #
#####################

kubectl --namespace argo get ingresses

ls -1 overlays/production/

cat overlays/production/kustomization.yaml

cat overlays/production/ingress_patch.json

kustomize build overlays/production \
    | kubectl apply --filename -

kubectl --namespace argo get ingresses

##########################
# Upgrading applications #
##########################

kubectl --namespace argo \
    get deployment argo-server \
    --output yaml

cd overlays/production

kustomize edit set image \
    argoproj/argocli=argoproj/argocli:v2.12.4

cat kustomization.yaml

cd ../../

kustomize build \
    overlays/production \
    | kubectl apply --filename -

kubectl --namespace argo \
    get deployment argo-server \
    --output yaml

###########
# Destroy #
###########

git stash

kind delete cluster kind