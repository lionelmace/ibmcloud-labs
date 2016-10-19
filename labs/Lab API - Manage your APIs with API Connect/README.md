![](./images/apic_64x64.png)
# Introduction (IN PROGRESS...)

In this lab, you’ll gain a high level understanding of the architecture, features, and development concepts related to the IBM API Connect (APIC) solution. Throughout the lab, you’ll get a chance to use the APIC command line interface for creating LoopBack applications, the intuitive Web-based user interface, and explore the various aspects associated with solution’s configuration of RESTful based services as well as their operation.


# Objective

In the following lab, you will learn:

+ How to create a simple LoopBack application
+ How to create a Representational State Transfer (REST) API definition
+ How to test a REST API
+ How to publish an API to Bluemix


# Pre-Requisites

+ Get a [Bluemix IBM id](https://bluemix.net)
+ Install [Node.js](https://nodejs.org)
+ Install [API Connect Developer Toolkit](https://www.npmjs.com/package/apiconnect)

Note:
To check all available versions of API Connect: ```npm view apiconnect version```
To check the local version of API Connect: ```apic -v```


# Steps

1. [Provision API Connect in Bluemix](#step-1---provision-api-connect-in-bluemix)
2. [Create a LoopBack application](#step-2---create-a-loopback-application)
3. [Manage your API in API Designer](#step-3---manage-your-api-in-api-designer)
4. [Manage the data persistence](#step-4---manage-the-data-persistence)
5. [Test your API](#step-5---test-your-api)
6. [Publish your API to Bluemix](#step-6---publish-your-api-to-bluemix)


# Step 1 - Provision  API Connect in Bluemix

From the Bluemix [Catalog] [bmx_catalog_uk_url], provision an instance of the service **API Connect**.


# Step 2 - Create a LoopBack application

API Connect comes with a developer toolkit. This toolkit provides a offline graphical user interace named API Designer for creating APIs, the LoopBack framework for developing REST applications, a local unit test environment that includes a Micro Gateway for testing APIs, and a set of command line tools for augmenting the development toolset and assisting devops engineers with continuous integration and delivery.

1. Get help on the **apic** command set:
  ```
  apic -h 
  ```

1. Create an API Connect LoopBack application. Make sure to select the project **notes** which contains a basic working example including a memory DB.

  ```$ apic loopback```

  ```
  ? Please review the license for API Connect available in /usr/local/lib/node_modules/apiconnect/LICENSE.txt and select yes to accept. yese arrow keys)
  ? What's the name of your application? demo
  ? Enter name of the directory to contain the project: demo
  ? What kind of application do you have in mind? notes (A project containing a basic working example, including a memory database)
  ```

1. Change into the LoopBack application directory

  ```cd demo```

1. Create a data model in your app

  ```apic create --type model```
 
  ```
  ? Enter the model name: Customer
  ? Select the data-source to attach Customer to: db (memory)
  ? Select model's base class PersistedModel
  ? Expose Customer via the REST API? Yes
  ? Custom plural form (used to build REST URL): Customers
  ? Common model or server only? common
  Let's add some Customer properties now.

  Enter an empty property name when done.
  ? Property name: name
     invoke   loopback:property
  ? Property type: string
  ? Required? Yes
  ? Default value[leave blank for none]: 

  Let's add another Customer property.
  Enter an empty property name when done.
  ? Property name: age
     invoke   loopback:property
  ? Property type: number
  ? Required? No
  ? Default value[leave blank for none]: 
  ```


# Step 3 - Manage your API in API Designer

1. Launch API Connect Designer
  ```apic edit```
  
  If the designer started correctly, a webpage will automatically opens and the terminal will show a message similar to this one:
  ```Express server listening on http://127.0.0.1:9000```
  
1. Click **Sign in with Bluemix**. If you're already sign in with Bluemix, you'll be automatically signed into the designer.

1. The designer opens into the APIs section showing the API definition we created from the command line.

1. Select the tab **Models** and delete only the **Note** model that was generated on our behalf, but make sure to keep the Customer model we created.

1. Open the Customer model. You should see the attributes age and name and their types. Note that name is marked as required as specified at the creation.


# Step 4 - Manage the data persistence

1. In the API Designer, go to the tab **Data Sources**. Click on db. In the Connector section, select *IBM Cloudant DB* instead of *In-memory db*.

1. The following message will appear:

  This connector is not currently available, please install it in your project by running
  ```npm install --save loopback-connector-cloudant```

1. Stop the API Designer and run the npm command above.

1. Re-Launch the API Designer

  ```apic edit```

1. Go back to the Data Models tab, you should now be able to select the **IBM Cloudant DB** in the list of Connector.

1. We still need a database to persist the data. To do so, we will create an instance of the service Cloudant DB. Go to the Bluemix [Catalog] [bmx_catalog_uk_url], create an instance of the service **Cloudant NoSQL DB**. Give it a name such as **cloudant-db**.

1. Launch the Cloudand Dashboard. A new tab should open automatically with the list of databases. Create a new database with the button on top right corner. Call this dabase **test**. Make sure to use this name as this is expected by the persistence layer of API Connect.

1. Go back to Bluemix console and click the tab **Service Credentials**.

  ```
  {
    "credentials": {
        "username": "XXXXXX",
        "password": "XXXXXX",
        "host": "f9246334-58d1-4a97-8bde-34c30121f063-bluemix.cloudant.com",
        "port": 443,
        "url": "https://USERNAME:PASSWORD@f9246334-58d1-4a97-8bde-34c30121f063-bluemix.cloudant.com"
    }
  }
  ```

1. Copy the url, username and password from the credentials into the Data Sources connector of the API Designer. Specify the database name **test*. If none is specified, API Designer will use test by default.

1. Save the configuration. Saving should display the confirmation message:

  ```
  Success Data source connection test succeeded
  ```

# Step 5 - Test your API

1. Let's test the API in the Designer. First, start the server by clicking the play button in bottom left corner. Once the server is started, you should see the endpoint of the Local Micro Gateway.

1. Click on Explore in the top right corner.

1. Select the operation POST /Customers. Click on *Generate* hyperlink before the button **Call operation** in the right panel.

  ```
  {
  "name": "YOUR NAME",
  "id": -30239449.275000483
  }
  ```

1. The first time you will most likely get a CORS error as follows:

  ```
  No response received. Causes include a lack of CORS support on the target server, the server being unavailable, or an untrusted certificate being encountered.
  Clicking the link below will open the server in a new tab. If the browser displays a certificate issue, you may choose to accept it and return here to test again.
  https://$(catalog.host)/api/Customers
  ```

1. Open the url below in a new tab of your browser:

  https://localhost:4002/api/Customers
  
1. Click on Advanced. Accept the exception.

1. Go back to the Explore view in API Designer and click **Call operation** again. You should get a successful response code 200 OK.

1. If you have kept the Cloudant DB dahsboard open, you can select the database **test** and view the newly created record.

1. Congratulations you successfullly tested your API.


# Step 6 - Publish your API to Bluemix

1. In the API Designer, select the tab APIs. Switch from the Design view to Assemble. In the left hand side panel, switch from **Micro Gateway policies** to **DataPower Gateway policies**. Save the change.

1. Click on **Publish** in the top right corner. Select **Add and Manage Targets**.

1. Add IBM Bluemix Target. Select the Region such as United Kingdom where you created the API connect instance, then the Organization (Space). Finally, select the default catalog **Sandbox**.

1. In the page Select a Bluemix application, type a new application name such as **apic-app**. Click + to add your app in the list. Then Save.

1. Click again on Publish in the top right corner. Select the newly created target.

1. Check the box **Publish Application** and **Stage or Publish products > Stage only**. Click Publish.

1. The publishing operation generates messages in the terminal window where you started APIC. We will need this information to update the APIs in API Designer.

1. In the API Designer, select the tab APIs. Switch from the Design view to Assemble. Select the Invoke  node in the flow diagram. A panel will open on the right.
  Replace the url:
  ```$(runtime-url)$(request.path)$(request.search)```
  by the **API target urls** in the terminal:
  ```apiconnect-e75b4707-af8b-4bc5-97ec-c9a6697fef09.org-ibm-dev.apic.eu-gb.mybluemix.net$(request.path)$(request.search)```

  Copy the **API invoke tls-profile** from the terminal into th panel: ```client:Loopback-client```

1. Save the change.

1. Click on Publish in the top right corner. Select the target.

1.  Do NOT check the box **Publish Application**. Only check **Stage or Publish products > Stage only**. Click Publish.

1. Go to the Bluemix [Dashboard] [bmx_dashboard_url]. You should see a new Cloud Foundry app called apic-app which is the Loopback app we just published.

1. Open the service instance api-connect. Launch API Manager.

1. In the main page, click the Sandbox catalog.

1. On the Product demo, click the **...** next to the state. In Visible to, select Custom then type **org-apic-demo** in the text field. Do the same for Subscribable by. Then click Publish. 

1. TODO: On the Product demo, click the **...** next to the state. In Visible to, select Custom then type **Bluemix** in the text field. Do the same for Subscribable by. Then click Publish.

1. You should get a successfull message **Published**.

1. Click on the tab **Settings**, then select the sub menu Portal. In the Portal Configuration, select IBM Developper portal instead of None. Save.

  The following popup message will appear:
  ```Creating the developer portal for catalog 'Sandbox' may take a few minutes. You will receive an email when the portal is available.```

1. Go to **Explore APIs**. You should see the version 1.O.O of our demo API.

# Step 6 - Configure the developper portal (TO be updated)

The published product can be viewed by signing into the [Bluemix API Connect service](https://console.ng.bluemix.net/apis/apiconnect), opening the **Sandbox** catalog, and select the **Products** tab.

In order for developers to subscribe to the climbing weather product a one time configuration of your Sandbox developer portal is required.
To configure the developer portal, open the Sandbox catalog in the [Bluemix API Connect
service](https://console.ng.bluemix.net/apis/apiconnect) and:

- Select Settings -> Portal -> IBM Developer Portal -> Save

You will receive an email when the portal is configured.  In the meantime, **bookmark** the portal URL for the Sandbox catalog.

# Step 7 - Subscribe to the API

Let's now subscribe to the API:

1. **Developer Portal**: Browse to the Sandbox developer portal you bookmarked in the prior step (it will look something like https://sb-BLUEMIXID-BLUEMIXSPACE.developer.us.apiconnect.ibmcloud.com):
1. **Create developer account**: Create a developer portal account, confirm via email, and login to the portal.
1. **Create developer App**: Create an application to store your client id credentials using the **Apps** tab (and **save your client id** for use later).
1. **Subscribe**: Select **API Products**, find the climbing weather product, and subscribe to the default plan.

# Step 8 - Invoke the API

Now that you have subscribed you can invoke the API using your client
id.  To determine the invocation URL to use:

1. Sign in to the Sandbox developer portal bookmarked above
1. Select **API Products** tab
1. Select the **Climbing Weather Product**
1. Scroll down to the **GET /yosemite** operation
1. Click on the grey operation area to expand the details for the **GET /yosemite** operation
1. On the right hand side you'll see the curl expression
1. Copy it into your terminal window replacing **REPLACE_THIS_KEY** with your client id saved from the prior step

You can also use the following as long as you replace BLUEMIXID, BLUEMIXSPACE, and CLIENT_ID:

```
curl -k https://api.us.apiconnect.ibmcloud.com/BLUEMIXID-BLUEMIXSPACE/sb/yosemite -H 'X-IBM-Client-Id: CLIENT_ID'
```


### Active Organization Portal

1. Go to Sandbox. Select the **Developers** tab. Click the button **Add Organization**.

1. Name you organization such as **org-apic-demo**. Add a new user such as your personal email address.

1. You will receive an activation link in a e-mail. A new web page will open to set your user id credentials (password and names). Do so and Activate.

1. The API Designer portal will open. Login with the credentials previously created.

1. Select the tab **Apps**.

1. Click **Register New Application**

1. Enter application name such as MyApp.

1. You will get a client secret. Copy it temporarily. 

1. In the Subscriptions section, click on Available APIs.

1. Select the demo API. Click on Subscribe. Click on Select an application to sign up to this plan.

1. You should get a message "Successfully subscribed to this plan."

1. Save the changes.

1. Click on demo in the tree panel on the left. It should open the explore to test your api.

1. ISSUE: I get a internal server error when calling Post Customers.


# Analytics

1. TODO: add analytics


# Additional Resources

For additional resources pay close attention to the following:

- [API Connect v5 Getting Started: Toolkit Command Line Interface](https://github.com/ibm-apiconnect/cli)
- [API Connect v5 Getting Started: API Products](https://github.com/ibm-apiconnect/product)
- [API Connect Developer Center](https://developer.ibm.com/apiconnect)
- [API Connect v5 Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSMNED_5.0.0/mapfiles/ic_home.html)
- [Follow us @ibmapiconnect](https://twitter.com/ibmapiconnect)


[bmx_dashboard_url]:  https://console.eu-gb.bluemix.net/
[bmx_catalog_uk_url]: https://console.eu-gb.bluemix.net/catalog/