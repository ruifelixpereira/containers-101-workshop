#
# LAB 04
#

# Set environment variables
export LOCATION?=westeurope
export NAMESPACE_PREFIX?=cnt101-wks
export RESOURCE_GROUP=$(NAMESPACE_PREFIX)-lab4
export APP_NAME?=my-hello-app
export IMAGE_NAME?=my-hello-app
export ACR_NAME?=cnt101reg
export ACR_RESOURCEGROUP?=$(NAMESPACE_PREFIX)-lab2
export SUBSCRIPTION?=5dd549af-d55f-4c2c-ba14-8bd7699a59b3

# Permanent local overrides
-include .env

# Get ACR password
ACR_PWD=$(shell az acr credential show --name $(ACR_NAME) --query "passwords[0].value")
ACR_LOGIN_SERVER=$(shell az acr show --name $(ACR_NAME) --query loginServer --output tsv)
WEBAPP_URL=$(shell az webapp show --resource-group $(RESOURCE_GROUP) --name $(APP_NAME) --query hostNames --output tsv)

# Set the CLI to the right subscription
default:
	az account set -s $(SUBSCRIPTION)
	az acr login --name $(ACR_NAME) --resource-group $(ACR_RESOURCEGROUP)
	az acr show \
		--name $(ACR_NAME) \
		--query loginServer \
		--output table

# Create a resource group and create the web app for containers resources inside it
create:
	-az group create --name $(RESOURCE_GROUP) --location $(LOCATION) --output table
	az appservice plan create \
		--name $(APP_NAME)-sp \
		--resource-group $(RESOURCE_GROUP) \
		--sku S1 \
		--is-linux
	az webapp create \
		--resource-group $(RESOURCE_GROUP) \
		--plan $(APP_NAME)-sp \
		--name $(APP_NAME) \
		--deployment-container-image-name hello-world
	az webapp config container set \
		--name $(APP_NAME) \
		--resource-group $(RESOURCE_GROUP) \
		--docker-custom-image-name $(ACR_LOGIN_SERVER)/$(IMAGE_NAME):v1 \
		--docker-registry-server-url $(ACR_LOGIN_SERVER) \
		--docker-registry-server-user $(ACR_NAME) \
		--docker-registry-server-password $(ACR_PWD)

get-webapp-url:
	az webapp show \
		--resource-group $(RESOURCE_GROUP) \
		--name $(APP_NAME) \
		--query hostNames --output tsv		

test-webapp:
	wget $(WEBAPP_URL)

# Destroy the entire resource group
destroy:
	az group delete \
		--name $(RESOURCE_GROUP) \
		--no-wait

# Configure environment variables
config-env:
	az webapp config appsettings set \
		--resource-group $(RESOURCE_GROUP) \
		--name $(APP_NAME) \
		--settings "APP_HELLO=8000"