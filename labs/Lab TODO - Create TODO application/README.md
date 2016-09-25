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

TODO: Leverage the standard Getting Started:
https://new-console.ng.bluemix.net/docs/containers/container_index.html?pos=2

# -------------------------

# Todo App Tutorial

This is a sample application built with a CLEAN stack (CLoudant NoSQL database, Express, Angular and Node.js).

![Todo](screenshot.png)

This Git tutorial is split in two branches:
  * *[master](https://github.com/lionelmace/node-todo/tree/master)* holds the final application
  * *[tutorial](https://github.com/lionelmace/node-todo/tree/tutorial)* starts with an in-memory
implementation of the Todo App and guide you through the steps of adding a persistent datastore.

For this tutorial, you will need:
  * a IBM Bluemix account,
  * the Cloud Foundry command line tool,
  * a Git client,
  * Node.js.

## Getting ready

1. Create a Bluemix Account

    [Sign up][bluemix_signup_url] for Bluemix, or use an existing account.
    
1. Download and install the [Cloud-foundry CLI][cloud_foundry_url] tool

## Create a new web application

1. Log in to Bluemix console.

1. Select the region where you want to create your application.

1. Go to the Bluemix catalog.

1. Create a new Cloud Foundry (CF) application with the SDK for Node.js under the Compute category.

1. Give your app a unique name and unique host (eg. todo-[your-initials])

1. View your application.

The SDK for Node.js created a simple "Hi there!" web app that will become our starting point.

## Add Git support

Now let's add a source code repository and
an automatic build pipeline to our project.

1. In your application overview page, click the "Add Git" button.

1. Leave the default options selected and continue.

Bluemix DevOps creates a Git repository for your application,
puts in it the starter code for the "Hi there!" application,
and defines a build pipeline so that your app gets automatically
redeployed after every commit.

1. Make note of the Git URL

## Checkout the code locally

1. From the overview page, get the Git repository URL for your app.

1. Clone the repository

  ```
  $ git clone https://hub.jazz.net/git/YOUR_DEVOPS_USERNAME/YOUR_APP_PROJECT
  ```

## Run the app locally

1. Change to the directory of the checkout

1. Get the node.js dependencies for this project

  ```
  $ npm install
  ```

1. Start the app

  ```
  $ npm start
  ```

  The console output will look like:
  
  ```
  > NodejsStarterApp@0.0.1 start /Users/john/dev/todo-[your-initials]
  > node app.js
  
  server starting on http://localhost:6021
  ```

1. Access the app with your web browser

## Change a file locally

1. Open **public/index.html**, modify the welcoming message at line 18

1. Reload the page in your web browser to confirm the change locally

## Push your local change to the cloud

Cloud Foundry relies on the *manifest.yml* file to know what to do when you run the *cf push* command.
A default manifest.yml file was generated for our app. It looks like:

  ```
  applications:
  - path: .
    memory: 256M
    instances: 1
    domain: eu-gb.mybluemix.net
    name: todo-[your-initials]
    host: todo-[your-initials]
    disk_quota: 1024M
  ```

It basically defines one application taking its content from the current directory,
being deployed with **256MB**, with **one** instance, under the **eu-gb.mybluemix.net** domain.
The app is named **node-todo-tutorial** and it is using **node-todo-tutorial** as host name.
It has **1024MB** of disk space available.

1. Log in to Bluemix:

  ```
  $ cf login
  ```

  Note: your API endpoint will be one of:
  * https://api.ng.bluemix.net
  * https://api.eu-gb.bluemix.net
  * or https://api.au-syd.bluemix.net
  
  based on where you decided to create your application.

  Note: you might need to use *cf login --skip-ssl-validation* if your certificate registry is too old.
  
1. Push the app to Bluemix:

  ```
  $ cf push
  ```

1. When the command completes, access the application running
in the cloud to confirm your change was deployed

  ```
  requested state: started
  instances: 1/1
  usage: 256M x 1 instances
  urls: todo-[your-initials].eu-gb.mybluemix.net
  last uploaded: Thu Mar 14 15:24:17 UTC 2016
  stack: cflinuxfs2
  buildpack: SDK for Node.js(TM) (ibm-node.js-4.3.0, buildpack-v3.1-20160222-1123)

       state     since                    cpu    memory          disk          details   
  #0   running   2016-03-14 04:25:24 PM   0.0%   75.9M of 256M   92.5M of 1G      
  ```

Changing files locally and pushing them worked but we can do better.
In a previous step we set up a Git repository and a build pipeline was automatically configured.

## Commit your changes and see them deployed automatically

1. Open **public/index.html**.

1. Change the page title at line 5.

1. Confirm the change works locally.

1. Commit your changes locally

  ```
  $ git commit -a -m "updated title"
  ```

  Note: you might be prompted to configure git for the first time:
  ```
  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"
  ```
  
1. From your application overview in the Bluemix console, open your project page in the Bluemix DevOps console.

1. Click the *Build and Deploy* button to access the build pipeline that was created automatically in a previous step.

1. Push your changes

  ```
  $ git push
  ```

1. Watch how the build pipeline notice your commit and redeploy the application

1. When the command completes, access the application running
in the cloud to confirm your change was deployed

## Get the Todo App starter code

In previous steps, we've seen the basic of modifying code and deploying the application.
Now let's focus on our task to build a Todo App.

A first version of the application has already been developed and is available in this Git repository.
Your first task is to integrate this code in the app you created, replacing the existing app code.

1. Delete all files and folders from your app **except the manifest.yml and .git folder**.

1. Download the Todo App in-memory application from [this archive](https://github.com/lionelmace/node-todo/archive/tutorial.zip) into a temp directory.

1. Unzip the files in a temp directory. It creates a *node-todo-tutorial* folder.

1. Move all files and directories from the extract to your app folder **except the manifest.yml**.

## Run the Todo App locally

1. Get the dependencies for the Todo App. In your app directory, run:

  ```
  $ npm install
  ```
  
1. Run the application

  ```
  $ npm start
  ```

1. Access the local application

1. Add and remove Todos

## Commit the changes

1. Add all new files to Git:

  ```
  $ git add .
  ```
  
1. Commit:

  ```
  $ git commit -a -m "in-memory implementation"
  ```

1. Push to remote Git

  ```
  git push
  ```
  
1. Watch the build pipeline processing your commit and deploying a new version of your app.

## "I lost my Todo list!!!"

Our Todo App is nice but it has a major issue. If we restart the Node.js app,
if we push a new version or if we have multiple instances, we either lose Todos
or we see inconsistencies.
The *in-memory* implementation is a weak implementation we need to fix.

Fortunately Bluemix offers multiple persistent storage options based on our needs.
For this tutorial we will look at how to integrate the IBM Cloudant NoSQL database,
a JSON document oriented store, compatible with CouchDB.

## Review the source code

Before starting to modify the app, let's get familiar with its content:

### Back-end

| File | Description |
| ---- | ----------- |
|[**package.json**](package.json)|Lists the node.js dependencies|
|[**.cfignore**](.cfignore)|List of files and directories ignored when calling **cf push**. Typically we ignore everything that can be retrieved with bower or npm. This speeds up the push process.|
|[**manifest.yml**](manifest.yml)|Used by Cloud Foundry when pushing the application to define the application environment, connected services, number of instances, etc.|
|[**app.js**](app.js)|Web app backend entry point. It initializes the environment and imports the Todo API endpoints|
|[**todos.js**](app/todos.js)|Todo API implementation. It declares endpoints for PUT/GET/DELETE (create/retrieve/delete) and handles the *in-memory* storage.

### Front-end

| File | Description |
| ---- | ----------- |
|[**.bowerrc**](.bowerrc)|Configuration file for the [bower](http://bower.io/) web package manager to put our web dependencies under public/vendor|
|[**bower.json**](bower.json)|Web dependencies (bootstrap, angular)|
|[**index.html**](public/index.html)|Web front-end implementation. It displays the todo list and has a form to submit new todos.|
|[**todo.js**](public/js/todo.js)|Declares the Angular app|
|[**todo.service.js**](public/js/services/todo.service.js)|Implements the connection between the front-end and the back-end. It has methods to create/retrieve/delete Todos|
|[**todo.controller.js**](public/js/controllers/todo.controller.js)|Controls the main view, loading the current todos and adding/removing todos by delegating to the Todo service|

## Create and bind a Cloudant service

1. Back in the Bluemix console, go to your application overview.

1. Add (or connect) a new service to your application

1. Search for *Cloudant* in the catalog

1. Select the **Shared** plan

1. Set the Service name to **todo-cloudant**

1. Click **Create**

  Bluemix will provision a Cloudant service and connect it to your application.

1. Select **Restage** when prompted to do so.

  Your application will restart and the service connection information will be made available to your application.

## Now it's up to you!

In the previous steps:
* you deployed an app in the cloud in few steps;
* you set up a source code repository to collaborate;
* you got a build pipeline to deploy your changes in the cloud
* you updated your app with an in-memory Todo App implementation
* you created a persistent storage, the Cloudant service, that is waiting to be used.

This is where we stop providing you with detailed steps and where you take the lead.

But we give you some tips :)

### How to use Cloudant with Node.js

[Nano](https://github.com/dscape/nano) is a good library to work with Cloudant (or CouchDB).

Install it with:

  ```
  $ npm install --save nano
  ```

### How to retrieve Cloudant credentials when running in the Cloud?

When your application runs in Cloud Foundry, all service information bound
to the application are available in the VCAP_SERVICES variable.

The node.js module **cfenv** simplifies working with the variable.
It is already included in the in-memory application.

The following code would retrieve the credentials for the service
we created before and initialize a Cloudant connection:

  ```
  var cloudant = require('nano')(appEnv.getServiceCreds("todo-cloudant").url).db;
  ```  

### How to Retrieve the Cloudant credentials to develop locally?

Given a Cloud Foundry app relies on the VCAP_SERVICES environment variable,
a straightforward approach is to set this variable in your environment.

1. In the Bluemix console, go in your application dashboard.

1. Under Environment Variables (or Runtime / Environment Variables), look for the value of the VCAP_SERVICES.

1. Copy the value into a text editor

1. Remove all carriage return so that it fits on one line

1. Define an environment variable in your shell or command prompt

  ```
  set VCAP_SERVICES={"cloudantNoSQLDB": [{"name": "todo-cloudant","label": "cloudantNoSQLDB","plan": "Shared","credentials": {"username": "this-is-an-example-bluemix","password": "this-password-is-incorrect","host": "host-bluemix.cloudant.com", "port": 443, "url": "https://this-is-an-example-bluemix:this-password-is-incorrect@host-bluemix.cloudant.com"}}]}
  ```
  
Other options include creating a local env file (JSON or key=value format),
to test for this file in your app and to load the values if found.

### Where is the solution?

If you're stuck and need some help to connect the Cloudant database,
a full working version using Cloudant as persistence can be found
in the [master branch](https://github.com/lionelmace/node-todo) of this repository.

## License

See [License.txt](License.txt) for license information.

## Credits

Based on [scotch-io/node-todo](https://github.com/scotch-io/node-todo)

---

This is a sample application created for the purpose of demonstration and learning
The program is provided as-is with no warranties of any kind, express or implied.

[bluemix_signup_url]: https://console.ng.bluemix.net/?cm_mmc=GitHubReadMe-_-BluemixSampleApp-_-Node-_-Workflow
[cloud_foundry_url]: https://github.com/cloudfoundry/cli/releases

# _________________________



# Resources

For additional resources pay close attention to the following:

- [GitHub Guides](https://guides.github.com/)