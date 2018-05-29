#
# LAB 02
#

# Set environment variables
export LOCATION?=westeurope
export NAMESPACE_PREFIX?=cnt101-wks
export RESOURCE_GROUP=$(NAMESPACE_PREFIX)-lab2
export APP_NAME?=my-hello-app
export ACR_NAME?=cnt101reg
export SUBSCRIPTION?=5dd549af-d55f-4c2c-ba14-8bd7699a59b3

# Permanent local overrides
-include ../.env

# Set the CLI to use my subscription
default:
	az account set -s $(SUBSCRIPTION)

# dump resource groups
resources:
	az group list --output table

# Dump list of location IDs
locations:
	az account list-locations --output table

# Create a resource group and create the Container Registry
create:
	-az group create --name $(RESOURCE_GROUP) --location $(LOCATION) --output table
	az acr create \
		--resource-group $(RESOURCE_GROUP) \
		--name $(ACR_NAME) \
		--sku Basic \
		--admin-enabled true

login:
	az acr login --name $(ACR_NAME)

# Show ACR Login Server
show:
	az acr show \
		--name $(ACR_NAME) \
		--query loginServer \
		--output table

# See the existing images
list-local:
	docker images

# Tag local image
tag-image:
	docker tag $(APP_NAME) \
		$(shell az acr show --name $(ACR_NAME) --query loginServer --output tsv)/$(APP_NAME):v1

# Push image to registry
push-image:
	docker push \
		$(shell az acr show --name $(ACR_NAME) --query loginServer --output tsv)/$(APP_NAME):v1

# List images in the registry
list-registry:
	az acr repository list \
		--name $(ACR_NAME) \
		--output table

# Show specific image tags
show-tags:
	az acr repository show-tags \
		--name $(ACR_NAME) \
		--repository $(APP_NAME) \
		--output table

# Destroy the entire resource group
destroy:
	az group delete \
		--name $(RESOURCE_GROUP) \
		--no-wait
