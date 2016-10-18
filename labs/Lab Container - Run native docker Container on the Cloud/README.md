![](./images/container_icon64x64.png)
# Introduction

In this lab, you’ll gain a high level understanding of the architecture, features, and development concepts related to the IBM Containers (IC) service. Throughout the lab, you’ll get a chance to use the Command Line Interface (CLI) for deploying new docker images to Bluemix, manage your running container, and use the API. This lab'll use the open-source [NGINX](https://www.nginx.com/) docker image.


# Objective

In the following lab, you will learn:

+ How to pull a docker image from docker hub and push it to Bluemix
+ How to bind a routable IP address to a running container
+ How to run a container in Bluemix
+ How to use the IBM Containers API


# Pre-Requisites

+ Get a [Bluemix IBM id](https://bluemix.net)
+ Install docker for [Mac](https://docs.docker.com/engine/installation/mac/) or [Windows](https://docs.docker.com/engine/installation/windows/)
+ Install the [Bluemix Command-Line CLI](http://clis.ng.bluemix.net/)


# Steps

1. [Start an existing docker image on Bluemix](#step-1---start-an-existing-docker-image-on-bluemix)
2. [Pull and run a container locally](#step-2---pull-and-run-a-container-locally)
3. [Prepare your IBM Containers service](#step-3---prepare-your-ibm-containers-service)
4. [Attach an IP to your container](#step-4---attach-an-ip-to-your-container)
5. [Use the Container API](#step-5---use-the-container-api)


# Step 1 - Start an existing docker image on Bluemix

In this first step, you will learn how to use the Bluemix Console to manage container following the documentation [Getting started with IBM Containers](https://new-console.ng.bluemix.net/docs/containers/container_index.html)
You could skip this step and directly start Step 2 if you only wanted to interact with the command line.


# Step 2 - Pull and run a container locally

1. Open Terminal

1. Login to the Private Registry
  ```
  bx login
  ```

1. Check that you’re connected
  ```
  bx ic info
  ```

1. Search the NGINX official image from Docker Hub
  ```
  docker search nginx
  ```

1. Retrieve the image NGINX locally on your laptop
  ```
  docker pull nginx
  ```

1. Let's start our NGINX Docker container with this command:
  ```
  docker run -d -p 80:80 --name webserver nginx
  ```
  
  + ```run``` is the command to create a new container
  + The ```-d``` flag to run this container in the background. The container will run in detached mode, meaning the container is started and stays running until stopped but does not listen to the command line.
  + ```-p``` specifies the port we are exposing in the format of -p local-machine-port:internal-container-port. The NGINX image exposes ports 80 and 443 in the container. In this case, we are mapping the container port 80 to port 80 on the Docker host.
  + ```--name``` flag is how we specify the name of the container (if left blank one is assigned for us)
  + ```nginx``` is the name of the image on dockerhub (we downloaded this before with the pull command, but Docker will do this automatically if the image is missing)

1. Run ```docker ps``` to verify that the container was created and is running

1. Show the running server: [http://localhost:80](http://localhost:80); the default NGINX welcome page appears.


# Step 3 - Prepare your IBM Containers service

To run native Docker CLI commands to manage your containers, we will use the ```bx ic```command line.

1. Retrieve your namespace
  ```
  bx ic namespace-get
  ```
  
  Note: A namespace is a unique name to identify your private repository on the Bluemix registry. The namespace is assigned one time for an organization and cannot be changed after it is created. To create a namespace, run the command:
  ```
  bx ic namespace-set <NEW_NAMESPACE>
  ```
  
1. Tag image for Bluemix registry
  ```
  docker tag nginx:latest registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:latest
  ```
 
1. Push ​*NGINX*​ image to Bluemix Public
  ```
  docker push registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:latest
  ```

1. Validate the presence of ​*NGINX*​ image on Bluemix
  ```
  bx ic images
  ```

1. Start the NGINX image on Bluemix
  ```
  bx ic run -d -p 80 --name webserver registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:latest
  ```

  Note: As each container has its own IP, there is no risk of port conflict. Thus, port mapping is not required.

# Step 4 - Attach an IP to your container

1. List running containers on Bluemix. Write down the ID of the running NGINX container.
  ```
  bx ic ps
  ```
  
1. Reqest a routable IP addresse.
  ```
  bx ic ip-request
  ```

1. Bind this IP address with your container
  ```
  bx ic ip bind <IP_ADDRESS> <YOUR_NGINX_CONTAINER_ID>
  ```

1. Show the running container on Bluemix: http://YOUR_IP_ADDRESS:80


# Step 5 - Use the Container API

1. Containers API is available in [Swagger Container API](http://ccsapi-doc.mybluemix.net) 

1. This API requires two HTTP headers. To retrieve those values, run the commands next to each header:

  X-Auth-Token     = ```bx cf oauth-token```
  
  X-Auth-Project-Id= ```bx cf space <SPACE_NAME> --guid```

1. To retrieve a namespace, run the following command with the correct HTTP headers instead of XXXXX
  ```
  curl -X GET -H "X-Auth-Project-Id: XXXXX" -H "Accept: application/json" -H "X-Auth-Token: bearer XXXXX" "https://containers-api.eu-gb.bluemix.net/v3/registry/namespaces"
  ```


# Resources

For additional resources pay close attention to the following:

- [Getting started with IBM Containers](https://new-console.ng.bluemix.net/docs/containers/container_index.html)
- [See Auto-scaling in Action for IBM Containers - YouTube](https://www.youtube.com/watch?v=MFs-pSr2gsw)