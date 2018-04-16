#
# LAB 01
#

# Set environment variables
export APP_NAME?=my-hello-app

# Permanent local overrides
-include .env

# Create a new container image with my app
build:
	docker build -t $(APP_NAME) .

# See the new image
show:
	docker images

# Run the container locally
run:
	docker run -d -p 8080:80 $(APP_NAME)

# Test my application running
test:
	wget http://localhost:8080

# Get container ID
ps:
	docker ps

# Print app output
logs:
	docker logs <container id>

# Enter the container if you need
exec-inside:
	docker exec -it <container id> /bin/bash

test2:
	curl -i localhost:8080