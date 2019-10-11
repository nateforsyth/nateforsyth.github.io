---
layout: post
title: An objective look at SharePoint customisation
date: 2019-10-10 +13:00
published: true
category: Discussion
tags: [sharepoint, customisation, out of the box experience, a way forward, dev perspectives]
---

I've had a lot of time to reflect upon my career recently. You may not know from reading my content, but I never intended to become a SharePoint Developer, in fact, it was more or less forced upon me.

My current role is officially **.NET Developer**, however on day one it was clear that my entire focus was going to be SharePoint. That wasn't in the position description. I digress.


### Reflection

There were enough similarities between the SharePoint eco-system and the greater Microsoft stack that I took the opportunity with open arms and set about learning a massive amount of material to become, if I can be bold, a damned good SharePoint Developer.

My daily bread and butter consists of the SharePoint Framework (SPFx), PowerShell, provisioning, configuration, administration, Azure and the various web based incarnations of the ASP.NET Framework. At least it used to.

I'll touch on that last point in a bit, first check out my [resume](https://dreamsof.dev/resume/) and scroll down half way for an indication of where my SharePoint specific/relevant skills lie.

I've noticed within the last ~6 months or so that Microsoft are making customisations harder and harder to persist. I'm not talking about simplistic Web Part and Extension projects, I'm talking about what we term to be addressing specific flaws within Microsofts' current offering. One specific example is a hard coded intercept that redirects from the Modern Search Box in the Tenant Bar within Office 365 to the Classic Search Centre.

Classic Search is still the preferred solution for large Enterprise clients because it enables you to surface item meta data as refiners for a very rich search experience.

Sure, Modern Search is getting better, but it's got a hell of a long way to go before it can be considered a true replacement for the Classic Search Centre.

We used an SPFx Extension component to intercept and override all of the gubbins behind the scenes that allow for the Modern Experience to be ignored, allowing a true redirect to the Classic Experience for Search.


### Why is a Search Override a big deal?

If you've had much experience with Classic and Modern Search side-by-side you'll be aware that there are no meta-data based refiners in Modern. Take my word for it if you're not.

Basically, we intercept the search box, passing the string through the extension and invoke a page load to classic search passing the search string through to the Classic Search page.

I'm talking about the difference between this:

![SharePoint Modern Search Box](/img/ObjectiveSpo01.png)

![SharePoint Modern Search Refiners (lol)](/img/ObjectiveSpo02.png)

And this:

![SharePoint Classic Search Refiners](/img/ObjectiveSpo03.png)


### Simple right? Well no, it's not actually

You'd be right in thinking "yo dude, just target the IDs and classes within the DOM", that's exactly what we do, and have done for quite a long time. But within the last 6-9 months Microsoft has begun obfuscating the DOM with automatically generated class names, which have become a right pain in the arse to maintain. Here's an example.

![SharePoint DOM for Tenant Search Box](/img/ObjectiveSpo04.png)

These change every time that Microsoft updates SharePoint; that's every 1-2 weeks. Guess what happens when we've already sold a solution such as this to an Enterprise client and they're actively using it... that's right, **technical debt** and **convoluted support**.


### Get to the point already!

I hope you can see by now that even simple solutions for simple requirements can be rendered convoluted by a change in direction by Microsoft. I do understand it though, it must be a nightmare supporting a monolithic eco-system that *users can customise*.

So, Microsoft is pushing everyone to their out of the box solutions, fine. But where does that leave us SharePoint Developers?

By nature we build custom solutions, it's what we do. Sure, we can continue to provide custom SharePoint UX to clients allowing them to visualise and/or interact with their data and assets the way they see fit. But that's not what I am seeing.

Perhaps it's a by-product of the New Zealand market, but most if not all of our clients are becoming shy of the additional overhead of maintaining custom solutions. It's not their fault, we already know that it's the way Microsoft is pushing them. By the way, I'm not talking about small companies here, sure we have a few around the 200 seat range, but these are also our ~6,000 seat clients.


### So what are we doing about this?

The writing is on the wall, and the company I work for is embracing the trend away from customisation. Sure, we have automated provisioning tools and a suite of SPFx components backed by PowerShell scripts for automation, but the custom development is drying up. We're definitely in maintenance mode, and it sucks when all you want to do is code.

The upper echelons are pushing for simplified provisioning of out of the box solutions, with just enough customisation to be able to surface rich meta data through search to enable users to drive their *business as usual* activities from Search and Microsoft Teams.

It's an honourable endeavour, one that is allowing our clients to efficiently and effectively utilise what they've already paid for (their Office 365 subscriptions) to work smarter, not harder.

The push to out-of-the-box solutions is happening, and we as a company are embracing it.


### Where does that leave this SharePoint Developer?

That's the part I am not too sure about.

Luckily automated provisioning was an opportunity to delve into Azure Functions and Logic Apps, allowing me to dive deeply into the C# and .NET eco-systems again. That's been about 4 months of solid dev work, but even that project is winding down - we're in deployment mode now. It's a product, and it's practically finished.

Within a few weeks my days will consist of preparation of CSV files for consumption within our well built scripts and tools. PowerShell is invoked and monitored, and if I am lucky there will be an exception or connectivity issue to troubleshoot.

Sure, there is consultation with clients, but **I am not a consultant**, I don't want to be => **job title = Developer**. There is a measure of boredom and a general feeling of 'what the hell am I doing this for?' within this type of work. I am loathe to say this but, I feel it's below me. Any SharePoint Administrator with even a little PowerShell knowledge could do what I will be stuck doing.


### A silver lining

I've learned a lot from this last 19 months of SharePoint Development, and I have had the privilege of working with two gentlemen who are at the top of their game globally. I've also been able to refine all of the soft skills needed to thrive in the industry. Time hasn't been wasted.

One thing is abundantly clear. The demand within this small part of the world for custom SharePoint Development is dwindling. It's time to think about the career.

On that note, I've begun to [teach myself C++](https://dreamsof.dev/2019-09-27-learning-the-cpp-basics-1/), it's nice to be a noob again, perhaps that's where my future lies - I love learning.


Thanks for reading.
