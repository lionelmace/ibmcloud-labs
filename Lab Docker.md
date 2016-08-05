# Pre-Requisites

* Get a [Bluemix IBM id](https://bluemix.net)

* Install docker for [Mac](https://docs.docker.com/engine/installation/mac/) or [Windows](https://docs.docker.com/engine/installation/windows/)

* Install the [Bluemix Command-Line CLI](http://clis.ng.bluemix.net/)


# Steps

1. [Start an existing docker image on Bluemix](#step-1---start-an-existing-docker-image-on-bluemix)
2. [Pull and run a container locally](#step-2---pull-and-run-a-container-locally)
3. [Prepare your IBM Containers service](#step-3---prepare-your-ibm-containers-service)
4. [Attach an IP to your container](#step-4---attach-an-ip-to-your-container)
5. [Use the Container API](#step-5---use-the-container-api)


# Step 1 - Start an existing docker image on Bluemix

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

1. Search the image nginx in docker hub
  ```
  docker search nginx
  ```

1. Retrieve the image nginx locally on your laptop
  ```
  docker pull nginx
  ```

1. Run the nginx image
  ```
  docker run -d -p 80:80 --name webserver nginx
  ```

1. Show the running server: [http://localhost:80](http://localhost:80)


# Step 3 - Prepare your IBM Containers service

1. Retrieve your namespace
  ```
  bx ic namespace-get
  ```
  
  Note: if you haven't created a namespace yet, run:
  ```
  bx ic namespace-set <NEW_NAMESPACE>
  ```
  
1. Tag image for Bluemix registry
  ```
  docker tag nginx:latest registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:latest
  ```
 
1. Push ​*nginx*​ image to Bluemix Public
  ```
  docker push registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:bluemix
  ```

1. Validate the presence of ​*nginx*​ image on Bluemix
  ```
  bx ic images
  ```

1. Start the nginx image on Bluemix
  ```
  bx ic run -d -p 80:80 --name webserver registry.eu-gb.bluemix.net/<YOUR_NAMESPACE>/nginx:latest
  ```

# Step 4 - Attach an IP to your container

1. List running containers on Bluemix. Not the ID of the running NGINX container.
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

1. Containers API is available in [Swagger Container API] [containers_api_url] 

1. This API requires two HTTP headers. To retrieve their values, run the BX commands next to each header:

  X-Auth-Token     = ```bx cf oauth-token```
  
  X-Auth-Project-Id= ```bx cf space <SPACE_NAME> --guid```

1. To retrieve a namespace, run the following command with the correct HTTP headers
  ```
  curl -X GET -H "X-Auth-Project-Id: XXXXX" -H "Accept: application/json" -H "X-Auth-Token: bearer XXXXX" "https://containers-api.eu-gb.bluemix.net/v3/registry/namespaces"
  ````

[containers_api_url]: http://ccsapi-doc.mybluemix.net