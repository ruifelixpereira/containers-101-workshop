#
# LAB 00
#

# Permanent local overrides
-include .env

# Install docker-ce
setup-docker:
	sudo apt-get update
	sudo apt-get install \
		apt-transport-https \
		ca-certificates \
		curl \
		software-properties-common
	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(shell lsb_release -cs) stable"
	sudo apt-get update
	sudo apt-get install docker-ce -y
	#sudo groupadd docker
	sudo usermod -aG docker $(USER)

# Test docker
test-docker:
	docker run hello-world

# Install az cli 2.0
setup-az:
	echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(shell lsb_release -cs) main" | \
		sudo tee /etc/apt/sources.list.d/azure-cli.list
	sudo apt-key adv --keyserver packages.microsoft.com --recv-keys 52E16F86FEE04B979B07E28DB02C46DF417A0893
	curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
	sudo apt-get install apt-transport-https
	sudo apt-get update && sudo apt-get install azure-cli

# Test az cli 2.0
test-az:
	az --version

# Install jq
setup-jq:
	sudo apt install jq -y
