![](./images/kubernetes.png)
# Introduction

In this lab, you’ll gain a high level understanding of the Kubernetes architecture, features, and development concepts related to the IBM Container Service. Throughout the lab, you’ll get a chance to use the Command Line Interface (CLI) for creating a Kubernetes cluster, manage your running cluster, and bind a service.


# Pre-Requisites

+ Get a [Bluemix IBM id](https://bluemix.net)
+ Install the [Bluemix CLI](http://clis.ng.bluemix.net)
+ Install docker for [Mac](https://docs.docker.com/engine/installation/mac/) or [Windows](https://docs.docker.com/engine/installation/windows/)
+ Install [Kubectl](https://kubernetes.io/docs/user-guide/prereqs/)


# Steps

1. [Prepare your IBM Container Service](#step-1---prepare-your-ibm-container-service)
1. [Connect to Bluemix](#step-2---connect-to-bluemix)
1. [Create a cluster](#step-3---create-a-cluster)
1. [Deploy Hello World app](#step-4---deploy-hello-world-app)
1. [Bind a Bluemix service to a Kubernetes namespace](#step-5---bind-a-bluemix-service-to-a-kubernetes-namespace)
1. [Weave Scope](#step-6---weave-scope)


## Step 1 - Prepare your IBM Container Service

To create Kubernetes clusters, and manage worker nodes, install the Container Service plug-in.

1. Open a command line utility.

1. Before installing the container plugin, we need to add the repository hosting Bluemix CLI plug-ins.
    ```
    bx plugin repos
    ```
    Output:
    ```
    Listing added plug-in repositories...

    Repo Name   URL
    Bluemix     https://plugins.ng.bluemix.net
    ```

1. If you don't see a repository Bluemix, run the following command:
    ```
    bx plugin repo-add Bluemix https://plugins.ng.bluemix.net
    ```

1. To install the Container Service plugin, run the following command:
    ```
    bx plugin install container-service -r Bluemix
    ```

1. To manage a private image registry, install the Registry plug-in. This plug-in connects to a private image registry Bluemix, where you can store images that can be used to build containers. The prefix for running registry commands is **bx cr**.
    ```
    bx plugin install container-registry -r Bluemix
    ```

1. To verify that the plug-in is installed properly, run the following command:
    ```
    bx plugin list
    ```
    and both plug-ins are displayed in the results:
    ```
    Listing installed plug-ins...

    Plugin Name          Version
    container-registry   0.1.104
    container-service    0.1.219
    ```

## Step 2 - Connect to Bluemix

1. Login to Bluemix
    ```
    $ bx login -a https://api.ng.bluemix.net
    ```

1. Log in to the IBM Bluemix Container Service Kubernetes plug-in. The prefix for running commands by using the IBM Bluemix Container Service plug-in is **bx cs**.
    ```
    bx cs init
    ```

# Step 3 - Create a cluster
To create a cluster, you have two options either a free cluster or a paid cluster.
+ A **free cluster** comes with one worker node to deploy container pods upon. A worker node is the compute host, typically a virtual machine, that your apps run on.
+ A **paid cluster** can have as many worker nodes as you want. A paid cluster requires requires a Bluemix Infrastructure (SoftLayer) account. If you have a Bluemix Infrastructure account, you can skip this first step and go to the step 2 *Set SoftLayer credentials*.

1. Create your free Kubernetes cluster.
    ```
    bx cs cluster-create --name <your-cluster-name>
    ```
    Once the cluster reaches the **deployed** state you can provision pods, but they will be enqueued until the cluster’s pods are finished provisioning. Note that it takes up to 15 minutes for the worker node machine to be ordered and for the cluster to be set up and provisioned.

    If you have already created a free cluster in the step above, skip to the step **Step 4 - Deploy Hello World app**.

1. Set SoftLayer credentials
    ```
    bx cs credentials-set --softlayer-username <YOUR-USER-NAME> --softlayer-api-key <YOUR-API-KEY>
    ```

1. Review the data centers that are available.
    ```
    bx cs locations
    ```
    and you should get back something like:
    ```
    dal10
    ```

1. Review the machine types available in the data center
    ```
    bx cs machine-types <datacenter>
    ```
    and you should get back something like:
    ```
    bx cs machine-types dal10
    Getting machine types list...
    OK
    Machine Types
    Name         Cores   Memory   Network Speed   OS             Storage   Server Type   
    b1c.16x64    16      64GB     1000Mbps        UBUNTU_16_64   100       virtual   
    b1c.32x128   32      128GB    1000Mbps        UBUNTU_16_64   100       virtual   
    b1c.4x16     4       16GB     1000Mbps        UBUNTU_16_64   100       virtual   
    b1c.56x242   56      242GB    1000Mbps        UBUNTU_16_64   100       virtual   
    u1c.2x4      2       4GB      100Mbps         UBUNTU_16_64   100       virtual   
    ```

1. Get the available VLANs in your account
    ```
    bx cs vlans <datacenter>
    ```
    and you should get back something like:
    ```
    Getting VLAN list...
    OK
    ID        Name   Number   Type      Router
    1556821          1860     private   bcr01a.dal10
    1556815          1626     public    fcr01a.dal10
    ```
    Note: When you create a Kube cluster with no vlans in create command, those should get created for you.

1. Create cluster
    ```
    bx cs cluster-create --name <YOUR-CLUSTER-NAME> --datacenter dal10 --workers 3 --machine-type u1c.2x4 --isolation public --public-vlan <ID-PRIVATE-VLAN> --private-vlan <ID-PUBLIC-VLAN>
    ```

1. Verify that the creation of the cluster was requested.
    ```
    bx cs clusters
    ```

1. Check the status of the worker nodes.
    ```
    bx cs workers <cluster_name_or_id>
    ```

1. You will need the kubeconfig data and certs to connect to your cluster using kubectl. You can download the config to your local machine via the CLI. Issue the following CLI command to download your kubeconfig for a given cluster.
    ```
    bx cs cluster-config <cluster_name_or_id>
    ```

1. Use the result of the previous command to set the path to your Kubernetes configuration file as an environment variable.
    For Mac: ```export KUBECONFIG=/Users/ibm/.bluemix/plugins...```
    For Win: ```set KUBECONFIG=/Users/ibm/.bluemix/plugins...````

1. Access your Kubernetes dashboard with the default port 8001.
    ```
    kubectl proxy
    ```

    ```
    $ kubectl proxy
    Starting to serve on 127.0.0.1:8001
    ```

1. Open the following URL in a web browser in order to see the Kubernetes dashboard.
    ```
    http://localhost:8001/ui
    ```

# Step 4 - Deploy Hello World app

1. Log in to the private Container Registry of Bluemix. Only required if you haven't `bx login` before.
    ```
    bx cr login
    ```
    `bx cr login` is a wrapper for `docker login` , it is only needed to log your local docker daemon into the registry, which enables you to push/pull images.

1. If you forgot the namespace for your image registry, run the following command.
    ```
    bx cr namespace-list
    ```

1. Clone or download the source code for the Hello world app to your user home directory.
    ```
    git clone https://github.com/IBM/container-service-getting-started-wt.git
    ```

1. Navigate to the first app directory, Stage1.
    ```
    cd <username_home_directory>/container-service-getting-started-wt/Stage1
    ```

1. Build a Docker image that includes the app files of the Stage1 directory.
    ```
    docker build -t registry.ng.bluemix.net/<namespace>/hello-world:1 .
    ```

    Note: If you already have an image, just need to tag this image before pushing it.
    ```
    docker tag hello-world:1 registry.ng.bluemix.net/<namespace>/hello-world:1
    ```

1. Push the image to your private images registry.
    ```
    docker push registry.ng.bluemix.net/<namespace>/hello-world:1
    ```

1. Verify that the image was successfully added to your registry.
    ```
    bx cr images
    ```
    Output:
    ```
    Listing images...

    REPOSITORY                                  NAMESPACE   TAG       DIGEST         CREATED        SIZE     VULNERABILITY STATUS
    registry.ng.bluemix.net/namespace/hello-world   namespace   1   0d90cb732881   1 minute ago   264 MB   OK
    ```

1. Create a Kubernetes deployment that is named hello-world-deployment to deploy the app to a pod in your cluster.
    ```
    kubectl run hello-world-deployment --image=registry.ng.bluemix.net/<namespace>/hello-world:1
    ```
    Output:
    ```
    deployment "hello-world-deployment" created
    ```

1. Make the app accessible to the world by exposing the deployment as a NodePort service.
    ```
    kubectl expose deployment/hello-world-deployment --type=NodePort --port=8080 --name=hello-world-service
    ```
    Output:
    ```
    service "hello-world-service" exposed
    ```

1. To test your app in a browser, get the details to form the URL.
    ```
    kubectl describe service hello-world-service
    ```
    Output:
    ```
    Name:                   hello-world-service
    Namespace:              default
    Labels:                 run=hello-world-deployment
    Selector:               run=hello-world-deployment
    Type:                   NodePort
    IP:                     10.10.10.8
    Port:                   <unset> 8080/TCP
    NodePort:               <unset> 30872/TCP
    Endpoints:              172.30.171.87:8080
    Session Affinity:       None
    No events.
    ```
    The NodePorts are randomly assigned when they are generated with the expose command, but within 30000-32767. In this example, the NodePort is 30872.

1. Get the public IP address for the worker node in the cluster.
    ```
    bx cs workers <cluster_name_or_id>
    ```
    Output:
    ```
    Listing cluster workers...
    OK
    ID                                            Public IP        Private IP      Machine Type   State      Status
    dal10-pa10c8f571c84d4ac3b52acbf50fd11788-w1   169.47.227.138   10.171.53.188   free           deployed   Deploy Automation Successful
    ```

1. Open a browser and check out the app with the following URL:
    ```
    http://<IP_address>:<NodePort>
    ```
    In this example, the url would be ```http://169.47.227.138:30872```


## Step 5 - Bind a Bluemix service to a Kubernetes namespace

1. See all the available services in the catalog
    ```
    bx service offerings
    ```

1. Create an instance of a service
    ```
    bx service create <service_name> <service_plan> <service_instance_name>
    ```
    Example: ```bx service create cloudantNoSQLDB Lite mycloudantinstance```

1. Verify you created your service
    ```
    bx service list
    ```

1. Find your Kubernetes namespace you will need in the next step.
    ```
    kubectl get namespaces
    ```
    Output:
    ```
    NAME          STATUS    AGE
    default       Active    7d
    ibm-system    Active    7d
    kube-system   Active    7d
    ```

1. Bind your service to your Kubernetes namespace
    ```
    bx cs cluster-service-bind <cluster_id> <kube_namespace> <service_instance_name>
    ```
    Example:
    ```
    bx cs cluster-service-bind ad35aacc139b4e11a6f3182fb13d24af default mycloudant
    ```
    Note: Use the namepsace **default** or create your own namespace.

1. Control that your secret was successfully created
    ```
    kubectl get secrets
    ```


## Step 6 - Weave Scope

Weaveworks scope provides a visual diagram of your resources within the kube cluster including services, pods, containers, processes, nodes, etc. Scope provides you interactive metrics for CPU and Memory and provides tools to tail and exec into a container. Scope is a powerful tool that you do NOT want to expose on the public internet. The following steps describe how to securely deploy scope and access it from a web browser.

1. Deploy weave scope service (privately accessible via cluster IP).
    ```
    kubectl apply -f 'https://cloud.weave.works/launch/k8s/weavescope.yaml'
    ```

1. Open a terminal and run a port forward.
    ```
    kubectl port-forward $(kubectl get pod --selector=weave-scope-component=app -o jsonpath='{.items..metadata.name}') 4040
    ```
    Open your web browser to ```http://localhost:4040```

    Note, weave scope is a cpu heavy (especially the app). I would not recommend running scope on a free cluster. Scope is best utilized in larger paid clusters.


# Resources

For additional resources pay close attention to the following:

- [Running Kubernetes clusters with IBM Bluemix Container Service (Beta)](https://console.ng.bluemix.net/docs/containers/cs_cluster.html#cs_cluster_cli)
- [Container Service Swagger API](https://us-south.containers.bluemix.net/swagger)
- [Bash script to tail Kubernetes logs from multiple pods at the same time](https://github.com/johanhaleby/kubetail)
- [Bluemix CLI Plug-in Repository](http://clis.ng.bluemix.net/ui/repository.html#bluemix-plugins)
