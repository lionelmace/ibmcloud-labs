![](./images/cloudfoundry.png)

# Introduction

In this lab, you’ll gain a high level understanding of the architecture, features, and development concepts related to the Cloud Foundry runtimes and Bluemix service. Throughout the lab, you’ll create a sample application built with a CLEAN stack (CLoudant NoSQL database, Express, Angular and Node.js).

![Todo](./images/screenshot.png)


# Objective

In the following lab, you will learn:

+ How to deploy a new Cloud Foundry app based on Node.js runtime
+ How to create a new service Cloudant DB to store NoSQL data 
+ How to set up a source code repository to collaborate
+ How to manage Continuous Integration and Deployment
+ How to use the Cloud Foundry Command Line


# Pre-Requisites

+ Get a [Bluemix IBM id](https://bluemix.net), or use an existing account.
+ Install the [Cloud Foundry Command-Line CLI](https://github.com/cloudfoundry/cli/releases)
+ Install a [Git client](https://git-scm.com/downloads)
+ Install [Node.js](https://nodejs.org)


# Steps

1. [Create a new web application](#step-1---create-a-new-web-application)
2. [Add Git support](#step-2---add-git-support)
3. [Checkout the code locally](#step-3---checkout-the-code-locally)
4. [Run the app locally](#step-4---run-the-app-locally)
5. [Change a file locally](#step-5---change-a-file-locally)
6. [Push your local change to the cloud](#step-6---push-your-local-change-to-the-cloud)
7. [Commit your changes and see them deployed automatically](#step-7---commit-your-changes-and-see-them-deployed-automatically)
8. [Get the Todo App code](#step-8---get-the-todo-app-code)
9. [Create and bind a Cloudant service](#step-9---create-and-bind-a-cloudant-service)
10. [Connect the Cloudant DB to the application code](#step-10---Connect-the-cloudant-db-to-the-application-code)
11. [Run the Todo App locally](#step-11---run-the-todo-app-locally)
12. [Commit the changes](#step-12---commit-the-changes)


# Step 1 - Create a new web application

1. Log in to [Bluemix console](https://bluemix.net).

1. Select the Region (e.g. United Kingdom) where you want to create your application.

1. Go to the Bluemix **Catalog**.

1. In the Compute category, select **Cloud Foundry Apps**

1. Create a new app with the ***SDK for Node.js***.

1. Give your app a unique name and unique host (e.g. todo-[your-initials])

1. View your application.

The SDK for Node.js created a simple "Hello World!" web app that will become our starting point.


# Step 2 - Add Git support

Now let's add a source code repository and an automatic build pipeline to our project.

1. In your application **Overview** page, click the **Add Git Repo and Pipeline** button.

1. Leave the default options selected and continue.

  Bluemix DevOps creates a Git repository for your application, puts in it the starter code for the "Hello World!" application, and defines a build pipeline so that your app gets automatically redeployed after every commit.

1. Make note of the Git URL.


# Step 3 - Checkout the code locally

1. From the **Overview** page, get the Git repository URL for your app.

1. Open a terminal or a command prompt to clone the repository

  ```
  $ git clone https://hub.jazz.net/git/YOUR_DEVOPS_USERNAME/YOUR_APP_PROJECT
  ```
  
  Note: If you're being asked for credentials, use your Bluemix Login and Password.

# Step 4 - Run the app locally

1. Change to the directory of the checkout

  ```
  $ cd todo-[your-initials]
  ```

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
  
  server starting on http://localhost:[port-number]
  ```

1. Access the app with your web browser


# Step 5 - Change a file locally

1. Open **public/index.html**, modify the welcome message at line 18

1. Reload the page in your web browser to confirm the change locally

# Step 6 - Push your local change to the cloud

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
The app is named **todo-[your-initials]** and it is using **todo-[your-initials]** as host name.
It has **1024MB** of disk space available.

1. Connect to Bluemix

  ```
  $ cf api <Bluemix_endpoint>
  ```

  Select one of the Bluemix endpoint below based on the region where you created your app.
  * US: https://api.ng.bluemix.net
  * EU: https://api.eu-gb.bluemix.net
  * AU: https://api.au-syd.bluemix.net
  
1. Login to Bluemix

  ```
  $ cf login
  ```

1. Push the app to Bluemix

  ```
  $ cf push
  ```

1. When the command completes, access the application running in the cloud to confirm your change was deployed

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


# Step 7 - Commit your changes and see them deployed automatically

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
  
1. Push your changes

  ```
  $ git push
  ```

1. Back to the Bluemix console, go to your application **Overview**. Click on the **Configure** button to access the **Build pipeline** that was created automatically in a previous step.

1. Watch how the build pipeline notice your commit and redeploy the application

1. When the command completes, access the application running in the cloud to confirm your change was deployed


# Step 8 - Get the Todo App code

In previous steps, we've seen the basic of modifying code and deploying the application.
Now let's focus on our task to build a Todo App. The application has already been developed and is available in this Git repository.

Your first task is to integrate this code in the app you created, replacing the existing app code.

1. Delete all files and folders from your app **except the manifest.yml and .git folder**.

1. Download the complete Todo application from [this archive](./solution/node-todo-master.zip) into a temp directory.

1. Unzip the files in a temp directory. It creates a *node-todo-master* folder.

1. Copy all files and directories from the extract to your app folder.

Note: Make sure the hidden files (.gitignore, .cfignore and .bowerrc) were also copied.


# Step 9 - Create and bind a Cloudant service

In order to store the todo, we will need a persistent storage. To do so, we will use a Cloudant NoSQL database, a JSON document oriented store, compatible with CouchDB.

1. Back to the Bluemix console, go to your application **Overview**.

1. Click **Connect New** to add a service to your application

1. Search for **Cloudant** in the catalog

1. Select the free **Lite** plan

1. Give the service a name such as **todo-cloudant**

1. Click **Create**. Bluemix will provision a Cloudant service and connect it to your application.

1. Select **Restage** when prompted to do so.

  Your application will restart and the service connection information will be made available to your application.
  
  Note: the Cloudant service could have been provionned by running the command line
  ```
  $ cf create-service cloudantNoSQLDB Lite todo-cloudant
  ```


# Step 10 - Connect the Cloudant DB to the application code

When your application runs in Cloud Foundry, all service information bound to the application are available in the **VCAP_SERVICES** variable.

Given a Cloud Foundry app relies on the VCAP_SERVICES environment variable, a straightforward approach is to set this variable in your environment by creating a local env file (JSON or key=value format),
to test for this file in your app and to load the values if found.

1. In the Bluemix console, go to your application **Overview**.

1. Under **Runtime / Environment Variables**, copy the full content of the **VCAP_SERVICES** into the file vcap-local.json of your project. Make sure to copy the content on line 3 below the services element. It should look as follows:

  ```
  {
    "services": {
      "cloudantNoSQLDB": [
        {
          "credentials": {
              "username": "XXXX",
              "password": "XXXX",
              "host": "XXXXXX-bluemix.cloudant.com",
              "port": 443,
              "url": "https://....-bluemix.cloudant.com"
          },
          "name": "todo-cloudant",
          "label": "cloudantNoSQLDB",
          "plan": "Lite",
          ...
        }
      ]
    }
  }
  ```


# Step 11 - Run the Todo App locally

1. Get the dependencies for the Todo App. In your app directory, run:

  ```
  $ npm install
  ```
  
1. Run the application

  ```
  $ npm start
  ```

1. Access the local application


# Step 12 - Commit the changes

1. Add all new files to Git:

  ```
  $ git add .
  ```
  
1. Commit:

  ```
  $ git commit -a -m "full solution"
  ```

1. Push to remote Git

  ```
  git push
  ```
  
1. Watch the build pipeline processing your commit and deploying a new version of your app.

Congratulations! You completed this lab. You can get familiar with the application code content.

## Source code

### Back-end

| File | Description |
| ---- | ----------- |
|**package.json**|Lists the node.js dependencies|
|**.cfignore**|List of files and directories ignored when calling **cf push**. Typically we ignore everything that can be retrieved with bower or npm. This speeds up the push process.|
|**manifest.yml**|Used by Cloud Foundry when pushing the application to define the application environment, connected services, number of instances, etc.|
|**app.js**|Web app backend entry point. It initializes the environment and imports the Todo API endpoints|
|**todos.js**|Todo API implementation. It declares endpoints for PUT/GET/DELETE (create/retrieve/delete) and handles the *in-memory* storage.

### Front-end

| File | Description |
| ---- | ----------- |
|**.bowerrc**|Configuration file for the [bower](http://bower.io/) web package manager to put our web dependencies under public/vendor|
|**bower.json**|Web dependencies (bootstrap, angular)|
|**index.html**|Web front-end implementation. It displays the todo list and has a form to submit new todos.|
|**todo.js**|Declares the Angular app|
|**todo.service.js**|Implements the connection between the front-end and the back-end. It has methods to create/retrieve/delete Todos|
|**todo.controller.js**|Controls the main view, loading the current todos and adding/removing todos by delegating to the Todo service|


# Resources

For additional resources pay close attention to the following:

- [GitHub Guides](https://guides.github.com/)


## Credits

Based on [scotch-io/node-todo](https://github.com/scotch-io/node-todo)