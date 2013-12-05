# SEC Protocol Manager
The SEC Protocol Manager is part of the "Secure Enterprise Connector" (SEC) built by Fujitsu RunMyProcess. The SEC allows the user to access local resources through a secure, encrypted tunnel from RunMyProcess.

The "SEC Protocol Manager" manages data received from the "Data Connector Agent" and determines which adapter it must forward the information too. The "SEC Protocol Manager" also keeps a registry of all adapters and regularly checks the health of each adapter to ensure it is running smoothly.


As  02/10/2013, the SEC Protocol Manager has only bean tested with jetty 7 standalone. It's basically a java servlet that runs with the help of the SEC-SDK. The pom.xml dependency for the sdk ussualy point to the current development sdk. If you want a more stable version you should download the latest version of the SEC-SDK and import it in your project. The latest version of the SEC-SDK that can be found in [here](https://github.com/MalcolmHaslam/RunMyProcess-SEC-SDK).


Install and run suggestion
---------------------------
---------------------------

1- Download jetty 7 standalone

2- Download the project and run mvn clean install to generate the war file with all dependencies

3- copy the generated war file in the jetty webapps folder

4- run jetty

When jetty is running you can test the Manager by navigating on your browser to the port where jetty is serving the servlet (usually localhost:8080). The following message should appear:
 
  The SEC-Protocol Manager is running! Registered Protocols [protocols registered in the Manager]
 
How it Works
-------
-------
As it was mentioned before the Manager is a JAVA Servlet that listens to a port for GETs and POSTs.

When it receives a GET it looks into a "registered Adapters" list and returns a message with all the Adapters currently registered.

When the Manager receives a POST it opens the package and retrieves the name of the intended destination Adapter. It then looks for this Adapter in the "registered Adapters" list and gets all the information about the Adapter's location, availability and communication port. The manager then creates a thread to forwards the information to the Adapter as raw data and wait for a response to send back.

The manager is always listening to the "registration port". This port is a configurable port that listens to pings from the Adapters. When it receives a call it will check if the adapter is already registered and it will registered if no previous registrations were found. If the Manager does not receive a ping in a configurable amount of time, the manager will consider that the adapter is idle or not responding, it will log the problem and flag the Adapter as "idle".
 

