---
layout: post
title:  "Why we switched from WPEngine to Pagely and how we did it"
permalink: blog/wpengine-to-pagely
categories:
  - WordPress
---

While I was in WordCamp Europe last weekend I met a lot of the WPEngine staff, we talked, we laughed, I think they're a great bunch of people! However, while I was there the whole political & economic status of my home country - Greece - took a turn for the worst (actually it's a turn for the best if you look into the future of it, but short-term the outcome will be hard and difficult to bear).

In short here's what happened: Pending a referendum this Sunday, Greek banks decided to close for a week. All money transfers and orders to foreign accounts were suspended. Credit & debit cards could not make payments to foreign companies.

What does that mean? Well... I couldn't pay my WPEngine invoice using a credit card!

### Why we switched from WPEngine to Pagely

As soon as I got back from WCEU, I had a few emails in my inbox: DigitalOcean & a couple more hosting companies that I use had sent me an email stating that they understand there's going to be a delay in their payments from cards/accounts in Greece, so there's basically no rush. I also had a **Failed Payment Notice** email from WPEngine. I sent them an email explaining that I will not be able to pay my account in time and the answer I got was the following:

> Hi, Ari - Please ensure this invoice will be paid by July 7 to ensure there's no interruption in service. Payment will need to be made on a credit card. Thanks!

This is not a bad answer... but it's strict and rigid. I mean after all, banks are _supposed_ to be open on July 7. But the key word in the previous sentense is that they are SUPPOSED to be open. I don't want to feel as if I'm asking for something. I'm a proud man and not being able to pay in time - no matter the circumstances - is humiliating for me. Now the stance DigitalOcean had was awesome! They showed understanding and they were pro-active. That's what I want, that's what I'm looking for. And if not that, then at least an alternative method of payment - other than using a credit card.

Now... what if I can't pay in time? Does WPEngine accept PayPal? Nope... If they did I'd have no issues. So, my site will just be taken offline? No way! If they only accept credit cards and I can't pay in time then I simply can't host my site on them.

### Why Pagely?

Before using WordPress-dedicated hosting I was using DigitalOcean for all my hosting needs. I still do in some instances and I have a few VPSs on them, but my _main_ site I don't want to manage. I want someone else dealing with all the server-related stuff so I can focus on more important things. I decided to move the site to Pagely because I've read wonderful reviews for them, they accept PayPal, and I've used them before and was very satisfied.

### How we migrated the site from WPEngine to Pagely

Now, this wasn't as easy as you'd imagine, mostly because of all the experimentation and ways I tried to make this work. I'll skip all the failed attempts and just tell you what **did** work:

**[VaultPress](https://vaultpress.com/)**

VaultPress is a tool developed by Automattic that basically allows you to backup your site and then restore it if something bad happens.
One of the features it has however is that it allows you to restore your site to a different server! So, What we did was first backup the WPEngine site, then add an "alternative source" (basically the SFTP credentials for our Pagely server) in the VaultPress dashboard, and finally restore to that alternative source site.
It took a couple of hours to complete, but after that the site was copied on our new server!

A few things we had to do to make this work:

1. The alternative site must have a different domain. We had https://press.codes and https://presscodes.com so we kept the press.codes domain on the old server and used the presscodes.com domain on the new server. After we finished the migration we were able to add the press.codes domain on the Pagely dashboard and make it primary so no harm no foul. This is just something you have to do while migrating your site.
2. If you try to migrate your site and you get a message that says
    > The restore is unable to proceed because we can not communicate with the Restore Helper script.

    then please check the "path" you've defined in your alternative source. If it's empty, try adding a `/` character and see if that works.
3. Once the migrate finished you'll most probably get a generic error message from Pagely. Don't worry... WPEngine has a lot of custom scripts installed in the `wp-content/mu-plugins` folder. These are specific to their hosting environment & setup. These are included in the backup, so naturally when you try to first load your site Pagely tries to run the WPEngine-specific scripts and crashed. not to worry... Just login to your Pagely dashboard, make sure SFTP is enabled, then login to your site via SFTP, navigate to `wp-content/mu-plugins` and delete all WPEngine-specific scripts from there.
4. I would advise you to use a service like CloudFlare - if not permanently then at least while migrating your site. They have DNS management that takes affect on-the-fly. So as soon as the migration finished, all we had to do was go in our CloudFlare account & change the DNS entries for the server, define new values for our A records and everything was up and running again, without anyone ever noticing anything.

### Epilogue

I really wish I didn't have to make that switch, it took me nearly 20 hours of experimentation and failed attempts before I found a way that worked. I honestly have no complain from WPEngine, they were wonderful and if not for the eco-political situation in my country I don't think I would have switched. But there's a lesson to be learned from this as well...

**Being pro-active and caring for your clients world-wide makes a difference**.
