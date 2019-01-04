Recently, I’ve been learning more about computer networking and part of that exploration has led me to learn more about domains and domain name servers. 
Computer networking is such a vast topic and being new to computer networking, I found myself confused with all the different terminology and concepts. 
I wanted to cover this topic because I think it’s especially useful if you ever wanted to set up your own domain name and all the infrastructure that goes along with it. 
Along the way, we’ll cover some tools that may also be useful.

So, first things first..

## What is a domain name? 
Computers work by routing you to a specific IP address (e.g., `172.217.6.14`). As humans, this can be hard to remember, so we use domain names. 
A domain name is the address of your website in plain english. In your browser, you can either type `www.8thlight.com` or `52.222.222.131`. They mean the same thing. 
A domain name is made up of the following parts: the sub-domain (`www`), the domain (`8thlight`) and the the top level domain (`.com`).

A note of clarification - a domain name is not the same as a url. For example, the url: `https://www.8thlight.com` has the domain name `8thlight.com`.

A helpful tool to find the IP address of a domain name is `ping`. This tool can be used for debugging purposes if you’re setting up your own domain name. 
You can double check that your domain has an IP address associated with it. Here’s a sample of the output:

```
$ ping www.8thlight.com
PING d114sp4kkqch89.cloudfront.net (52.222.222.131): 56 data bytes
64 bytes from 52.222.222.131: icmp_seq=0 ttl=246 time=27.143 ms
64 bytes from 52.222.222.131: icmp_seq=1 ttl=246 time=21.353 ms
```

## How does this relate to a Domain Name System?
Domain Name System (DNS) is the translation layer, storing the mapping of domain name to IP address. 
It’s a database that contains all the records of public addresses and their associated domain names. 
This translation layer is fairly complex, so I'm going to layout the basics below.

### What is a DNS Server?

DNS software can be installed on servers, thus making them a DNS Server. 
This software allows for the lookup process to occur. You may have heard of the term Name Server (NS). 
These are servers that have DNS.

### How does the lookup work?

The lookup process is actually a series of lookups. Here's a visual that describes what the lookup process
would look like for `www.drive.google.com`

![DNS](/assets/DNS.jpg)

When you make an http request to `www.drive.google.com` it first hits the `com` name server. The `com` name server
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
