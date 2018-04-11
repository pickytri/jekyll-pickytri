---
id: 7
title: 'Raspberry Pi Home Server - Part 1 - What is it and what can it do?'
date: 2017-03-07T17:51:04+00:00
author: matt
layout: post
image: 
  path: /wp-content/uploads/2017/03/RaspberryPi3-1024x669.jpg
  thumbnail: /wp-content/uploads/2017/03/RaspberryPi3-300x196.jpg
categories: tech
tags: pi pihole
---
What is a https://en.wikipedia.org/wiki/Raspberry_Pi)? it&#8217;s a small (think credit card size), cheap computer with some interesting options for tinkering and learning. You can do many things with it, like set up a https://retropie.org.uk/) to play old arcade or console games, set up a https://www.raspberrypi.org/blog/magic-mirror/) or https://www.google.com/search?q=raspberry+pi+projects). It really is pretty neat.<!--more-->

{% responsive_image path: wp-content/uploads/2017/03/RaspberryPi3.jpg %}

&nbsp;

Recently I found out about https://pi-hole.net/), a DNS level ad blocker that runs on a raspberry pi and thought that would be a cool and useful project.

The second part is the important thing for me, I remember hearing about the pi when it first came out a few years ago and thought &#8220;cool I want one&#8221; so I got one and it sat plugged into my tv not doing much other than being a video player for my tv &#8211; and did that pretty poorly because I treated it like I had another desktop computer. The most use it got was that time we watched a 3d movie on it. Then it sat gathered dust until I lost track of it during a move, and it wasn&#8217;t that big a deal because it was only $35 and it was fun to put together &#8211; but I really could have done much more with it.

So this time around I wanted to make sure I was getting more use out of it than a fun project to build on the weekend then forget about and never use again. While Pi-hole fit the bill, it also seemed like a waste to just be a DNS ad blocker so I decided to test it out as a cheap home server and am pretty happy with the results.

{% responsive_image path: wp-content/uploads/2017/03/IMG_20170307_095809.jpg %}

Well cable management has always been a problem. But I do have it up and running with a few cool things that I do use and even managed to build a lego case around it, a usb hub and an external hard drive. Here&#8217;s what&#8217;s running at the moment:

&#8211;https://pi-hole.net/)
  
&#8211;[OpenVPN server
  
http://www.pivpn.io/) &#8211;[Plex media server
  
https://www.plex.tv/) &#8211;[Syncthing
  
https://syncthing.net/) &#8211;[LAMP stack
  
https://en.wikipedia.org/wiki/LAMP_(software_bundle)) &#8211;https://jumpifzero.wordpress.com/2014/04/15/installing-linux-dash-on-nginx-on-raspberry-pi/)

Pi-hole is an ad blocker at the DNS level. DNS is what computers use to determine IP addresses. For example when you type google.com in your browser first your computer makes a DNS request to a DNS server which looks up google.com and returns the associated IP address to your browser. Then your browser makes a request to the IP address and the servers will return google.com content.

You can probably see whoever maintains the DNS servers has a staggering amount of control over your browsing, if they wanted to direct google.com requests to yahoo.com they could just edit the google.com entry to point to yahoo IP address.  Or they could remove entire websites from the internet by removing entries (well sort of, you could still access a site if you knew it&#8217;s IP address). This doesn&#8217;t happen because security protocols and if a DNS server started messing with it&#8217;s entries people would stop using it &#8211; not to mention it would basically crash the internet.

So what does all this have to do with a Pi-hole? What if you had a DNS server that didn&#8217;t return IP addresses for domains associated with ad servers? That&#8217;s what Pi-hole does, first you set up the raspberry pi with Pi-hole and next you configure your router to use the raspberry pi as the DNS server (by setting the DNS server in the router any device connected to the router will use the pi as their DNS server, like your phone when it&#8217;s on wifi). The pi blocks ad domains and forwards legitimate domain queries to a real DNS server out on the internet that you chose so you don&#8217;t actually have to maintain DNS records on the pi. The DNS server will return the non ad domain IP addresses and you can access the website without ads. Here&#8217;s a screenshot from the web interface:

{% responsive_image path: wp-content/uploads/2017/03/pihole.jpg %}

You can see the computers on my network made nearly 20,000 requests and just 171 were blocked. Pretty neat.

This post getting to be longer than I thought so I will save the explanation of the other things my raspberry pi is running for another post (or 2).
