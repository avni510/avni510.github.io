--- 
layout: post
title:  Introduction to Networking
date:   2017-04-03 11:20:00 -0600
categories: Server
---
<!--Socket
  * one end point of a two-way communication
  * combination of IP address and a port
  * TCP connection is defined as two sockets
  * used to send data to one computer's software
  * socket is bound to a port number
  * server listens to socket for a client to make a connection request
  * on client-side - the socket is used to communicate with the server

To make a client connection request to server
* knows server's host address and port number
* server accepts connection
* it creates a new socket when connection with client is accepted so it can continue to listen to other requests for other clients 

communicate with server and client occurs through the socket
-->

I had the opporuntunity to read the first chapter of [Java Network Programming](http://it.guldstadsgymnasiet.se/java/Java%20Network%20Programming,%204th%20Edition.pdf) by Elliotte Rusty Harold. The first chapter covers networking basics, and this post will summarize many of the concepts covered in that chapter. 

To start off, here's some basic vocabulary about networking:

* Network - collection of computers and other devices that can transmit data to each other 
* Node - any machine on a network (can be a computer, router, etc.)
* Host - any node is that a general purpose computer
* Address - a unique sequence of bytes that identifies a node
* Protocol - defines a set of rules for communication

At a high level, the client makes a request that request is converted into packets of data which is are sent through wires to a server. The server then responds and sends packets of data back to the client. 

The internet is a very large network. There are 4 main layers of the internet protocol suite. Each layer abstracts information from the layer on top. Therefore, each layer contains specific information on a part of how the internet works. The layers are listed from deepest (hardware) to shallowist (interatctions with an application). 

#### Host to Network Layer
This layer specifies how IP datagrams are sent over a physical connection. For example, through fiber optic cables, radio waves, etc. This layer is heavily concerned with the hardware that comes with data transmission. 

#### Internet Layer
The Internet Layer implements the Internet Protocol (IP) which defines how bits and bytes are organized into packets and identifies how different hosts can find one another on the network.Each node in the network has an IP address which is a four-byte number typically written like this 199.1.32.90. Each packet contains the destination IP address and the source IP address. This layer also allows different types of Hosts to Network layers (ex: Wifi, ethernet, etc.) to communicate with each other.

#### Transport Layer
There is no guarantee that the packets that are sent will be delievered and if they are delievered they could be corrupted. Packets could also arrive in a different order from how they were sent because there are multiple routes to get to the same host. For example, if we send packet A before packet B, packet B could arrive before packet A. Two different types of protocols can be implemented within this layer (these are the most common). The first is the Transmission Control Protcol (TCP) which asks for a retransmission of data if it's lost or corrupted and will guarentee the specific order in which the order of the bytes were sent. The second is the User Datagram Protocol (UDP) which will allow the receiver of the data to dectect if there are corrupted packets but this protocol does not guarantee that packets are delievered in the order in which they were sent, if at all. Why would you use UDP over TCP? It tends to be much faster, and sometimes speed is more crucial than getting every packet back. 

#### Application Layer
This is the layer that directly communicates with an application if a request is made by the client. It is in charge of delievering data to the user. And it decides what to do with bits and bytes of data after its been transferred. For example, rendering text instead of a string of binary. 

So now we have covered the basics of how data transfers from the client to the server. But there's a bit more on initially creating a connection with a server. 

#### Ports
Computers are able to do mulitple things at once and we use ports to handle this. A port is not a physical construct. Conceptually, a port can be allocated to a specific service. And data is usually sent to a specific port on the receiving machine. If you are using multiple applications that access the internet ex: chrome, skype, etc. Each application will have their own dedicated port. The server and the client are connected to a port on each respective machine. 

#### Sockets 
A socket is one end point of a two way communication (between the server and client). It is comprised of a port and an IP address (which is a unqiue pair, you can have multiple connections with your host and server, but they can be on different ports). They are used to send data back and forth. A TCP connection is defined as two sockets. On the server side there is a socket that is listening for a connection request. 

Here are the steps that occur when a client requests a connection to a server
1. The client must know what host address and port number to make a request to
2. The server accepts the connection
3. A new socket is created by the server when the connection with the client is accepted so it can continue to listen to other requests for other clients. 


This covers the very basics of networks. Java is specifically designed for network applications, and it is what I will be using to build my HTTP Server. 
