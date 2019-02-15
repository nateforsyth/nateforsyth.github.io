---
layout: post
title: SPFx projects and Shared Libraries - numero uno
date: 2019-02-15 +13:00
published: false
category: Dev
tags: [spfx, sharepoint, sharedlibraries, spfxextensions, spfxwebparts, typescript, npm]
image: /img/hello_world.jpeg
---

The SharePoint Framework is **a complicated beast**, and there are a multitude of concepts that [this relatively new developer](https://nateforsyth.github.io/2019-02-14-from-dot-net-to-sharepoint/) has been struggling to understand.

I'm getting there though!

## Just create a library and reference it in all your projects yo!

.NET developers will all be familiar with the simplicity of writing a .dll file within C#, and simply referencing that assembly within any required project (gross generalisations notwithstanding).

Developers in all major languages and frameworks enjoy the same freedoms in 2019, Right? Right???

I began scratching the surface, and boy was I surprised...

## Shared Libraries aren't supported within the SharePoint Framework - wait, what???

Once you've spent a few months within the [SharePoint Framework](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/sharepoint-framework-overview) you'll no doubt become familiar with what I have; **there's no _supported_ way to share your code between multiple projects**.

You'll have:  
* Multiple projects.  
* Multiple copies of _similar_ code implementations, be they utility functions, service wrappers for the various SharePoint APIs, or SharePoint Search query builders.  
* Projects near identical utility functionality that _really should be shared_.

There are many people much smarter than I am, that much is obvious. Some have even blogged about this very topic of course, but from the perspective of a Production solution, I've found what I've been reading to be either:  
* [Overly generalised for production use](https://disq.us/url?url=https%3A%2F%2Fpaulryan.com.au%2F2017%2Fspfx-packaging-sharing-code-web-parts-extensions%2F%3AX9WxEr9F3Mfr4h8DWkrfZXXmG64&cuid=4032940) and unacceptable for our clients:  
  >So take my word for it, add all your SPFx components to a single package and create multi-component bundles  
  
  Our clients pay only for specific component bundles. Bundling them all together opens us up to having to contractually support a component they've not paid for, but have managed to access through potential user error.
* Or [working perfectly](https://blog.mastykarz.nl/building-shared-code-sharepoint-framework-revisited/#comment-4309760993) albeit not officially supported:  
  >The feature should be coming shortly. No public ETA has been shared but it shouldn't take much longer.
  
I've used up a huge amount of my product development time this month on this topic, and have learned a lot from the multitude of articles I've read through, including the ones previously linked.

## The holy grail?

I very much doubt it, but I am very happy with what I've implemented.

The solution works very well for our needs and has allowed us to build a shared code project that has been deployed independently of our production SPFx Web Part and Extension project**s**.

The shared project is built using Azure Pipelines and published to an Azure Artifacts feed. Configuring the feed with upstream sources enabled me to use that feed as an <b>npm</b> Registry replacement.

## The end result

With this decoupling comes much better code re-use and complete [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) from the consuming projects. This separation has been at the behest of ease of debugging though - I'm still trying to figure out how best to handle this outside of Unit Testing.

The end result is the ability to simply <i>npm install project_name@latest</i> and access the shared code project including typings directly, along with all of the other lovely npm functionality that comes with an implementation such as this.

This was a long process and there are many steps that I've not even mentioned in this post. Check back for part two where I will go into further detail about how this was implemented, consumed within our SPFx projects, and configured within Azure DevOps CI to build and publish the project...

When I get around to writing it that is!
