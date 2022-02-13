#########
# Setup #
#########

# It could be any Kubernetes cluster
kind create cluster

kubectl create namespace argo-events

kubectl apply \
    --filename https://raw.githubusercontent.com/argoproj/argo-events/stable/manifests/install.yaml

kubectl --namespace argo-events apply \
    --filename https://raw.githubusercontent.com/argoproj/argo-events/stable/examples/eventbus/native.yaml



cd argo-events

##########################
# Creating event sources #
##########################

cat event-source.yaml

kubectl --namespace argo-events apply \
    --filename event-source.yaml

kubectl --namespace argo-events \
    get eventsources

kubectl --namespace argo-events \
    get services

kubectl --namespace argo-events \
    get pods

# Replace `[...]` with the name of the `webhook-eventsource-*` Pod
export EVENTSOURCE_POD_NAME=[...]

kubectl --namespace argo-events \
    port-forward $EVENTSOURCE_POD_NAME 12000:12000 &

curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"message":"My first webhook"}' \
    http://localhost:12000/eventsource-toolkit

# Open https://github.com/argoproj/argo-events/blob/master/api/event-source.md#eventsourcespec

#################################
# Creating sensors and triggers #
#################################

cat sensor.yaml

kubectl --namespace argo-events apply \
    --filename sensor.yaml

curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"message":"laksh webhook demo"}' \
    http://localhost:12000/eventsource-toolkit

kubectl --namespace argo-events get pods

kubectl --namespace argo-events logs \
    --selector app=payload

kubectl --namespace argo-events \
    delete pods \
    --selector app=payload

# Open https://github.com/argoproj/argo-events/blob/master/api/sensor.md#sensor

###########
# Destroy #
###########

pkill kubectl

