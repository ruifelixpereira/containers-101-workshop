#
# LAB 03
#

# Set environment variables
export LOCATION?=westeurope
export NAMESPACE_PREFIX?=cnt101-wks
export RESOURCE_GROUP=$(NAMESPACE_PREFIX)-lab3
export APP_NAME?=my-hello-app
export ACR_NAME?=cnt101reg
export ACR_RESOURCEGROUP?=$(NAMESPACE_PREFIX)-lab2
export SUBSCRIPTION?=5dd549af-d55f-4c2c-ba14-8bd7699a59b3
export ACI_NAME?=aci-hello-app
export ACI_DNS?=rfp-aci-hello-app


# Permanent local overrides
-include .env

# Get ACR password
ACR_PWD=$(shell az acr credential show --name $(ACR_NAME) --query "passwords[0].value")
ACR_LOGIN_SERVER=$(shell az acr show --name $(ACR_NAME) --query loginServer --output table)

# Set the CLI to the right subscription
default:
	az account set -s $(SUBSCRIPTION)
	az acr login --name $(ACR_NAME) --resource-group $(ACR_RESOURCEGROUP)
	az acr show \
		--name $(ACR_NAME) \
		--query loginServer \
		--output table

# Create a resource group and create the kubernetes cluster resources inside it
create:
	az container create \
		--resource-group $(RESOURCE_GROUP) \
		--name $(ACI_NAME) \
		--image $(ACR_LOGIN_SERVER)/$(APP_NAME):v1 \
		--cpu 1 \
		--memory 1 \
		--registry-username $(ACR_NAME) \
		--registry-password $(ACR_PWD) \
		--dns-name-label $(ACI_DNS) \
		--ports 80

validate:
	az container show \
		--resource-group $(RESOURCE_GROUP) \
		--name $(ACI_NAME) \
		--query instanceView.state

get-fqdn:
	az container show \
		--resource-group $(RESOURCE_GROUP) \
		--name $(ACI_NAME) \
		--query ipAddress.fqdn

get-logs:
	az container logs \
		--resource-group $(RESOURCE_GROUP) \
		--name $(ACI_NAME)

# Destroy the entire resource group
destroy:
	az group delete \
		--name $(RESOURCE_GROUP) \
		--no-wait
