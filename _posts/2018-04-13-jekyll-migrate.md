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
Some website updates: I have switched from using wordpress to using Jekyll. This will be full of tech details so you can ignore it if that doesn't interest you. Long story short: the web pages should load faster and the images should scale better.

## WordPress vs. Jekyll, a Quick Overview ##

What are [WordPress](https://wordpress.org) and [Jekyll](https://Jekyllrb.com/)? WordPress is the most popular CMS (Content Management System) in use for websites, it makes it easy to setup a website with a blog or e-commerce store or whatever you want. WordPress creates dynamic websites and uses a database which gives it lots of potential, but can also be overkill for simpler sites (like mine).

Jekyll is a static site generator, meaning it takes a bunch of files in specific formats and converts them to html to be served by a web server as static files. Because it is only serving static pages, the website should be faster to load and feel more responsive.

WordPress and Jekyll both have their uses, for me right now Jekyll seems like a better fit.

## WordPress vs. Jekyll, Formatting ##

Here are some comparisons between Jekyll and WordPress layouts (I probably could have adjusted WordPress better, but found it easier to change Jekyll). I meant to have the old WordPress site still active so you could compare yourself, but it actually was giving me some weird error and rather than fix it, I just switched over to the Jekyll generated site.

The main page on WordPress full screen:

{% responsive_image path: assets/images/jekyll-migrate/wp-fullmain.png %}

As you can see there is a lot of wasted space, compared to Jekyll:

{% responsive_image path: assets/images/jekyll-migrate/jek-fullmain.png %}

Next, here is a comparison of pictures in a post on a full screen:

{% responsive_image path: assets/images/jekyll-migrate/wp-fullpic.png %}

And compare that to Jekyll:

{% responsive_image path: assets/images/jekyll-migrate/jek-fullpic.png %}

So now you should see larger images and less wasted space.

## WordPress vs. Jekyll, the Numbers ##

In this section you can see the speed improvements. I used [gtmetrix](https://gtmetrix.com) to generate a page speed report, here is the summary for WordPress:

{% responsive_image path: assets/images/jekyll-migrate/wp-gtmetrix.png %}

Compared to Jekyll:

{% responsive_image path: assets/images/jekyll-migrate/jek-gtmetrix.png %}

gtmetrix also provides a waterfall to show you how long elements on your page take to load, here it is for WordPress:

{% responsive_image path: assets/images/jekyll-migrate/wp-waterfall.png %}

And for Jekyll:

{% responsive_image path: assets/images/jekyll-migrate/jek-waterfall.png %}

The initial response takes almost twice as long with WordPress (695ms vs. 375ms), and then the rest of the page starts loading and with increased speed in Jekyll because of the image scaling. These are some pretty noticeable gains. Below is a table from Google Page Insights with the scores:

| Site Version  | Mobile Score  | Desktop Score |
---|---|---|
WordPress | <span style="color:red"> 57</span>/100 |<span style="color:red"> 39</span>/100
Jekyll | <span style="color:yellow">67</span>/100 | <span style="color:green"> 83</span>/100

The Desktop score really stands out, making a huge improvement. Another area that will be improved even more with Jekyll is using a CDN (Content Delivery Network), which really improve page load times for static assets - and Jekyll is all static. I plan to set up a CDN soon and post about the improvements.

## It's not all Rainbows ##

It wasn't like flipping a switch and now my site uses Jekyll and is faster. I did have to spend some time to get Jekyll up and running the way I wanted.

### Theming ###

Getting the look right was about half the battle, I chose the [basically basic theme](https://mmistakes.github.io/jekyll-theme-basically-basic/) and made some modifications to get it the right shade of dark (and custom sidebar colors), and to have orange highlights, and a larger maximum size layout.

### Image Links ###

The other half (the one I'm still working on) is importing the old posts and formatting them correctly. I was able to use a neat [Jekyll Exporter](https://wordpress.org/plugins/jekyll-exporter/) WordPress plugin to get all my posts in markdown format. The next step was adding header information which wasn't so bad, the plugin took care of most of it. The images were another story though.

I wanted to use a Jekyll plugin for responsive images - basically when Jekyll generates the static site it will resize images for different screen sizes so if you are on your phone you aren't downloading a huge image, and if you are using a huge monitor the image is still clear. In WordPress this was handled by linking to the resized images, i.e. a picture link would be ````[some picture text](http://pickytri.com/wp-content/uploads/2017/09/somepicture800x640.jpg)```` and that's how all the images in the posts were exported. In Jekyll, with the responsive images the links are generated with the site, so a picture link would be ````{ % responsive_image path: assets/my-file.jpg % }````.

I was left with the task of going through all my old posts and replacing the links for all the pictures - not something I wanted to do manually. Luckily I was able to hack something together in the command line (which probably could have been done better, and in one line, but regex remains a little like magic to me). The commands I eventually used, one after the other were:

````bash
sed -i 's/\[[^][]*\]/\]/g' *
sed -i 's/\](//g' *
sed -i 's#http://pickytri.com/#{\% responsive_image path: #g' *
sed -i 's#.jpg)#.jpg \%\}#g' *
````

The first one removes everything between ``[]``, and replaces it with ``](``, so our link from above now would go from:

````
[some picture text](http://pickytri.com/wp-content/uploads/2017/09/somepicture800x640.jpg)
````

to:

````
](http://pickytri.com/wp-content/uploads/2017/09/somepicture800x640.jpg)
````

The next command removes the ``](`` giving us:

````
http://pickytri.com/wp-content/uploads/2017/09/somepicture800x640.jpg)
````

Third we remove the ``http://pickytri.com`` and add some things for the Jekyll responsive image plugin and get:

````
{ % responsive_image path: wp-content/uploads/2017/09/somepicture800x640.jpg)
````

And finally we tidy up the end and get:
````
{ % responsive_image path: wp-content/uploads/2017/09/somepicture800x640.jpg % }
````

And I managed fix all the image links in all the posts (sort of - I had to redo it for .png images and one or two were named .jpeg). I estimate the time it took to create and test those ``sed`` commands is about equal to replacing the links of one or two posts, so with more than 50 posts it was definitely a time saver. I can still learn much much more about regex (the stuff used to match and replace the strings) and probably could have done it all in one command, but this worked for me.

And just like most programming tasks, this "second half" turned out to be more like two thirds of the work.

### Still todo ###

And also just like most programming, after finishing the first two halves of the project, there's a third half to finish up. There are posts from the first platform I used - blogspot, and the image links there are not quite right. So I'll go back through and correct what I can.

I also plan to add comment sections back and a search function, things already built into WordPress that have to be tacked on to Jekyll.

Originally I planned to finish everything up and then switch the site over to the Jekyll version, but I had some strange WordPress error so rather than try and fix that, I just switched before I finished setting everything up.

## Other Notes ##

I started using [github](https://github.com) for this project, a version control system, I figured that would be a nice place to store all the theme configuration I did.

There's also now an RSS feed link, I never really got into that so eventually I suppose I'll take a look at it and see if I need to change anything.
