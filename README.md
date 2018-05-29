# Containers 101 Workshop Hands-on-Labs

All the materials are available [here](https://github.com/ruifelixpereira/containers-101-workshop).


## Lab 00. Environment Setup (10 min)

**Prepare VM**

Create a new Ubuntu VM  17.10 (e.g., size B2s) in Azure and install `make` on it:

```shell
sudo apt install make
```

**Clone lab materials**

Now, inside your new workstation VM just clone all the materials from github:

```shell
git clone https://github.com/ruifelixpereira/containers-101-workshop.git
```

**Install Docker**

I've prepared a `Makefile` to automate the installation for you:

```shell
cd containers-101-workshop
cd lab00-setup
make setup-docker
```

Now, just logout and login again to reflect changes in your user and test that docker is running:

```shell
make test-docker
```

Alternatively, if you prefer you can follow the instructions available:
* Install Docker CE on Ubuntu like [this](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-repository) 
* Add user to `docker` group to avoid having to do `sudo` everytime, like [this](https://docs.docker.com/install/linux/linux-postinstall/)
* Logout and login again to reflect changes in your user.

**Install Azure CLI 2.0**

You can use the same `Makefile` and run:

```shell
make setup-az
```

and in the end just test it:

```shell
make test-az
```

Alternatively, if you prefer you can follow the instructions available [here](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-apt?view=azure-cli-latest)


**Install jq**

`jq` is a tool for processing JSON inputs which is useful in some cases. To install it just use:

```shell
make setup-jq
```

and in the end just test it with:

```shell
jq
```

**CentOS Install**

IF you have CentOS you can follow the steps to prepara the workstation:
* Docker CE [install](https://docs.docker.com/install/linux/docker-ce/centos/#set-up-the-repository).
* az CLI 2.0 [install](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-yum?view=azure-cli-latest)


## Lab 01. Containers Hello World (10 min)

Here I am introducing some docker concepts like *image*, *container* and *Dockerfile*. Just examine the provided `Dockerfile` with a very simple example:

```docker
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/* /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Let's explore this in more detail.

**Containers "Hello World"**

I've prepared a `Makefile` to automate the lab steps for you (just to avoid having to write too much):

```shell
cd containers-101-workshop
cd lab01-docker
make build
```

This creates a new container image `my-hello-app`. You can check in the end the images added to your local repository:

```shell
docker images
```

or running:

```shell
make show
```

which will give this output:

![lab01-01-docker-images](./media/lab01-01-docker-images.png)

You can test running a new container instance from the newly created image by using either of these commands:

```shell
docker run -d -p 8080:80 my-hello-app
```

or

```shell
make run
```

and invoke with

```shell
make test
```

This is a sample output:

![lab01-02-run-test](./media/lab01-02-run-test.png)

And you can check the docker processes running with

```shell
make ps
```

![lab01-03-docker-ps](./media/lab01-03-docker-ps.png)


Alternatively, if you prefer you can follow the [step by step](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-app) instructions.

I am using this [application](https://github.com/Azure-Samples/aci-helloworld) for testing purposes.


## Lab 02. Private Container Registry (10 min)

Let's create a private container registry in Azure using the Azure Container Registry service.

**Prepare**

First you need to login with:

```shell
az login
```

I've prepared a `Makefile` to automate the lab steps for you (just to avoid having to write too much) that uses Azure CLI 2.0, but you need to customize it at least by putting your Azure subscription Id in `SUBSCRIPTION`:

```shell
# Set environment variables
export LOCATION?=westeurope
export NAMESPACE_PREFIX?=cnt101-wks
export RESOURCE_GROUP=$(NAMESPACE_PREFIX)-lab2
export APP_NAME?=my-hello-app
export ACR_NAME?=cnt101reg
export SUBSCRIPTION?=5dd549af-d55f-4c2c-ba14-8bd7699a59b3
```
To guarantee that you are working in the right subscription:

```shell
cd containers-101-workshop
cd lab02-acr
make
```

And check the correct subscription ID, like in my example:

![lab02-01-az-set-account](./media/lab02-01-az-set-account.png)


**Create new registry**

To create the new registry:

```shell
cd containers-101-workshop
cd lab02-acr
make create
```

This creates a new container registry `cnt101-wks-lab2`. If you want you can change this name by updating the environment variables in your `Makefile`. The output produced should be something like:

![lab02-02-create](./media/lab02-02-create.png)

You can check the created registry:

```shell
make show
```

![lab02-03-show](./media/lab02-03-show.png)

And you can also login in the new container registry:

```shell
make login
```

![lab02-04-login](./media/lab02-04-login.png)


Alternatively, if you prefer you can follow the [step by step](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-prepare-acr).


**Push image to registry**

Let's also test to push a local image into our new registry.

First, list your local images:

```shell
make list-local
```

![lab02-05-local](./media/lab02-05-local.png)

And tag the existing image `my-hello-app` with a version and the registry prefix to `cnt101reg.azurecr.io/my-hello-app:v1`

```shell
make tag-image
```

![lab02-06-tag](./media/lab02-06-tag.png)

And now you can push the tagged local image to your registry

```shell
make push-image
```

![lab02-07-push](./media/lab02-07-push.png)


Just list the images in the registry to check the new one

```shell
make list-registry
make show-tags
```

![lab02-08-list-registry](./media/lab02-08-list-registry.png)


## Lab 03: Deploy Azure Container Image (10 min)

Assuming that you have completed the previous *Lab02* with the creation of a private container registry and the upload of a sample image, let's now create an instance of such image using the simple service Azure Container Image.

You can check this by running

```shell
cd containers-101-workshop
cd lab03-aci
make
```

![lab03-01-check](./media/lab03-01-check.png)

**Deploy App**

Deploy the app container by running

```shell
make create
```

And check in the end if it's running

```shell
make validate
```

![lab03-02-validate](./media/lab03-02-validate.png)

To test it just get the URL

```shell
make get-fqdn
```

![lab03-03-fqdn](./media/lab03-03-fqdn.png)

Now for testing just use wget

```shell
make test-aci
```

or invoke the url in your browser, like in my example: 
`http://rfp-aci-hello-app.westeurope.azurecontainer.io`

![lab03-04-test](./media/lab03-04-test.png)

You can also view the log output of the container:

```shell
make get-logs
```

![lab03-05-logs](./media/lab03-05-logs.png)


Alternatively, if you prefer you can follow the [step by step](https://docs.microsoft.com/en-us/azure/container-instances/container-instances-tutorial-deploy-app).


## Lab 04: Simple Web App in Containers (10 min)

Assuming that you have completed the previous *Lab02* with the creation of a private container registry and the upload of a sample image, let's now create an Azure Web App based in a container instance of such image.

You can check this by running

```shell
cd containers-101-workshop
cd lab04-webapp
make
```

![lab04-01-default](./media/lab04-01-default.png)

**Deploy App**

Deploy the web  app container by running

```shell
make create
```

And check in the end the uRL of the new Web app

```shell
make get-webapp-url
```

![lab04-02-url](./media/lab04-02-url.png)

You can also check the docker registry settings of the new web app:

![lab04-03-docker](./media/lab04-03-docker.png)


Now for testing just use wget

```shell
make test-webapp
```

![lab04-04-wget](./media/lab04-04-wget.png)

or invoke the url in your browser, like in my example: 
`http://my-hello-app.azurewebsite.net`

![lab04-05-test](./media/lab04-05-test.png)

You can also change application settings environment variables like this example:

```shell
make config-env
```

![lab04-06-env](./media/lab04-06-env.png)

And you can also check these changes in the Azure Portal

![lab04-07-env-protal](./media/lab04-07-env-portal.png)


Alternatively, if you prefer you can follow the [step by step](https://docs.microsoft.com/en-us/azure/app-service/containers/tutorial-custom-docker-image#use-a-docker-image-from-any-private-registry-optional).


### Lab 05: Create an Azure Managed Kubernetes cluster (25 min)

Until now we've been playing with simple scenarios, almost single container applications. Let's now move on to a scenario where we need to create an application with several components and deploy it in a Kubernetes cluster. We will continue to use the container registry created in the previous *Lab02*.

Let's start by checking that our environment is ready:

```shell
cd containers-101-workshop
cd lab05-aks
make
```

![lab05-01-default](./media/lab05-01-default.png)

**Create the Kubernetes cluster**

Create the managed Kubernetes cluster with

```shell
make create
```

![lab05-02-create](./media/lab05-02-create.png)


In Kubernetes we need the kubectl tool to talk with the orchestrator that resides in the master node(s). To install it in our workstation just do:

```shell
make install-cli
```

If you get a *permission denied* error, just do:

```shell
sudo make install-cli
```

![lab05-03-cli](./media/lab05-03-cli.png)

Now let's validate that our cluster is ready and we can administer it with kubectl.

```shell
make validate
```

We can see here that our cluster has 2 agent nodes to run our application workloads.

![lab05-04-validate](./media/lab05-04-validate.png)

In the Azure Portal we can also see the created cluster:

![lab05-05-cluster](./media/lab05-05-cluster.png)

Since we might deploy applications where the container images reside in the registry created in *Lab02*, let's ensure that the new Kubernetes cluster has the proper permissions to access it:

```shell
make allow-access-to-acr
```

![lab05-05-reg](./media/lab05-06-reg.png)

And just validate this access:

```shell
make check-access-to-acr
```

![lab05-06-check](./media/lab05-07-check.png)

**Access to the Kubernetes management console**

We have available the Kubernetes console that is only accessible locally with a tunnel. To start the tunnel you can do it in 2 ways:

If you are in your workstation you can open a browser to the console like this:

```shell
make browse-console &
```

If you are in a remote workstation using SSH, start the proxy:

```shell
kubectl proxy --port 8001 &
```

create a tunnel to the remote workstation using something like this sample:

```shell
ssh -L 9000:127.0.0.1:8001 azureuser@<your workstation ip>
```

And then browse to [http://localhost:9000/ui](http://localhost:9000/ui)

In case you get an error like this one:

![lab05-10-proxyerr](./media/lab05-10-proxyerr.png)

just replace the URL from this:

http://localhost:9000/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/

to this:

http://localhost:9000/api/v1/namespaces/kube-system/services/http:kubernetes-dashboard:/proxy/


**Deploy the app**

Our sample [Azure Vote](https://docs.microsoft.com/en-us/azure/aks/tutorial-kubernetes-prepare-app) application has 2 components (2 containers) that will be deployed in 2 Kubernetes pods:
* Redis cache listening at port 6379.
* Web frontend listening at port 80 that uses data residing in the Redis cache.

This is the sample Kubernetes yaml file with the 2 components which are mapped to 2 Kubernetes Deployments and 2 Kubernetes Services:

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  minReadySeconds: 5 
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
          limits:
            cpu: 500m
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Let's deploy this app to our cluster:

```shell
make deploy-app
```

![lab05-11-deploy](./media/lab05-11-deploy.png)

You can check in the Kubernetes console the deployment progress until you get this:

![lab05-12-deploy-web](./media/lab05-12-deploy-web.png)

Since this is the firt application with a load balancer that we deploy, it might take a couple of minutes to get an assigned IP address for the azure-vote-front web application.

You can get the IP adress from the Kubernetes console:

![lab05-13-ip](./media/lab05-13-ip.png)

Or running

```shell
make app-public-ip
```

![lab05-14-ip](./media/lab05-14-ip.png)

Now just test the newly deployed application opening your brwser with the collected IP address: `http://[you-ip-address]`

![lab05-15-app](./media/lab05-15-app.png)


**Scale the application**

In a Kubernetes cluster is very simple to scale our application.

At this moment our applciation has 2 pods:
* Redis cache listening at port 6379.
* Web frontend listening at port 80 that uses data residing in the Redis cache.

You can check this with

```shell
make get-deployments
```

or

```shell
make get-pods
```

![lab05-16-pods](./media/lab05-16-pods.png)

or in the Kubernetes console

![lab05-17-pods](./media/lab05-17-pods.png)

Now let's scale up our azure-vote-front from 1 to 2 pods (e.g., to serve more users in the front-end). Again, we can do this using CLI commands

```shell
make scale-frontend
```

![lab05-18-scale](./media/lab05-18-scale.png)

and we can check it

```shell
make get-deployments
```

![lab05-19-scale](./media/lab05-19-scale.png)

Alternatively, we can scale using the Kubernetes console by navigating to `Overview / Deployments` and choosing the `Scale` option for the desired Deployment

![lab05-20-scale](./media/lab05-20-scale.png)

choose the desired number of pods and hit `OK`

![lab05-21-scale](./media/lab05-21-scale.png)

You can check the progress immediately

![lab05-22-scale](./media/lab05-22-scale.png)


Since we scaled up the `azure-vote-front` you can check the associated service and realize that the load balancer now can use the 3 pods

![lab05-23-lb-01](./media/lab05-23-lb-01.png)
![lab05-24-lb-02](./media/lab05-24-lb-02.png)


### Lab 06: AKS Persistent Volumes

In many scenarios we don't want to use the internal container's filesystem since it can be temporary and we want to have a persistent storage that can be shared amongst several containers. Imagine the storage to store data files for a database cluster.

![kubernetes-sql-after-node-fail.png
](./media/kubernetes-sql-after-node-fail.png)

In our scenario we want ot have a shared storage for all the instances of `azure-vote-front`. To do this in Kubernetes we need to create what's called a `Persistent Volume` and then mount this volume in the desired pods.

Let's start by checking that our environment is ready:

```shell
cd containers-101-workshop
cd lab06-aks-persistent-volumes
make
```

![lab06-01-default](./media/lab06-01-default.png)

**Check Storage classes**

To create a `Persistent Volume` we need to decide which `Storage Class` we want according to our requirements.

First we need to check which `Storage Classes` exist by default in our Kubernetes cluster.

```shell
make get-storageclasses
```

![lab06-02-storageclasses](./media/lab06-02-storageclasses.png)

You can also use the Kubernetes console:

![lab06-03-storageclasses](./media/lab06-03-storageclasses.png)

We can see that by default we have 2 storage classes:
* `default` - using Azure Standard storage with managed disks;
* `managed-premium` - using Azure Premium Storage with managed disks.

If we need to create an addicional storage classe (e.g., unmanaged disks) we can use a sample yaml like this one:

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
     name: azure-disk-standard
provisioner: kubernetes.io/azure-disk
parameters:
  storageaccounttype: Standard_LRS
  kind: Managed
```

An deploy it with

```shell
kubectl create -f sample-storageclass.yaml
```

**Create Persistent Volume**

After deciding on the `Storage Class` let's create a new `Persistent Volume` using another yaml file like this one:

```yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-data
  annotations:
    volume.beta.kubernetes.io/storage-class: default
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 8Gi
```

In this example we are creating a volume named `my-data` with `8Gi` using the storage class `default`.

Let's deploy it with:

```shell
make create-pvc
```

![lab06-05-pvc](./media/lab06-05-pvc.png)

Since in Azure a new disk will be created for this Persistent Volume, it may take a couple of minutes to finish. You can check the status in the Kubernetes console:

![lab06-07-pvc](./media/lab06-07-pvc.png)

...


### Lab 07: AKS Custom VNET

