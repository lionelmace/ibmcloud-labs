## Leverage APIC

### Pre-Requisites

* Install [Node.js](https://nodejs.org) Version compatible with APIC Designer
* Install [API Connect Loopback application]() version 2.1.19

Note: To check the version of API Connect, run the command

  ```npm view apiconnect version```

### In Bluemix Console

1. From the Bluemix [catalog] (http://www.bluemix.net/catalog), create an instance of the service Cloudant DB. Give it a name such as cloudant-db. Note: This database will be used later for the persistence of the data model.

1. From the Bluemix [catalog] (http://www.bluemix.net/catalog), create an instance of the service API Connect. Give it a name such as api-connect.

1. Launch API Manager. In the main page, click the Sandbox catalog. Go to Settings, then Portal. In the Portal Configuration, select IBM Developper portal instead of None. Save.

  Note: The following popup message will appear:
  ```Creating the developer portal for catalog 'Sandbox' may take a few minutes. You will receive an email when the portal is available.```

1. Go back to the Get Started

1. Create an API Connect LoopBack application

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
  ? Property name: **name**
     invoke   loopback:property
  ? Property type: **string**
  ? Required? **Yes**
  ? Default value[leave blank for none]: 

  Let's add another Customer property.
  Enter an empty property name when done.
  ? Property name: age
     invoke   loopback:property
  ? Property type: number
  ? Required? No
  ? Default value[leave blank for none]: 
  ```