![](./images/iotp_icon_64.png)

# Introduction

This tutorial demonstrates how to connect a simulated device to the Watson IoT platform, vizualise its live data, store its data into a database and leverage [Node-RED](http://www.nodered.org) tool for wiring together hardware devices, APIs and online services.

  ![Sample Architecture](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/iot-architecture.png)


# Objectives
* You will collect sensor data from a Watson IOT service.
* You will simulate a temperature sensor.
* You will discover how to leverage Node-RED to connect IoT.
* You will store the collected data into a NoSQL database.
* You will use Watson to translate messages.


# Pre-Requisites
* A [Bluemix](http://www.bluemix.net) account


# Start the simulated device

We will use a simulator of a temperature sensor. This sensor also simulates Humidity and Object Temperature.<br />
This way, we don't require an actual hardware device to test our application.

1. In a new browser window or on a smartphone, browse to [IOT Sensor](http://quickstart.internetofthings.ibmcloud.com/iotsensor).
<br />Alternatively, enter this short URL: http://ibm.biz/iotsensor

    ![Sensor simulator](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/smarphone-iotsensor.png)

1. Note the Device Id (displayed in the top right corner).


# View the live sensor data

1. In a new browser window, browse to [Watson IOT Platform quickstart](https://quickstart.internetofthings.ibmcloud.com).
<br />Alternatively, enter this short URL: http://ibm.biz/iotquickstart

    ![IOT Quickstart](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/iot-quickstart.png)
    
1. Enter the device id. 

1. Vizualise the live sensor data. 


# Connect your device to the Watson IOT Platform

You've seen my data, what next? Now you will use your device in an application created with IBM Bluemix.

1. Create an app using **Internet of Things Foundation Starter** from the Boilerplates category in the Catalog.

    ![](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/boilerplate-iotstarter.png)

1. Provide the application name, modify the host name, if required, and click **Create**.
<br /> *Note: Wait for a few minutes for your app to start running.*

1. When your app is running, select the app URL or type it into the browser to open the **Node-RED flow editor**

    ```
    https://<appname>.mybluemix.net
    ```

1. You see a ready-made flow that can process temperature readings from a simulated device.

    ![](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/nodered-defaultflow.png)

# Use Node-RED to read the sensor data

1. In the Node-RED workspace, double-click the **IBM IoT App In** node to open the configuration dialog.

    ![IOT App IN node](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/iot-appnode.png)

1. In the Authentication type field, select **Quickstart** from the pull-down list. Enter the Device ID field and click OK.
<br />*Make sure that the device id is entered in lowercase, and that there are no leading or trailing space characters.*

1. Look for the **Deploy** button in the upper right hand corner of your Node-RED workspace. The deploy button is now red; click it to deploy your flow.

    ![Node-RED Deploy](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/nodered-deploy.png)
 
1. Open the debug pane on the right. You will see that the flow is generating Temperature Status messages.

1. Increase the temperature value on the simulator to see the messages change in the debug pane. 
<br /> *Note that a different message appears if the temperature exceeds 40 degrees.*

# Store the device data into a No SQL database

1. In Node-RED flow editor, add a **Cloudant out** node 

    ![Cloudant out node](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/nodered-cloudant.png)

1. In the Service type field, select the name of Cloudant service bound to Node.js runtime from the pull-down list.
<br />Enter a dabatase name in lowercase. Keep the default operation insert and finally give a name to the node.

  ![Cloudant configuration](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/nodered-cloudantconfig.png)

1. Deploy the flow. Return to the Bluemix console, go to the Cloudant console and navigate into the records.

  ![Cloudant console](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/cloudant-console.png)

# Translate messages with Watson.

The warning messages generated in Node-RED uses English by default. You may want to translate those messages into your oww language.

1. In Bluemix console, bind a new service **Language Translation** to your app.

1. In Node-RED flow editor, add a new **Language Translation** node to the flow.

1. Modify the flow accordingly to translate those messages.

    ![Watson Language Translation](/lionelmace/bluemix-pot/raw/master/labs/Lab%203%20-%20Connect%20your%20Devices%20with%20IOT%20Platform/images/nodered-translationflow.png)

1. Deploy the updated flow. 

1. Observe the translated output based on the selected language.

