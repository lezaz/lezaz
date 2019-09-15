---
layout: post
title: "Thoughts on a Sustainable Web"
permalink: "general/2019/05/17/sustainable-web-thoughts"
categories:
  - General
comments_id: 2
---

Sustainability in the web is something everyone should take more seriously. [In 2015 the carbon footprint of the web eclipsed global air travel](https://www.theguardian.com/environment/2015/sep/25/server-data-centre-emissions-air-travel-web-google-facebook-greenhouse-gas). Surprised? You shouldn't be...  
All sites run on some server ran by a company that you pay. There are vast server-farms around the world, and they require lots and lots of power. Guess where all that power comes from... You guessed it, mostly coal. The internet is a huge, coal-burning machine. And though some companies around the globe have started investing in renewable energies and are slowly turning greener, the situation is not improving.  

But we can do better. It will probably take some time and a lot of hard choices, but we can do it. We just have to be a little more concious. And that goes for everyone: Developers, designers, and clients. Now I don't expect clients to start evangelizing for a greener and more sustainable web, most of the time they just want a site that works well and looks cool. But as developers and designers we can educate our clients and push them towards better decisions. After all it's in their best interest! The benefits of a greener, more sustainable website:

* Loads faster: A sustainable site has a significantly smaller footprint and loads a lot faster.
* Loads everywhere: Making your site sustainable means it's a lot lighter, so it will load on all devices.
* Lower hosting costs: By reducing your site's size, you waste less bandwidth and fewer server resources, and that translates to real savings. Fewer hosting costs for the client, fewer server costs for the host, fewer greenhouse emissions for the environment. Everybody wins.
* More accessible: Speed and accessibility go hand-in-hand. Instead of emulating a button element using JS and complex behaviours, use an actual HTML button element. By reducing the amount of code we have on our websites, better choices are made.
* Easier to maintain: It's easier to maintain a minimal and well-structured codebase than it is to maintain a large (and subsequently semi-chaotic) codebase - so even more savings in the long run.
* More secure: Fewer and simpler scripts mean less attack vectors.
* Better conversion rates: Nobody likes a slow website. Each second spent loading a website increases the chances users will just walk away before they even consider reading or buying.

## The reason we now have a problem

The main reason we are where we are is lazyness, and what I like to call "visual greed".  
When you start building a website, chances are you pick a platform/CMS like WordPress, Drupal or something olse - even an HTML template you bought somewhere. You start working on your site, add your content, images, menus, whatever your site will have. You go to your site, it loads and it's perfect!! So, you consider your job finished and move on to the next project, or if it's your own site you keep adding content, keep improving it etc.  
But there's one thing you most likely didn't check.  
How much data does your site need to load? You may see it load fast, but you're on a fast connection with a relatively modern PC or phone. Your site loads in less than 4 seconds, but that's only because your connection is able to handle a vast amount of data really fast, and your device is able to render that vast amount of data almost instantly.  

Now think about all the people and parts of the world that don't have access to your fast connection or state-of-the-art device. You don't need to imagine extreme conditions, 3rd-world-countries etc, just normal things that may influence the way we access the web.  
Up until 6 months ago, I was working on a 7-year-old laptop. It was doing its job just fine, I could get a code editor and a browser running and that's all I needed. The only reason I changed was because it just couldn't take the abuse and gave up. Anyone can have an old device as their main working environment, don't assume that an old device is only used by an 80 year-old guy living in a cabin up in the mountains.  
What about connection speeds? When it rains in my area, my connection speed drops to annoyingly ridiculously low levels. I'm talking about speeds I only tolerated in the 90's, and I now have to live with them whenever the weather is bad. Why? I don't know, maybe because the building is old, something's wrong with the wiring, it doesn't really matter. Just don't assume that everyone has access to blazing-fast internet, I guarantee you that's not the case all the time.

**If you design your site under perfect conditions, make sure it works equally well under poor conditions, otherwise you're just alienating visitors and customers.**

If 6 months ago on my old laptop I was trying to get to a page on a rainy day and it took more than 20 seconds, I just closed the tab and moved on to the next site. I don't have time to waste on waiting for your 1MB of CSS, another 1MB of JS and 10MB of images to load, I've got more important things to do.  
Seriously, that's not a far-fetched example. 2MB for CSS + JS is pretty normal these days. And 10MB of images is not that much either if they are not optimized and there's a featured image in an article, 4 related posts, 3 ads, a header image, avatars for authors, social-media avatars in a facebook widget in the sidebar and so on.  
And that's where the "lazyness" and "visual greed" I mentioned earlier come into play.  

## Designer's / Client's Visual Greed and Developer's Lazyness

You want your site to have a slider/carouser to show your featured products, you want micro-interactions, you want to have images appear crystal-clear on a device that has a 3000px screen-width and a 3 times the normal pixel density. You want to look cool and have all the bells and whistles.  
So what do you do?  
You pick a slider, add super high-res images, write a ton of JS (or add a plugin/module on your CMS) to get those micro-interactions just right, you add more and more and more.

Seriously?  
I mean, seriously????  
Do you honestly believe that if I have a choice between site A which looks perfect on a 40-inch screen but makes my $100 phone struggle, and site B which is more minimal but just works everywhere, I'm going to bother reading the content from site A, purchase a product or visit it again? Of course not!

Do you really need sliders? Most definitely not! It has been proven again and again that sliders have poor convertion rates, don't contribute anything to a site, are 100% not accessible by a large number of people, and are just bad no matter how you look at it. A hero image with a call-to-action button most times has a significantly better result.

That micro-interaction you wrote using JS? Yeah, the one you wanted to have a rotation on hover, wiggle when the user made a mistake when they submitted a form or whatever it is you wanted. Was it necessary? No, you just thought it would look cool. So the developer probably included a library to handle all those animations etc like jQuery for example, then wrote some code on top of it to do what was required. I mean there's no point in reinventing the wheel when sombody else has already done the job, right? Libraries are well-maintained and they have a purpose. But now you're loading an extra 100KB for jQuery + custom code, just because you wanted the form to wiggle when the user entered a wrong password. Was that really necessary? Absolutely not. But even if you deem the interaction or functionality necessary, was it necessary to load 100KB of code to do them? I seriously doubt it. It could have been done it a lot less than that, a lot more efficiently and performant. But that would mean extra work. The client would have to know what they're asking, the designer would have to avoid adding things that don't serve any real purpose other than make stuff move around (and in the process annoy a large portion of users - including me), and the developer would have to spend extra time to write some smarter code.  

That crisp image you loaded on the top of your site? Yeah, I know, you're really proud of that one, it looks great! But it's 3MB for an image I really don't care about. Really, I don't care about it. At all. I'll see it for a few milliseconds then scroll down and never give it a 2nd thought. If I'm on my phone, I just downloaded 3MB for an image that was displayed at 1 10th of its normal resolution. And for what?

## What you can do

There are many things we can do today to make our sites more sustainable and increase their value. We can start thing about what's best for everyone, not just how to make things looks cool.

* Choose a green host.
* You can implement lazy-loading for your images.
* Implement responsive images so when I'm on my phone I get a 100KB image instead of your 3MB one.
* Strip down elements that don't serve any function at all
* Write smarter code
* Use vanilla JS instead of libraries whenever possible
* Use CSS instead of JS whenever possible.
* Don't load all of your assets everywhere. If I'm not on a page that doesn't have a form, there's no need to load all your styles for forms.

## Is it worth it?

It depends. If we're talking about a site that gets 10 visits/month and doesn't generate any revenue, then no, it's not worth it.  
If we're talking about a site that has some traffic and has revenue (or the potential for a revenue) then in the long run it would be worth it.

## A note to developers

We can achieve many things if we're just smart with the code we write. I realize some of the things above may sound a bit scary at first, but they're really not. We just have to put some thought into the code we write instead of jumping in, head-first. Be smart, keep learning, keep improving. That's all one can do.  
In the meantime, give this some thought: A single pageload on an average site today is more than 5MB. Doom 2 was a first-person shooter game released in 1994 and it included a complete 3d engine, complex graphics, maps, scenarios and a whole lot of code. Its size was about 3MB. Be smart with your code. We can do better.

## Shameless self-promotion

If you're using WordPress, check out the [Gridd theme](https://github.com/wplemon/gridd). It is accessible, sustainable and open-source. Minimal JS, modular CSS, modern code and a lot of features that will allow you to build a great website.