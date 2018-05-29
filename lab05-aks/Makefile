#
# LAB 05
#

# Set environment variables
export LOCATION?=westeurope
export NAMESPACE_PREFIX?=cnt101-wks
export APP_NAME?=azure-vote
export RESOURCE_GROUP=$(NAMESPACE_PREFIX)-lab5
export PLAIN_PREFIX?=$(shell echo $(NAMESPACE_PREFIX) | sed -e 's/-//g')
export CLUSTER_NAME?=rfpAKSCluster
export SUBSCRIPTION?=5dd549af-d55f-4c2c-ba14-8bd7699a59b3
export ACR_NAME?=rfp01
export ACR_RESOURCEGROUP?=containerreg

# Permanent local overrides
-include .env

# Get Service Principal for Kubernetes cluster
SERVICE_PRINCIPAL_ID=$(shell az aks show -g $(RESOURCE_GROUP) --name $(CLUSTER_NAME) -o json | jq -r ".servicePrincipalProfile.clientId")

# Set the CLI to use my subscription
default:
	az account set -s $(SUBSCRIPTION)
	az acr login --name $(ACR_NAME) --resource-group $(ACR_RESOURCEGROUP)

# dump resource groups
resources:
	az group list --output table

# Dump list of location IDs
locations:
	az account list-locations --output table

# Create a resource group and create the kubernetes cluster resources inside it
create:
	-az group create --name $(RESOURCE_GROUP) --location $(LOCATION) --output table
	az aks create \
		--resource-group $(RESOURCE_GROUP) \
		--name $(CLUSTER_NAME) \
		--node-count 2 \
		--node-vm-size Standard_A2_v2 \
		--generate-ssh-keys
#		--no-wait

install-cli:
	az aks install-cli
	
validate:
	az aks show \
		--resource-group $(RESOURCE_GROUP) \
		--name $(CLUSTER_NAME)
	az aks get-credentials \
		--resource-group $(RESOURCE_GROUP) \
		--name $(CLUSTER_NAME)
	kubectl get nodes
	kubectl cluster-info		

# Destroy the entire resource group
destroy:
	az group delete \
		--name $(RESOURCE_GROUP) \
		--no-wait


# Add kubernetes service principal permissions to access ACR with Reader role
allow-access-to-acr:
	az role assignment create \
		--role=Reader \
		--scope=/subscriptions/$(SUBSCRIPTION)/resourceGroups/$(ACR_RESOURCEGROUP)/providers/Microsoft.ContainerRegistry/registries/$(ACR_NAME) \
		--assignee $(SERVICE_PRINCIPAL_ID)

check-access-to-acr:
	az role assignment list \
		--assignee $(SERVICE_PRINCIPAL_ID) --all  


# Deploy kubernetes app to the cluster
deploy-app:
	kubectl create -f azure-vote.yaml


# Explore the created app artifacts
get-deployments:
	kubectl get deployments

describe-deployments:
	kubectl describe deployments

get-replicasets:
	kubectl get rs

get-pods:
	kubectl get pods --show-labels

# Get demo app dashboard external IP address
app-public-ip:
	kubectl get service azure-vote-front --watch

# Get versions
get-cluster-versions:
	az aks get-versions --resource-group $(RESOURCE_GROUP) --name $(CLUSTER_NAME) --location $(LOCATION) --output table

get-instrumentation-key:
	az resource show --resource-group $(NAMESPACE_PREFIX)-monitoring --name $(NAMESPACE_PREFIX) --resource-type "Microsoft.Insights/components" --query 'properties.InstrumentationKey' --output tsv

# Upgrade cluster (takes time, avoid it in demo)
upgrade-cluster-to-%:
	az aks upgrade --resource-group $(RESOURCE_GROUP) --name $(CLUSTER_NAME) --kubernetes-version $*

# Open the management console
browse-console:
	az aks browse --resource-group $(RESOURCE_GROUP) --name $(CLUSTER_NAME)

# Proxy to the management console
proxy:
	kubectl proxy --port 8001


# Scale number of frontend pods
scale-frontend:
	kubectl scale deployment azure-vote-front \
		--replicas=2

# Configure auto-scale
auto-scale:
	kubectl autoscale deployment azure-vote-front \
		--min=2 \
		--max=15 \
		--cpu-percent=80

# Scale the cluster (2 min - avoid it in the demo - it doesn't appear imediately in the Kubernetes admin dashboard)
scale-cluster:
	az aks scale \
		--resource-group=$(RESOURCE_GROUP) \
		--name=$(CLUSTER_NAME) \
		--node-count 3
