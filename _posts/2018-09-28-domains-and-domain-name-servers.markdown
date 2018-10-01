 DNS  blogpost
  - Tool you can use to access these things
 
  Why should I care?
  - You use it everyday
  - It can be helpful to know the internals if and when you would like to set up your own
 
 
## Domains and Domain Name Servers
 
Recently, I've been learning more about computer networking and part of that exploration has been to 
learn more about domains and domain name servers. I'm currently working on deploying my personal blog
and I wanted to setup my own domain that my blog could live on. 

### What is a domain name? 

It is the address of your website in plain english. Computers work by routing you to a specific IP address
Ex: `172.217.6.14`. As humans this can be hard to remember so we translate them to domain names. 
In your browser you can either type `www.8thlight.com` or `<IP address`>. A domain name is sometimes referred to 
as the host name. A note of clarification - a domain
name is not the same as a url. For example, the url: `https://8thlight.com/team/` has the domain name `8thlight.com`

A helpful tool to find the IP address of a domain name is `ping`. (more info about this tool?)
// Sample output of ping

### How does this relate to a Domain Name System?

DNS is the translation layer, it stores the mapping of domain name to IP address. It's a database
that contains all the records of public addresses and their associated domain names. This translation layer is
fairly complex and I'm going to into parts of it below.

### What is a DNS Server?

DNS software can be installed on servers. Thus making them a DNS Server. 
This software allows for the lookup process to occur. You may have heard of the term Name Server. These
are servers that have DNS. 

### How does the lookup work?

The lookup process is actually a series of lookups. It can be thought of similar to 
Here's a visual that describes what the lookup process
would look like for `www.drive.google.com`

http_request -> com NS (top level domain) -> google.com NS  -> returns IP address for www.drive.google.com

When you make an http request to `www.drive.google.com` it first hits the `com` name server. The last segment
of the domain name is called the top level domain. Other examples include `org`, `gov`, etc. The `com` name server
only has knowledge of the next part of the address, so it returns the IP address for - `google.com`. The `google.com`
NS has knowledge of `drive.google.com` so it finally returns the IP address for that and your browser is directed
to that IP address.

- nslookup tool
- traceroute
- reverse DNS lookup

This process and all the vocabulary associated with it took me a while to wrap my head around. Typing an address your
browser is actually a complex process and it all happens so fast! DNS is actually something we use everyday without
even knowing it. This can be helpful when you want to create your own domain name and host a website on it.
I hope this post demystifies some of the mysteries of the complex world of networking.


