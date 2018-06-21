# Pre-Requisites

## User Accounts

+ You must create an account on IBM Cloud.

+ If you do not have an account yet, follow this link to register, using your IBM id: [https://bluemix.net/registration](https://bluemix.net/registration)

+ You’ll be registered for a 30 days trial account.


## IBM Cloud Command Line Interface (IBM Cloud CLI)

+ This will help you connect to your IBM Cloud account through the command line.

+ You can download it from [here](https://console.bluemix.net/docs/cli/reference/bluemix_cli/download_cli.html)

+ Select the version corresponding to your operating system.


## Container docker

+ Install docker for [Mac](https://docs.docker.com/engine/installation/mac/) or [Windows](https://docs.docker.com/engine/installation/windows/)

+ Install the [Cloud Foundry Containers plug-in](https://console.ng.bluemix.net/docs/containers/container_cli_cfic.html)


## Node and Web Development

+ Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine.
  npm, the package manager for JavaScript is installed with Node.js
  Download the latest stable version 4.X
  [https://nodejs.org/en/download/](https://nodejs.org/en/download/)

+ Nodemon is a utility that will monitor for any changes in your source and automatically restart your server. Perfect for development. To install, get node.js, then from your terminal run:

  ```npm install -g nodemon```

+ Bower, A package manager for the web. Bower is a command line utily.
  Install it with npm:

  ```npm install -g bower```


## Git

+ Git is a distributed version control system where every developers' working copy of the code is also a repo that can contain the full history of all changes.
  [https://git-scm.com/downloads](https://git-scm.com/downloads)


## IDE

There are several modern, open source text editor that understands web design. Select your favorite development environment. Here are some suggestions:

+ Atom [https://atom.io/](https://atom.io)

+ Brackets [http://brackets.io](http://brackets.io)
  Brackets provides many extensions, including a Git plugin.

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ Eclipse Neon
  [http://www.eclipse.org/downloads/packages/release/Neon/1A](http://www.eclipse.org/downloads/packages/release/Neon/1A)

+ Eclipse Tools for IBM Cloud
  [https://marketplace.eclipse.org/content/ibm-eclipse-tools-bluemix](https://marketplace.eclipse.org/content/ibm-eclipse-tools-bluemix)


## Java JDK

+ JDK v1.8
  [http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)


## Optional tools

+ curl, command line tool and library for transferring data with URLs
  [https://curl.haxx.se/](https://curl.haxx.se/)

## Proxy

  If you have an HTTP proxy server on your network between a host running a command lne CLI (e.g. CF) and your Cloud Foundry API endpoint, you must set a proxy variable with the hostname or IP address of the proxy server.

### Cloud Foundy Proxy

  For Cloud Foundry API endpoint, you must set https_proxy with the hostname or IP address of the proxy server.

  ```set https_proxy=http://<your.company.proxy>:8080```

  If the proxy server requires a user name and password, include the credentials:

  ```
  https_proxy=http://username:password@proxy.example.com
  ```

  For more information, go to [https://docs.cloudfoundry.org/cf-cli/http-proxy.html](https://docs.cloudfoundry.org/cf-cli/http-proxy.html)

  To test if your proxy works, run the following command:

  ```cf api https://api.ng.bluemix.net```

### Git Proxy

  ```set http.proxy=http://<your.company.proxy>:8080```

  To set permanently the proxy:
  ```git config --global http.proxy http://<your.company.proxy>:8080```

  To remove it:
  ```git config --global --unset http.proxy```

  To test if your proxy works, run the following command:

  ```git clone https://hub.jazz.net/project/lionelmace/<myapp>```
