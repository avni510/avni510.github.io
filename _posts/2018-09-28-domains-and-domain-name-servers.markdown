---
layout: post
title:  Domains and Domain Name Servers 
date:   2017-09-28 07:04:00 -0600
categories: DNS 
---
 
Recently, I've been learning more about computer networking and part of that exploration has led me to
learn more about domains and domain name servers. Computer networking is such a vast topic and being new to computer networking,
I found myself confused with all the different terminology and concepts. I wanted to cover this topic because I think it's especially
useful if you ever wanted to set up your own domain name and all the infrastructure that goes along with it. Along the way we'll cover
some tools that may also be useful. 

### What is a domain name? 

It is the address of your website in plain english. Computers work by routing you to a specific IP address
Ex: `172.217.6.14`. As humans this can be hard to remember so we translate them to domain names. 
In your browser you can either type `www.8thlight.com` or `52.222.222.131`.  A note of clarification - a domain
name is not the same as a url. For example, the url: `https://www.8thlight.com` has the domain name `8thlight.com`

A helpful tool to find the IP address of a domain name is `ping`.

```
$ ping www.8thlight.com
PING d114sp4kkqch89.cloudfront.net (52.222.222.131): 56 data bytes
64 bytes from 52.222.222.131: icmp_seq=0 ttl=246 time=27.143 ms
64 bytes from 52.222.222.131: icmp_seq=1 ttl=246 time=21.353 ms
```

### How does this relate to a Domain Name System?

Domain Name System (DNS) is the translation layer, it stores the mapping of domain name to IP address. It's a database
that contains all the records of public addresses and their associated domain names. This translation layer is
fairly complex and I'm going to into the basics below.

### What is a DNS Server?

DNS software can be installed on servers. Thus making them a DNS Server. 
This software allows for the lookup process to occur. You may have heard of the term Name Server (NS). These
are servers that have DNS. 

### How does the lookup work?

The lookup process is actually a series of lookups. 

![Mind Blown](https://media.giphy.com/media/OK27wINdQS5YQ/giphy-tumblr.gif)

Here's a visual that describes what the lookup process
would look like for `www.drive.google.com`

![DNS](/assets/DNS.jpg)

When you make an http request to `www.drive.google.com` it first hits the `com` name server. The last segment
of the domain name is called the top level domain. Other examples include `org`, `gov`, etc. The `com` name server
only has knowledge of the next part of the address, so it returns the IP address for - `google.com`. The `google.com`
NS has knowledge of `www.drive.google.com` so it finally returns the IP address for that and your browser is directed
to that IP address.

To lookup the NS servers IP addresses for `8thlight.com` we can use `nslookup`

```
$ nslookup 8thlight.com
Server:         192.168.0.1
Address:        192.168.0.1#53

Non-authoritative answer:
Name:   8thlight.com
Address: 52.84.141.164
Name:   8thlight.com
Address: 52.84.141.215
Name:   8thlight.com
Address: 52.84.141.74
Name:   8thlight.com
Address: 52.84.141.163
```

Keep in mind the IP address is different for `www.8thlight.com`

```
$ nslookup www.8thlight.com
Server:         192.168.0.1
Address:        192.168.0.1#53

Non-authoritative answer:
www.8thlight.com        canonical name = d114sp4kkqch89.cloudfront.net.
Name:   d114sp4kkqch89.cloudfront.net
Address: 52.222.222.131
Name:   d114sp4kkqch89.cloudfront.net
Address: 52.222.222.36
Name:   d114sp4kkqch89.cloudfront.net
Address: 52.222.222.73
Name:   d114sp4kkqch89.cloudfront.net
Address: 52.222.222.111
```

This is because the first `nslookup` is to the name server where as the second `nslookup` is to the website. The name
server will return the IP Address of the website `www.8thlight.com`

### Conclusion

Typing an address into your browser is actually a complex process and it all happens so fast! 
DNS is actually something we use everyday without even knowing it. It is one of the many parts of the internet that allows us to access a website. 
DNS just grazes the surface of computer networking. For more topics on computer networking you may enjoy this book **Computer Netowrking a Top Down
Approach** By Kurose and Ross
