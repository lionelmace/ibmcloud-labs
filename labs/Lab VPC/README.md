** Under construction **


# Pre-Requisites

+ Get a [IBM Cloud account](https://bluemix.net)
+ Install the [IBM Cloud CLI](https://console.bluemix.net/docs/cli/reference/ibmcloud/download_cli.html#install_use)


# Steps

1. [1 - Install IBM Cloud Infrasctructure plugin](#1---install-ibm-cloud-infrastructure-plugin)
1. [2 - Connect to IBM Cloud](#2---connect-to-ibm-cloud)
1. [3 - Show Infrastructure and Region commands](#3---show-infrastructure-and-region-commands)
1. [4 - Create VPC](#4---create-vpc)
1. [5 - Create Subnets](#5---create-subnets)
1. [6 - Create a Public Gateway](#6---create-a-public-gateway)
1. [7 - Add key](#7---add-key)
1. [8 - Show images & profiles](#8---show-images-profiles)
1. [9 - Create VMs](#9---create-vms)


# 1 - Install IBM Cloud Infrastructure plugin

To create VPC, and VSI, install the Infrastructure plug-in.

1. Open a command line utility.

1. Before installing the Infrastructure plugin, we need to add the repository hosting IBM Cloud CLI plug-ins.
    ```
    ibmcloud plugin repos
    ```
    Output:
    ```
    Listing added plug-in repositories...

    Repo Name   URL
    Bluemix     https://plugins.ng.bluemix.net
    ```

1. If you don't see a repository, run the following command:
    ```
    ibmcloud plugin repo-add Bluemix https://plugins.ng.bluemix.net
    ```

1. To install the Infrastructure Service plugin, run the following command:
    ```
   ibmcloud plugin install infrastructure-service
    ```

1. To verify that the plug-in is installed properly, run the following command:
    ```
    ibmcloud plugin list
    ```
    and both plug-ins are displayed in the results:
    ```
    Listing installed plug-ins...

    Plugin Name          Version
    infrastructure-service                 0.1.3
    ```

1. To update the plugin
    ```
    ibmcloud plugin update
    ```


# 2 - Connect to IBM Cloud

1. Login to IBM Cloud
    ```
    ibmcloud login
    ```

1. Select the region (API Endpoint) where you deployed your application.

    | Location | Acronym | API Endpoint |
    | ----- | ----------- | ----------- |
    |Germany|eu-de|https://api.eu-de.bluemix.net|
    |Sydney|au-syd|https://api.au-syd.bluemix.net|
    |US East|us-east|https://api.us-east.bluemix.net|
    |US South|us-south|https://api.us-south.bluemix.net|
    |United Kingdom|eu-gb|https://api.eu-gb.bluemix.net|

    >  To switch afterwards to a different region, use the command `ibmcloud target -r eu-de`


 
# 3 - Show Infrastructure and Region commands

1. Get the list of all Infrastructure commands
    ```
    ibmcloud is help
    ```

1. List all regions
    ```
    ibmcloud is regions
    ```

1. List all zones in a region
    ```
    ibmcloud is zones us-south
    ```

1. Get zone info with a region
    ```
    ibmcloud is zone us-south us-south-2
    ```


# 4 - Create VPC

1. List all VPCs in the account
    ```
    ibmcloud is vpcs
    ```

1. Create a VPC
    ```
    ibmcloud is vpc-create <vpc-name>
    ```
    Output:
    ```
    Creating VPC mace-vpc1 in resource group Default under account VPC Demo's Account as user lionel.mace@fr.ibm.com...

    ID        8e6eaa99-b3aa-4ef8-8e12-54967f74f7bb
    Name      mace-vpc1
    Default   no
    Created   6 seconds ago
    Status    available
    Tags      -
    ```

1. Save the VPC id into an environement variable for future use.
    ```
    export vpc=8e6eaa99-b3aa-4ef8-8e12-54967f74f7bb
    ```
 

# 5 - Create Subnets

1. Create a Subnet
    ```
    ibmcloud is subnet-create <subnet-name> $vpc us-south-2 --ipv4_cidr_block 10.1.1.0/24
    ```

1. Save the subnet id into an environement variable for future use.
    ```
    export s1=ca3dd4ee-881e-4286-ae4b-1cddce100042
    ```

1. List of all subnets
    ```
    ibmcloud is subnets
    ```

1. List of all subnets within a VPC (??)
    ```
    ibmcloud is subnets --vpc $vpc
    ```

 
# 6 - Create public gateway

1. Create a Public Gateway
    ```
    ibmcloud is public-gateway-create <gateway-name> $vpc us-south-2
    ```

1. List all Public Gateways
    ```
    ibmcloud is pubgws
    ```

1. Save the gateway id
    ```
    export gateway=c29984d9-a435-4dd5-af6f-f4764a4ac4cc
    ```

1. Update the subnet with the gateway (??)
    ```
    ibmcloud is subnet-update $s1 --gw $gateway
    ```

    ```
    ibmcloud is subnets
    ```
 
# 7 - Add key

1. Create a SSH Key
    ```
    ibmcloud is key-create --name <ssh-key-name> --key "ssh-rsa AAAAB.....vPzQ==" --rg 1
    ```

1. List all keys
    ```
    ibmcloud is keys
    ```

1. Save the key in the env variable
    ```
    export key=8cda4a00-946c-47ad-8044-bbc3bb09fbd1
    ```
 
# 8 - Show images & profiles

1. List OS available
    ```
    ibmcloud is images
    ```

1. Save OS into an env variable
    ```
    export image=cc8debe0-1b30-6e37-2e13-744bfb2a0c11
    ```

1. List Server Profiles
    ```
    ibmcloud is instance-profiles
    ```

1. Save profile into an env variable
    ```
    export profile=c-1x1
    ```
 

# 9 - Create VMs

1. Create a VSI
    ```
    ibmcloud is instance-create <instance-name> $vpc us-south-2 $profile $s1 1000 --image $image --keys $key
    ```

1. List all instances
    ```
    ibmcloud is instances
    ```
 
# Create floating IP
    ```
    ibmcloud is instance 66f31a3d-09d1-44a9-b7fe-e6b716737273
    ```
    ```
    $nic="53f8d4e4-5531-4cf5-8388-157ce65ffac6"
    ```
    ```
    ibmcloud is floating-ip-reserve VIP-P1 --nic $nic
    ```