
# multihop-bluetooth-tether
Automatically exported from code.google.com/p/multihop-bluetooth-tether

BlueNET consists of a tabbed view with two tabs. The first tab provides with the functionality of application and the 2nd tab displays the current BlueNET’s network topology. 

The first tab consists of two button – Client, Server. It consists of a text view which is used to provide with connection information such as the mobile devices’ mac address, it’s primary ip address, the mac and ip of the device it is connected to and also the devices that are connected to it.

The server button is used to switch on the “Server” mode of the application. Once in the server mode, the application can either work as a Gateway server (if it has a wireless interface with internet) or as an intermediate router/server if it is connected to a gateway server as a client in the first place. The client button is used to put the application to “Client mode”

When the application is started for the first time, the following occurs:
* The application switches on the Bluetooth adaptor.
* Check the wi-fi interface if it is on. If the wi-fi interface is currently in the “on” state, then the mobile device is assumed to be connected to the internet and it can function as the “Gateway server”.
* The acceptThread is started which waits for message exchanges between other mobile devices running the BlueNET. This mainly servers as a service thread that performs services such as informing the client if it is a Gateway server, intermediate server or a client and also establish connections by providing IP addresses if it is a server.
* Depending on the status, “Server” button is either enabled or disabled.
* The application now waits for user interaction.

The user can do the following:
* If the server button is enabled, switch it on to become a server.
* Switch on the client button to search for servers to connect to.
* Use the Options button on the phone to exit the app or make the Bluetooth device discoverable.

When a user clicks the client button:
* The mobile device switches over to client mode.
* The device scans for devices in the vicinity which are part of the “BlueNET” (LETS CALL OUR NETWORK BLUENET :-D).
* The list of devices is shown along with the status of the device (i.e. client/server/gateway server) on the UI from which the user is allowed to select the device he/she wants to connect to.
* Once the user clicks on the device selected, the client contacts the server and requests for an IP address (Each networks should work on a different subnet).
* The server replies by providing an ip address and the gateway address (i.e. the address it is going to use to communicate with this client). 
* The bnep configuration takes place. Once IP address is assigned to the bnep, the client sends a message to the server requesting it to configure its bnep interface.
* The server sets up the interface, NAT rules and port forwarding.
* Once this is done, the client can now browse internet through the Bluetooth connection.
* The “Server” button is also made available to the client device.
* If the user wants to become an intermediate server, he/she can switch that button on.
* When the client button is switched off, it disconnects from the BlueNET by sending a disconnect message to the server.

When a user clicks the server button:
* The IP address table is initialized with addresses in the following range:
10.0.1.1/10.0.1.2 to 10.0.255.1/10.0.255.2
* There are 255 subnets possible. The server now waits for a client to request connection.
* When a connection requests arrives, the server searches the above pool of ip addresses for one which is not currently used. It returns the IP address to the client.
* The client after self configuration, asks the server to configure the bnep interface on its end. The server then uses the 10.0.x.1 address for its bnep and marks that subnet as used.
* When a client wants to disconnect, the server frees the ip address that was used by the client and this subnet is now added back to the address pool.

Messaging protocol:
The messages that are used in the application to communicate between the devices are as follows:
1) IS_SERVER: This message is sent by the client to the devices participating in the BlueNET to query the state of the device.
2) SERVER_GATEWAY: The mobile device running BlueNET responds with this message type for the IS_SERVER request if it is the gateway Server.
3) SERVER_INTERMEDIATE: The mobile device running BlueNET responds with this message type for the IS_SERVER request if it is an Intermediate server. 
4) NOT_SERVER: If the IS_SERVER message is received by a device which is neither Gateway server nor intermediate server, then this message is sent.
5) CONNECT: Once a client decides to connect to a particular server, it sends the CONNECT message. The server upon receiving this request, searches the IP address pool for a free subnet and replies to this message – an IP address and the gateway address.
6) CONNECTED – The client after network configuration, sends this message to the server. The server then sets up the network configuration on its side. This is done because the client has to use pand –connect in order to establish a connection with the server for the bnep to be created on the server side.
7) DISCONNECT – When a client wants to leave the BlueNET it sends the DISCONNECT message to the server. The server will free the IP address subnet and put it back in the address pool.


BlueNET device states:

* NONE: When the application has started and the user has not switched either the server or the client button on, the mobile device is in the NONE state.
* GATEWAY SERVER: The mobile device is a gateway server if it is directly connected to the internet.
* INTERMEDIATE SERVER: The mobile device is an intermediate server if it is a client to another gateway server/intermediate server and it is also accepting devices to connect to it.
* CLIENT: The device only connects to a server (Intermediate/Gateway). It does not allow any other devices to connect to it.

![alt tag](https://github.com/sarun87/multihop-bluetooth-tether/blob/master/HelloTab/screenshots/Multihop-Application_Workflow-black.jpg?raw=true)

