# API Connect v5 Getting Started: Developing, Testing, and Publishing an API to Bluemix using `apic`

[Shane Claussen](mailto:claussen@us.ibm.com), Chief Architect, API Connect



# Introduction

Welcome to API Connect v5!  One of the most powerful features of API
Connect is the developer toolkit.  The toolkit provides a graphical
user interace named **API Designer** for creating APIs, the **LoopBack
framework** for developing REST applications, a local **unit test
environment** that includes a Micro Gateway for testing APIs, and a
set of **command line tools** for augmenting the development toolset
and assisting devops engineers with continuous integration and
delivery.

This article is aimed primarily at developers who want to better
understand how they'd use the `apic` command line to develop, test,
and publish APIs.  These capabilities are illustrated using a
relatively contrived sample API named Climbing Weather that exposes a
top down REST surface area that proxies to existing APIs provided by
[climbingweather.com](http://climbingweather.com/api).  Although many
of the capabilities described below could be accomplished via a
graphical user experience like API Designer or API Manager this
article takes the CLI approach where possible in an attempt to
illustrate the feature, function, breath, and depth of the `apic`
toolset.



# Get Started

As you read this article you'll want to follow along creating and
testing your own development artifacts using the API Connect developer
toolkit.  To get the toolkit, get a [Bluemix IBM
id](https://bluemix.net), provision [API Connect in Bluemix
(free)](https://new-console.ng.bluemix.net/catalog/services/api-connect),
and then install the [API Connect Developer
Toolkit](https://www.npmjs.com/package/apiconnect).

Next, clone the climbingweather repository:

<pre>
git clone https://github.com/ibm-apiconnect/climbingweather
</pre>



# Steps



1. [Create the API](#step-1---create-the-api)
1. [Test the API](#step-2---test-the-api)
1. [Secure the API](#step-3---secure-the-api)
1. [Package the API](#step-4---package-the-api)
1. [Publish the API](#step-5---publish-the-api)
1. [Configure the developer portal](#step-6---configure-the-developer-portal)
1. [Subscribe to the API](#step-7---subscribe-to-the-api)
1. [Invoke the API](#step-8---invoke-the-api)
1. [Retire the API](#step-9---retire-the-api)



# Step 1 - Create the API

Get started by changing into the climbingweather repository you cloned
from github where you should find a copy of this README.md.  

```
cd climbingweather
```

Let's use the **apic create** command to create our climbing-weather
API definition:

```
apic create --type api --title "Climbing Weather"
```

This command creates climbing-weather.yaml which is a boiler plate
[OpenAPI (Swagger)](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
API definition.  

To move things along, from within your cloned climbingweather
directory, checkout an improved version of the climbing-weather API
that has a prebuilt policy assembly that can proxy to
climbingweather.com's API:

```
git checkout no-client-id -f
```

Although we won't go into great depth on the API definition there are
a couple things to note:

1. The API definition contains an extension property named
   info.x-ibm-name.  The **info.x-ibm-name** and **info.version**
   properties are used to uniquely identify an API.
1. The **host** value has been set to **$(catalog.host)** which is
   replaced automatically by the configured host of the catalog
   the API is publish to.  In the local unit test environment that's
   typically localhost:4001 or localhost:4002.
1. The core IBM extensions to OpenAPI can be found in the
   **x-ibm-configuration** section.  In this section you will find a
   policy flow section named assembly that will define a set of
   policies that will be enforced by the Micro Gateway. This section
   defines how the surface area exposed by the climbing-weather API
   definition is proxied to climbingweather.com's REST APIs.



# Step 2 - Test the API

Now that we have a runnable API definition let's start the Micro
Gateway so we can test the definition's policy assembly.

```
apic start
```

The apic start command returns the service name and the port (if this
is something other then 4001 use that port below).  

Next, use curl to invoke the climbing-weather REST API hosted by the
Micro Gateway which will then proxy the request to
climbingweather.com's REST API:

```
curl -k https://localhost:4001/yosemite
```

Now that the test is complete stop the Micro Gateway using:

```
apic stop
```



# Step 3 - Secure the API

Now that we have an API proxy implementation we need to add security
using OpenAPI [security
definitions](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject)
and
[security requirements](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#security-requirement-object).

Most APIs will add security definitions and requirements for identity,
authentication, and authorization.  In the case of climbing-weather,
to keep it simple, no authentication or authorization will be
required.  However, climbing-weather will add an identity security
requirement requiring application developers to subscribe to
climbing-weather, obtain a client id (aka API key), and pass that
client id via an HTTP header with each climbing-weather API
invocation.  The client id will provide the API provider a mechanism
for managing the API lifecycle and analyzing API invocations.

To add the identity definition and requirement edit the
climbing-weather.yaml API definition and add the following two stanzas
(this can optionally be done using the developer toolkit's API
Designer using `apic edit`):

```
securityDefinitions:
  api-key-1:
    type: apiKey
    description: ''
    in: header
    name: X-IBM-Client-Id
security:
  - api-key-1: []
```

In short, the security definition and requirements sections will now
require application developers to obtain a client identifier and pass
it with each REST invocation via the X-IBM-Client-Id HTTP header.

As part of the developer toolkit, the Micro Gateway allows developers
to mock API subscriptions using a built in client identifier named
**default**.  Let's start the Micro Gateway and invoke the API using
the default client identifier:

```
apic start
```

In a second window issue the following command that should initially fail:

```
curl -i -k https://localhost:4001/yosemite
```

The **401 Unauthorized** error was reported due to the new client
identifier requirement.  Let's now pass the X-IBM-Client-Id header
with the mock client identifer to get passed the 401 error:

```
curl -k https://localhost:4001/yosemite -H 'X-IBM-Client-Id: default'
```

Now that we've successfully tested the security definition and
requirements using the Micro Gateway kill the apic logs command and
stop the Micro Gateway using:

```
apic stop
```

To get a copy of the latest API definition you can use `git checkout
client-id -f` (remember to replace the two occurrences of API_KEY).



# Step 4 - Package the API

In order to publish an API to a catalog it must be packaged into a
product definition.  Products enable product managers to package
multiple APIs into a single product with a set of plans to control
consumption.

To create a product definition for our climbing-weather API:

```
apic create --type product --title "Climbing Weather Product" --apis climbing-weather.yaml
```

Take a look at the generated product definition
(climbing-weather-product.yaml) and note a couple of things:

1. In the **apis** section of climbing-weather-product.yaml references the climbing-weather.yaml API definition.
1. The product has a single **default** plan that provides a rate limit.

To get a copy of the latest API definition you can use `git checkout
product -f` (remember to replace the two occurrences of API_KEY).



# Step 5 - Publish the API



**Catalog Configuration Variable**

Unless the **catalog** configuration variable is set all the commands
that target API Connect catalogs require the --server, --organization,
and --catalog options.  To set the catalog configuration variable and
provide default values for these options:

1. Sign in to the [Bluemix API Connect service](https://new-console.ng.bluemix.net/apis/apiconnect)
1. Select the **link** icon on the Sandbox catalog

You should see a `apic config:set` command like the one below -
execute it in your climbingweather directory:

```
apic config:set catalog=apic-catalog://us.apiconnect.ibmcloud.com/orgs/<bluemix_id>-<bluemix_space>/catalogs/sb
```



**Bluemix Authentication**

All interactions with API Connect clouds require authentication via
the `apic login` command.  Let's login using your bluemix credentials:

```
apic login
Enter your API Connect credentials
? Server: us.apiconnect.ibmcloud.com
? Username: <bluemix_id>
? Password (typing will be hidden) **********
Logged into us.apiconnect.ibmcloud.com successfully
```



**Product Validation**

It's always a good idea to validate your product and its API
definition(s) before you publish:

```
apic validate climbing-weather-product.yaml
```



**Product/API Publish**

Now let's publish the product and API to the Sandbox catalog:

```
apic publish climbing-weather-product.yaml
```

The **products** and **apis** commands contain several actions for
managing products and apis in the catalog.  For example:

```
apic products
apic products:get climbing-weather-product:1.0.0
apic apis
apic apis:get climbing-weather:1.0.0
```

Note that the names returned and used above are the values of the
info.name and info.x-ibm-name and info.version properties of the
product and API definitions respectively.



# Step 6 - Configure the Developer Portal

The published product can be viewed by signing into the [Bluemix API
Connect service](https://new-console.ng.bluemix.net/apis/apiconnect),
opening the **Sandbox** catalog, and select the **Products** tab.

In order for developers to subscribe to the climbing weather product a
one time configuration of your Sandbox developer portal is required.
To configure the developer portal, open the Sandbox catalog in the
[Bluemix API Connect
service](https://new-console.ng.bluemix.net/apis/apiconnect) and:

- Select Settings -> Portal -> IBM Developer Portal -> Save

You will receive an email when the portal is configured.  In the
meantime, **bookmark** the portal URL for the Sandbox catalog.



# Step 7 - Subscribe to the API

Let's now subscribe to the API:

1. **Developer Portal**: Browse to the Sandbox developer portal you
bookmarked in the prior step (it will look something like
https://sb-BLUEMIXID-BLUEMIXSPACE.developer.us.apiconnect.ibmcloud.com):
1. **Create developer account**: Create a developer portal account,
confirm via email, and login to the portal.
1. **Create developer App**: Create an application to store your
client id credentials using the **Apps** tab (and **save your client
id** for use later).
1. **Subscribe**: Select **API Products**, find the climbing weather
product, and subscribe to the default plan.



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



# Step 9 - Retire the API

Once the API has run its lifecycle it can be retired and removed from
the catalog using the following commands:

<pre>
apic products:set --status deprecated
apic products:set --status retired
apic products:delete climbing-weather-product:1.1.0
</pre>



# Summary

I hope you found the sample useful!  Feel free to [contact
me](emailto:claussen@us.ibm.com) to ask questions or provide feedback.

In addition to the content above, I want to call your attention to the
set of commands for working with **LoopBack applications** that will
be covered in a separate article (try `apic loopback -h` in the
meantime).  In addition, many of the development capabilities
described above can be performed in the **API Designer** application,
check it out (`apic edit`).



# Additional Resources

For additional resources pay close attention to the following:

- [API Connect v5 Getting Started: Toolkit Command Line Interface](https://github.com/ibm-apiconnect/cli)
- [API Connect v5 Getting Started: API Products](https://github.com/ibm-apiconnect/product)
- [API Connect Developer Center](https://developer.ibm.com/apiconnect)
- [API Connect v5 Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSMNED_5.0.0/mapfiles/ic_home.html)
- [Follow us @ibmapiconnect](https://twitter.com/ibmapiconnect)
