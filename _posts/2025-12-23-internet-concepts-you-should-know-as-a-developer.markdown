---
layout: post
title:  "Internet Concepts you should know as a developer"
date:   2025-12-23 20:47:01 +1100
---

## How does the internet work?

The internet is an interconnection of devices and computer systems that are connected through a set of standardized protocols such as IP, and TCP. At the core of the internet, routers are responsible for transferring the data. Data is broken down into smaller chunks called packets, and these packets are first examined by the router, and then transferred from one router to the next, until it arrives at its destination. Routers are responsible for determining the path through which the packets travel. 

IP (Internet Protocol) ensures that the packets arrive at the right destination, and TCP (Transmission Control Protocol) ensures that packets are transmitted reliably, and in correct order. 

## What is a domain name?

A domain name is used to identify a website on the internet. It is a human readable name. 

## What is DNS?

DNS is the Domain Name System responsible for converting a domain name into an IP address.
When you type, let us say, “google.com”, your browser sends a DNS query to a DNS server, which then returns the corresponding IP address, if found. This IP address is used by your browser to create connections with the website/server. 

## What is an IP address?

An IP address is a unique number used to identify each device on a network, and to route data to the correct destination. 

## What is HTTP?

HTTP (Hyper Text Transfer Protocol) is a protocol used to transfer data between a client and a server. Example: from a browser to a website, or vice versa. 

## What is HTTPS?

HTTPS (Hyper Text Transfer Protocol Secure) is a protocol used to provide secure communication between a client and a server. It is an encrypted version of HTTP.

Using a SSL/TLS certificate makes a website HTTPS(secure). This provides encryption; all the data between a browser (client) and server is scrambled. Similarly, authentication is provided; the website/server proves that its authenticity, in other words, it is who it is claiming to be. 

TLS (Transport Layer Security) is a modern and upgraded version of SSL (Secure Sockets Layer) that fixes many vulnerabilities of SSL. 

## Difference between TCP and UDP:

| **TCP** (Transmission Control Protocol) | **UDP** (User Datagram Protocol) |
|-----------------------------------------|----------------------------------|
| TCP ensures a connection is established with the listener before sending a packet. | UDP does not establish a connection with the listener before sending a packet. |
| Ensures packets arrive in the correct order and retransmits lost packets. | Does not care if some packets do not arrive in the correct order, or if some packets are lost. |
| Example: File transfer | Example: Live video streaming |

## What is a socket?

A socket represents a specific endpoint used for communication. This endpoint is achieved by combining IP address and a port number. 

## Conclusion

Though there are plenty of concepts that have not been covered here, the concepts described above provide an introduction to the fundamentals in short and easy to understand words.
