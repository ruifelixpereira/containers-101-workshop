#
# LAB 06
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

# Check storage classes
get-storageclasses:
	kubectl get storageclasses

# Create new storage class
create-storageclass:
	kubectl create -f sample-storageclass.yaml

# Create new persistent volume
create-pvc:
	kubectl create -f sample-pvc.yaml

# Get persistent volumes
get-pv:
	kubectl get pv

describe-pv:
	kubectl describe pv

