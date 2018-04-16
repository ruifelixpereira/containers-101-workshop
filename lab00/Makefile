#
# LAB 00
#

# Set environment variables
export GIT_REPOSITORY?="https://github.com/ruifelixpereira/containers-101-workshop.git"

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

# Clone the git repository
get-labs:
	git clone $(GIT_REPOSITORY)