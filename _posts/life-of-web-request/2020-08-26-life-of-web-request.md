---
title: Day in a life of Web Request
date: 2020-08-26 11:04:00 +07:00
tags: [networking, web request, request-response cycle]
description: Request response cycle when we enter any URL in browser.
image1: /life-of-web-request/DNS-query-768x432.webp
image2: /life-of-web-request/DNS-query-response.webp
twitter: https://twitter.com/ayush_garg341
---

In the fast growing tech world, we all know how to search for a problem. Not just developers but a common person knows how to use the internet and get results. But as a developer we all should know something more, like what happens behind the scene when we enter any URL or specifically **www.google.com**. So let's get started without any further ado.


There are steps involved in the process, so these are as follows:-

**1\.)** When we first get connected to the internet, our device is assigned a **public IP address** and after that all sort of communication happens.

How our device gets IP address ?  
It gets IP address from **DHCP ( Dynamic Host Configuration Protocol )** server. When we use our cell phone to get access to internet, then our **ISP ( Internet Service Provider )** works as DHCP server and allocates us IP address. But when we are on LAN, sometimes are router is configured as DHCP server or there is a separate DHCP server ( better approach ).

**`Note:-`** Our device does not know the IP address of DHCP server, it broadcast a query to the network and every device on the network receives that, but only DHCP server responds to it, since it knows what to do with the query. DHCP operations fall into four phases: **server discovery**, **IP lease offer**, **IP lease request**, and **IP lease acknowledgement**.

So it happens like this:-  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**a\.** First device broadcast a query and says will someone provide me an IP address ? ( **IP lease request** )  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**b\.** Now the right candidate ( DHCP ) will respond, is this x.x.x.x works for you ? ( **IP lease offer** )  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**c\.** The device will respond, yeah this is fine ( **IP lease acknowledgement** )  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**d\.** DHCP aknowledges this and says start using this IP from now onwards.


**2\.)** Now we have got an IP address for the device, next thing is to get the IP address of **www.google.com** which is knows as **DNS ( Domain Name System )**  resolution.

Every device on the internet is recognized by its IP address. No device knows what is www.google.com, though these names are given for human to easily remember the names of websites instead of some random gibberish looking number like 192.132.124.121, before we send any HTTPS request too google server, we must know the IP address of google server and this is done by  DNS resolution.

**`Note:-`** DNS is an application layer protocol. DNS resolution uses UDP, instead of TCP since speed is very critical parameter in DNS resolution and TCP is slower in comparison to UDP due to 3-way handshake. Some of you might think that **UDP is not reliable**, then why uses UDP where reliablity is a concern ( DNS resolution ) ? The answer is reliability can be added on application layer. An application can use UDP and can be reliable by using a timeout and resend at the application layer.  

DHCP server also provides the **MAC ( Media Access Control )**  address of first hop router. At this point, we have got two things, our device IP address and MAC address of first hop router, so it can now send frame containing DNS query. A DNS query and a DNS reply share the same basic structure. Let’s have a look at one sample DNS query and corresponding response in **Wireshark ( packet sniffer )**.

<figure>
<img src="{{ page.image1 }}" alt="DNS query">
<figcaption>Fig 1. DNS query structure.</figcaption>
</figure>


<figure>
<img src="{{ page.image2 }}" alt="DNS response">
<figcaption>Fig 2. DNS response structure.</figcaption>
</figure>

To explain the DNS query and DNS response is out of scope of this article. DNS resolution is itself a complex process and happens at many stages. See <a href="https://www.cloudflare.com/learning/dns/what-is-dns/" target="_blank">this</a> article to get deeper understanding of how DNS resolution works.


**3.)** HTTP request

Now we have got IP address of **www.google.com**, device own public IP and MAC address of first hop router. At this point we can make HTTP query and send it into packet, web server will respond with HTTP reply ( web page ).

**`Note:-`** HTTP uses TCP instead of UDP because it guarantees delivery via a three-way handshake and re-transmission of lost packets. Also because the files, images, web pages which we get from the remote host should not be dropped on the way and it should be delivered in order to the HTTP client.
HTTP could also use UDP but usually not, if a UDP packet containing the first part of a web page is lost, then its not retransmitted. Though we can write some code in the application layer to handle these retransmissions of lost packets when we use UDP. But we don't want to burden the web browsers ( Application Layer) in doing this work rather than doing the actual work of rendering pages.


*This is the end of this article on basic networking of web request. Stay in touch. Please follow on <a href="{{page.twitter}}" target="_blank">Twitter</a>.*