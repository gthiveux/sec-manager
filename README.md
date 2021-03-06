# SEC Protocol Manager
The SEC Protocol Manager is part of the "Secure Enterprise Connector" (SEC) built by Fujitsu RunMyProcess. The SEC allows the user to access local resources through a secure, encrypted tunnel from RunMyProcess.

The "SEC Protocol Manager" manages data received from the "Data Connector Agent" and determines which adapter it must forward the information too. The "SEC Protocol Manager" also keeps a registry of all adapters and regularly checks the health of each adapter to ensure it is running smoothly.


As  02/10/2013, the SEC Protocol Manager has only bean tested with jetty 7 standalone. It's basically a java servlet that runs with the help of the SEC-SDK. The pom.xml dependency for the sdk usually point to the current development sdk. If you want a more stable version you should download the latest version of the SEC-SDK and import it in your project. The latest version of the SEC-SDK that can be found in [here](https://github.com/MalcolmHaslam/RunMyProcess-SEC-SDK).


Install and run suggestion
---------------------------
---------------------------

1. Make sure you have [java](http://www.oracle.com/technetwork/java/index.html) and [maven](http://maven.apache.org/) installed on your machine 
2. Download **[jetty](http://www.eclipse.org/jetty/)** standalone (tested on jetty 7.6.11)
3. Download the **sec-manager** project and run mvn clean install to generate the war file with all dependencies

run mvn clean install

	mvn clean install
	
4. copy the generated war file in the jetty webapps folder
5. Create the **Configuration File** (see bellow)
6. run jetty

When jetty is running you can test the Manager by navigating on your browser to the port where jetty is serving the servlet (usually localhost:8080). The following message should appear:
 
  The SEC-Protocol Manager is running! Registered Protocols [protocols registered in the Manager]
  
Configuration File
-------------------

The SEC-Manager expects a config file to exist inside a folder named "configFiles". This folder must be placed on the folder outside of where the Manager is running. For example, on a jetty configuration, it must be placed next to the jetty folder. 

**NOTE** The relative path can be modified in the ProtocolManager.java file.

The config file inside the "configFiles" folder must be named "manager.config" and must follow this structure:

	#ProtocolManager configuration
	pingPort = 4444
	pingFrequency = 10000

Where:

 -**pingPort** is the port where the Manager will wait for Adapters registration information periodically.
 
 -**pingFrequency** is the time, in milliseconds, that the Manager will wait for a ping from the registered Adapters before declaring an adapter idle.

How it Works
-------
-------
As it was mentioned before the Manager is a JAVA Servlet that listens to a port for GETs and POSTs.

When it receives a GET it looks into a "registered Adapters" list and returns a message with all the Adapters currently registered.

When the Manager receives a POST it opens the package and retrieves the name of the intended destination Adapter. It then looks for this Adapter in the "registered Adapters" list and gets all the information about the Adapter's location, availability and communication port. The manager then creates a thread to forwards the information to the Adapter as raw data and wait for a response to send back.

The manager is always listening to the "registration port". This port is a configurable port that listens to pings from the Adapters. When it receives a call it will check if the adapter is already registered and it will registered if no previous registrations were found. If the Manager does not receive a ping in a configurable amount of time, the manager will consider that the adapter is idle or not responding, it will log the problem and flag the Adapter as "idle".

 
