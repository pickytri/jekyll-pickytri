---
id: 35
title: 'Raspberry Pi Home Server - Part 2 - OpenVPN and more'
date: 2017-03-07T19:23:02+00:00
author: matt
layout: post
image: 
  path: /assets/images/headers/2017-03-07-raspberry-pi-home-server-part-2-openvpn-and-more.jpg
  thumbnail: /assets/resized/480/images/headers/2017-03-07-raspberry-pi-home-server-part-2-openvpn-and-more.jpg
categories: tech
tags: DASH LAMP OpenVPN pi plex syncthing
---
In my first raspberry pi post I described how I set up the raspberry pi to block ads using Pi-hole. But there were some other things I was running:
  
&#8211;[OpenVPN server<!--more-->


  
http://www.pivpn.io/) &#8211;[Plex media server
  
https://www.plex.tv/) &#8211;[Syncthing
  
https://syncthing.net/) &#8211;[LAMP stack
  
https://en.wikipedia.org/wiki/LAMP_(software_bundle)) &#8211;https://jumpifzero.wordpress.com/2014/04/15/installing-linux-dash-on-nginx-on-raspberry-pi/)

&nbsp;

**OpenVPN** is probably the most complicated to explain, but generally it acts as a tunnel for traffic from a device I use on the internet to my home network. This tunnel encrypts my traffic so if I was connected to an open wifi network to my VPN server the traffic is protected from any malicious people who might try to intercept and view my data. It also has the effect of making my device appear like it is locally connected to my router to both my router and any outside servers I might contact.  Here&#8217;s a simple diagram to help explain:

{% responsive_image path: wp-content/uploads/2017/03/VPN.jpg %}

The red network represents an unsecure wifi you are connected to on your phone with a malicious user on the laptop. The orange network is your home network, and they are connected by the internet, which also has a connection to a server. Normally when you browse the internet from your phone in this situation your data travels to the Open wifi router then to the internet and server and back to you. With VPN on your home router (or a raspberry pi connected to your home router) your phone first encrypts the request, sends it to the VPN server (the raspberry pi) which decrypts it and forwards it to the server over the internet. The request still travels across the red network to get to your home router, but it is encrypted so the malicious laptop user can&#8217;t see the data. The server returns the requested data to the VPN server (the raspberry pi), which encrypts the data and sends it back to my phone. This prevents the malicious laptop user from examining your data and also makes the server think you are connected to your home router.

I set up my pi to act as a VPN server so now my phone can connect to it when I&#8217;m out in the world and I can access my local network. As an added bonus, I can use the raspberry pi as my DNS server so pi-hole can block ads even when I&#8217;m away from home.

Note: while this situation encrypts traffic travelling between my phone and my router, any requests made outside the local network will not be encrypted.

Further note: depending on how much data is flowing through my VPN server the raspberry pi can get overwhelmed trying to perform all that encryption and decryption. More than a couple users or anything using lots of bandwidth will slow down the pi and possibly crash it.

**Plex Server**

{% responsive_image path: wp-content/uploads/2017/03/plex.jpg %}

Speaking of tasks that really bring the pi to it&#8217;s knees &#8211; I also installed a Plex server on my pi. Plex is a media streaming server/client that organizes your media files and can play and sync them across many devices. Usually you want your plex server to be fairly powerful so it can perform transcoding (changing resolution of videos so streaming them uses less bandwidth or for low powered clients who can&#8217;t play the full resolution). The pi can&#8217;t really handle much transcoding, but that&#8217;s ok because for streaming videos across my local network there is plenty of bandwidth.

**Syncthing** is another cool utility running on my pi. It is a distributed bit torrent synchronization platform. You install it and select folders to keep synchronized across devices and it takes care of transferring the files using bit torrent.

{% responsive_image path: wp-content/uploads/2017/03/synthing.jpg %}

I installed it on my phone, my desktop and the raspberry pi. Then I selected folders to keep synchronized between them, like the camera and backup folders on my phone. Now whenever I am on my local network my phone syncs all the pictures I took with my computer and my raspberry pi.

A **Lamp Stack** is just short for Linux, apache, mysql and php and represents a model of web service. For now my raspberry pi is just a testing webserver but I could migrate this website (which is being served from my desktop computer) to the raspberry pi. A project for another day.

{% responsive_image path: wp-content/uploads/2017/03/dash.jpg %}

**DASH** monitoring is a simple web based remote monitoring program so I can just type an address into any local web browser see how my raspberry pi is doing. Like when I tried to transcode a video file or had a few too many VPN connections I could see the CPU usage jump up on the graphs.

And that&#8217;s pretty much it for my pi. It&#8217;s doing much more than my first one which sat idle connected to my tv. I have plans to build a more powerful server to take over some of these duties, but the pi is a great starting place.
