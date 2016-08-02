# Pre-Requisites

* Get a [Bluemix IBM id](https://bluemix.net)

* Install docker for [Mac](https://docs.docker.com/engine/installation/mac/) or [Windows](https://docs.docker.com/engine/installation/windows/)

* Install the [Bluemix Command-Line](http://clis.ng.bluemix.net/)

# Start an existing docker image on Bluemix

# Pull and Push a NGINX container to Bluemix

1. Open Terminal

1. Login to the Private Registry
  ```
  bx ic login
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

1. Retrieve your namespace
  ```
  bx ic namespace-get
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

1. Create a volume (Optional)
  ```
  bx ic volume-create NAME
  ```

1. Containers API is available in [Swagger Container API] [containers_api_url] 
  
  X-Auth-Token     = ```cf oauth-token```
  
  X-Auth-Project-Id= ```cf space <space-name> --guid```

Sample to retrieve a namespace:
curl -X GET -H "X-Auth-Project-Id: xxxx" -H "Accept: application/json" -H "X-Auth-Token: bearer xxxxx" "https://containers-api.eu-gb.bluemix.net/v3/registry/namespaces"
  
[containers_api_url]: http://ccsapi-doc.mybluemix.net