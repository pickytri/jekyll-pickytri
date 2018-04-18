---
layout: post
title: Cloudflare
date: 2018-04-18
author: matt
image: 
  path: /assets/images/headers/2018-04-18-cloudflare.png
  thumbnail: /assets/resized/480/images/headers/2018-04-18-cloudflare.png
categories: tech
tags: python, hosting, cloudflare, jekyll, wordpress, server, website
---
I have added Cloudflare as a CDN for this website and cleaned up a bit of extra files behind the scenes - again another tech heavy post with details you can ignore but should make the website load faster.

## Cloudflare and CDNs ##

### CDNs ###

[Cloudflare](https://cloudflare.com) is a CDN or [Content Delivery Network](https://en.wikipedia.org/wiki/Content_delivery_network) which pretty much means it speeds up delivering content over the internet. In it's simplest form a basic website will be served from one server in one location to many clients all over the world. This leads to a situation where clients closer to the server have faster speeds, for example if the server is located in California people in New York will not have things loading as fast as people in California. This could be a big problem if you are a business like Netflix, and upgrading your data center in California won't solve the problem. The solution is to build a new data center in New York and cache the content closer to the destination. Then clients in New York will be served from the local data center which will speed things up compared to California.

{% responsive_image path: assets/images/cloudflare/cdn.png %}

On the left you can see a single server responding to all the clients, on the right you can see multiple servers, with the closest ones to the client serving the content.

### Cloudflare ###

Cloudflare provides a CDN that will cache content from your server and use that to answer client requests. I set up [matthewlindsey.net](https://matthewlindsey.net) on Cloudflare last week and you can see some savings.

{% responsive_image path: assets/images/cloudflare/cloud-cached.png %}

This illustrates in the last 24 hours there have been 60 requests and 14 of those have been for cached content, i.e. if the client requests an image that is stored in the Cloudflare cache, the cache serves the image without having to retrieve the content from my server. As you can see below there are some bandwidth savings:

{% responsive_image path: assets/images/cloudflare/cloud-saved.png %}

Note that out of almost 2MB a total of 786KB was served from a cache. This might not seem like much, but my website doesn't get many hits. Consider it on a bigger scale with a site serving millions of requests.

### The Numbers: Did it Help? ###

Setting up Cloudflare is pretty straightforward, just change the DNS nameserver (basically the internet phonebook that tells your browser what IP to go to when you type in a website name) to Cloudflare and wait for it to propagate. I had some more backend stuff because my IP address changes whenever Verizon leases a new one so I have a small program set up to tell Cloudflare my new IP address whenever it changes.

If you don't remember, last week I had comparisons of WordPress vs. Jekyll, here are the same ones with Jekyll vs. Jekyll w/Cloudflare. First up, plain old Jekyll:

{% responsive_image path: assets/images/cloudflare/jek-gtmet.png %}

The score is a little lower than last week because I am using larger resolution images for thumbnails but not by much. And here it is with Cloudflare:

{% responsive_image path: assets/images/cloudflare/cloud-gtmet.png %}

The scores improved a bit. The loading time went up a little as well, strange but it is being served from a different location now. I'll have to check again later to see if it is consistently slower.

Here is the waterfall of plain Jekyll:

{% responsive_image path: assets/images/cloudflare/jek-water.png %}

Compared to Cloudflare:

{% responsive_image path: assets/images/cloudflare/cloud-water.png %}

Here we get some more insight as to why it was slower than without Cloudflare, the initial GET request takes 513ms with Cloudflare (request has to go to Cloudflare then to my server then back to Cloudflare and then back to client) vs. 329ms without Cloudflare (request only has to go to my server and back to client). But look at the image loading requests - for Cloudflare they are all under 100ms (the images are all cached at the Cloudflare server), and without Cloudflare the quickest is 273ms and most are in the high 400ms range. I imagine a page with more pictures would favor Cloudflare heavily - unfortunately I didn't test it without Cloudflare so I can't really compare, but after some tests with image heavy pages I am convinced it would be much faster loading with Cloudflare.

And finally we come to Google Page Insights tests:

| Site Version  | Mobile Score  | Desktop Score |
---|---|---|
WordPress | <span style="color:red"> 57</span>/100 |<span style="color:red"> 39</span>/100
Jekyll (small thumbnails) | <span style="color:yellow">67</span>/100 | <span style="color:green"> 83</span>/100
Jekyll (large thumbnails) | <span style="color:green"> 83</span>/100 | <span style="color:yellow"> 62</span>/100
Jekyll (large thumbnails w/Cloudflare) | <span style="color:green"> 83</span>/100 | <span style="color:yellow"> 69</span>/100

So all in all pretty good improvements, especially from WordPress to Jekyll, adding in Cloudflare is just a nice little boost. Still some areas to improve, but will get to those eventually.

### Bonus: Added Security ###

A bonus from using Cloudflare is added security. They have built in DDoS mitigation. A DDoS is a type of attack meant to bring down a server by overloading it. The attacker will have a bot net of thousands of compromised computers all make thousands of bogus requests to the target server, eventually the server will become so overloaded trying to process all those requests that legitimate requests will be dropped and the website goes down.

Just having more servers helps mitigate DDoS attacks, especially if those servers are spread out. But there are other things like analyzing traffic patterns that Cloudflare will perform as well.

And while I haven't been the target of any DDoS attacks, I do get a steady stream of bots probing my websites for security flaws. For example here is a snippet of the log with two requests from bots looking for "/wp-login.php" - a WordPress login page (which I don't have anymore after switching to Jekyll):

    162.158.89.186 - - [18/Apr/2018:06:50:34 -0400] "GET /wp-login.php HTTP/1.1" 404 143 "-" "Mozilla/5.0 (Windows NT 6.0; rv:34.0) Gecko/20100101 Firefox/34.0"
    172.68.244.89 - - [18/Apr/2018:07:03:18 -0400] "GET /wp-login.php HTTP/1.1" 404 143 "-" "Mozilla/5.0 (Windows NT 6.0; rv:34.0) Gecko/20100101 Firefox/34.0"

(Just a note - the IPs are from Cloudflare because these bots are requesting the content from Cloudflare servers which request it from my server - there are ways to get the origin IP address I haven't implemented yet).

I see about 20 of these requests a day. When I used WordPress they found the login page, and I'd have to rely on my password to protect the site. Against a computer guessing all day for as long as my website was up (well not really, there are things to block repeated login failures, but still a concern). Now with Jekyll and static pages there is no login, just static content.

## Cleaning Up ##

I have completely removed the WordPress site and in the process of cleaning up the directory structure of the new site went back and adjusted the header images for each page, something no one will notice but me when working with the files, but I did get to learn a little more about regex and python in the process.

Each Jekyll post starts with front matter identifying it, for example:

    ---
    title: "Migrated to Jekyll"
    date: 2018-04-13
    author: matt
    layout: post
    image:
      path: /assets/images/headers/2018-04-13-jekyll-migrate.png
      thumbnail: /assets/resized/480/images/headers/2018-04-13-jekyll-migrate.png
    categories: tech
    tags: hosting, server, wordpress, blog, website
    ---

This tells Jekyll what the title, date and other information about the post is. The problem I ran into was I wanted to consolidate my images into one folder, the ````"/assets/images"```` folder but my old posts had images in their old WordPress folder ````"/wp-content/upload/year/month/```` or blogger folder. Pretty easy fix for one or two posts, go in and change the front matter path location for the image and copy the images to the new folder. But I have about 50 posts, going through each one seems a bit tedious.

Enter python, a programming language that's great for things like this. Rather than my fix last week, which involved some command line magic, editing multiple lines in multiple files seemed like an easier task to accomplish with python.

It took some tinkering but I ended up with a nice little program, here is the relevant part:

````python
tomove = open("tomove", "w")
tomove.write("#!/bin/bash \n")

for x, files in enumerate(mdfiles):
    fullpath = os.path.splitext(files)[0]
    title = os.path.basename(fullpath)
    print(files)
    f = open(files, "r")
    nf = open((title + ".new"), "w")
    for line in f:
        if re.match("  path:(.*)", line):
            origpath = line.replace("  path: /","")
            origpath = origpath.replace("\n","")
            ext = os.path.splitext(origpath)[1]
            tomove.write("cp " + origpath + " assets/images/headers/" + title + ext + "\n")
            nf.write("  path: /assets/images/headers/" + title + ext+ "\n")
        elif re.match("  thumbnail:(.*)", line):
            nf.write("  thumbnail: /assets/resized/480/images/headers/" + title + ext + "\n")
        else:
            nf.write(line),
    f.close
    nf.close
````

Basically what this does is open every file of a certain type in a directory, create a new file that I can run as a bash script that will copy the images to their new location, and it replaces the front matter lines ````path:```` and ````thumbnail:```` with the new location.

That should just about do it for setting up Jekyll, just some fine tuning left.
