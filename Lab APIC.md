## Leverage APIC

### Pre-Requisites

* Install [Node.js](https://nodejs.org) Version compatible with APIC Designer
* Install [API Connect Loopback application]() version 2.1.19

Note: To check the version of API Connect, run the command: ```npm view apiconnect version```

### In Bluemix Console

1. From the Bluemix [Catalog] [bmx_catalog_uk_url], create an instance of the service API Connect. Give it a name such as **api-connect**. Keep the default options.


### In Terminal Window

1. Create an API Connect LoopBack application. Make sure to select the project **notes** which contains a basic working example including a memory DB.

  ```$ apic loopback```

  ```
  ? Please review the license for API Connect available in /usr/local/lib/node_modules/apiconnect/LICENSE.txt and select yes to accept. yese arrow keys)
  ? What's the name of your application? demo
  ? Enter name of the directory to contain the project: demo
  ? What kind of application do you have in mind? notes (A project containing a basic working example, including a memory database)
  ```

1. 
  ```cd demo```

1. 
  ```apic create --type model```

1. 
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

1. Launch API Connect Designer
  ```apic edit```
  
  Note: If the designer started correctly, a webpage will automatically opens and you will see a message as follow in the terminal:
  ```Express server listening on http://127.0.0.1:9000```


### In API Designer (Web Browser)

1. Click **Sign in with Bluemix**. If you're already sign in with Bluemix, you won't be asked your credentials.

1. You should see the API generated earlier in the terminal. We'll get back to that later.

1. Select the tab **Models** and delete only the **Note** model that was generated on our behalf, but make sure to keep the Customer model we created.

1. Go to the tab **Data Sources**. Click on db. In the connector, replace *In-memory db* by *IBM Cloudant DB*.

1. The following message will appear:

  This connector is not currently available, please install it in your project by running
  ```npm install --save loopback-connector-cloudant```


### In Terminal Window

1. Stop the API Designer and run the following command:

  ```npm install --save loopback-connector-cloudant```

1. Re-Launch the API Designer

  ```apic edit```


### In Bluemix Console

1. From the Data Models tab, you should now be able to select the **IBM Cloudant DB** in the list of Connector.

1. We still need a database to persist the data. To do so, we will create an instance of the service Cloudant DB. Go to the Bluemix [Catalog] [bmx_catalog_uk_url], create an instance of the service **Cloudant NoSQL DB**. Give it a name such as **cloudant-db**.

1. Launch the Cloudand Dashboard. A new tab should open automatically with the list of databases. Create a new database with the button on top right corner. Call this dabase **test**. Make sure to use this name as this is expected by the persistence layer of API Connect.

1. Click the tab **Service Credentials**.

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

1. Copy the url, username and password from the credentials into the Data Sources connector of the API Designer.

1. Save the configuration. Saving should display the confirmation message:

  ```
  Success Data source connection test succeeded
  ```

1. Let's test the API in the Designer.

1. First, start the server by clicking the play button in bottom left corner.

1. Click on Explore in the top right corner.

1. Select the operation POST /Customers. Click on Generate hyperlink before the button **Call operation**.

  ```
  {
  "name": "YOUR NAME",
  "id": -30239449.275000483
  }
  ```

1. The first time you will get a CORS error as follows:

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

### Publish API to Bluemix

1. In the API Designer, select the tab APIs. Switch from the Design view to Assemble. In the tree view on the left switch from **Micro Gateway** to **DataPower Gateway Policies**. Save the change.

1. Click on **Publish** in the top right corner. Select **Add and Manage Targets**.

1. Add IBM Bluemix Target. Select the Region such as United Kingdom, then the Space where you created the API connect instance. Finally, select the default catalog **Sandbox**.

1. In the page Select a Bluemix application, type a new application name such as **apic-app**. Click + to add your app in the list. Then Save.

1. Click again on Publish in the top right corner. Select the newly created target.

1. Check the box **Publish Application** and **Stage or Publish products > Stage only**. Click Publish.

1. The publishing operation generates messages in the terminal window where you started APIC. We will need this information to update the APIs in API Designer.

1. In the API Designer, select the tab APIs. Switch from the Design view to Assemble. Select the Invoke  node in the flow diagram. A panel will open on the right.
  Replace the url:
  ```$(runtime-url)$(request.path)$(request.search)```
  by the **API target urls** in the terminal:
  ```apiconnect-e75b4707-af8b-4bc5-97ec-c9a6697fef09.org-ibm-dev.apic.eu-gb.mybluemix.net```

  Copy the **API invoke tls-profile** from the terminal into th panel: ```client:Loopback-client```

1. Save the change.

1. Click on Publish in the top right corner. Select the target.

1.  Do NOT check the box **Publish Application**. Only check **Stage or Publish products > Stage only**. Click Publish.

1. Go to the Bluemix [Dashboard] [bmx_dashboard]. You should see a new Cloud Foundry app called apic-app which is the Loopback app we just published.

1. Open the service instance api-connect. Launch API Manager.

1. In the main page, click the Sandbox catalog.

1. On the Product demo, click the **...** next to the state. In Visible to, select Custom then type **Bluemix** in the text field. Do the same for Subscribable by. Then click Publish.

1. You should get a successfull message **Published**.

1. Click on the tab **Settings**, then select the sub menu Portal. In the Portal Configuration, select IBM Developper portal instead of None. Save.

  The following popup message will appear:
  ```Creating the developer portal for catalog 'Sandbox' may take a few minutes. You will receive an email when the portal is available.```

1. Go to **Explore APIs**. You should see the version 1.O.O of our demo API.

1. If I click on the visible API, No products have been found. WHAT are the next steps to finalize this API and be able to test it from Bluemix??


[bmx_dashboard]:      https://console.eu-gb.bluemix.net/
[bmx_catalog_uk_url]: https://console.eu-gb.bluemix.net/catalog/