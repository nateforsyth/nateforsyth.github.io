---
layout: post
title: SPFx projects and Shared Libraries - numero uno
date: 2019-02-15 +13:00
published: true
category: Dev
tags: [spfx, sharepoint, shared libraries, spfx extensions, spfx webparts, typescript, npm]
---

The SharePoint Framework is **a complicated beast**, and there are a multitude of concepts that [this relatively new developer](https://nateforsyth.github.io/2019-02-14-from-dot-net-to-sharepoint/) has been struggling to understand.

I'm getting there though!

## Just create a library and reference it in all your projects yo!

.NET developers will all be familiar with the simplicity of writing a .dll file within C#, and simply referencing that assembly within any required project (gross generalisations notwithstanding).

Developers in all major languages and frameworks enjoy the same freedoms in 2019, Right? Right???

I began scratching the surface, and boy was I surprised...

## Shared Libraries aren't supported within the SharePoint Framework - wait, what???

Once you've spent a few months within the [SharePoint Framework](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/sharepoint-framework-overview) you'll no doubt become familiar with what I have; **there's no _supported_ way to share your code between multiple projects** (yet).

You'll have:  
* Multiple projects.  
* Multiple copies of _similar_ code implementations, be they utility functions, service wrappers for the various SharePoint APIs, or SharePoint Search query builders.  
* Projects with near identical utility functionality that _really should be shared_.

There are many people much smarter than I am, that much is obvious. Some have even blogged about this very topic of course, but from the perspective of a Production solution, I've found what I've been reading to be either:  
* [Overly generalised for production use](https://disq.us/url?url=https%3A%2F%2Fpaulryan.com.au%2F2017%2Fspfx-packaging-sharing-code-web-parts-extensions%2F%3AX9WxEr9F3Mfr4h8DWkrfZXXmG64&cuid=4032940) and unacceptable for _our_ clients:  
  >So take my word for it, add all your SPFx components to a single package and create multi-component bundles  
  
  Our clients pay only for specific component bundles. Bundling them all together opens us up to having to contractually support a component they've not paid for, but have managed to access through potential user error.
* Or [working perfectly](https://blog.mastykarz.nl/building-shared-code-sharepoint-framework-revisited/#comment-4309760993) albeit not officially supported:  
  >The feature should be coming shortly. No public ETA has been shared but it shouldn't take much longer.
  
I've used up a huge amount of my product development time this month on this topic, and have learned a lot from the multitude of articles I've read through, including the ones previously linked.

## The holy grail?

I very much doubt it, but I am very happy with what I've implemented.

We now have a simple shared TypeScript _library_ project that is built using Azure Pipelines CI and published to an Azure Artifacts feed. Configuring the feed with upstream sources enabled me to use that feed as an <b>npm</b> Registry replacement.

We can simply <i>npm install project_name@latest</i> and access the _private_ shared code project including typings directly, along with all of the other lovely npm functionality that comes with an implementation such as this.

The solution works very well for our needs and has allowed us to build a shared code project that has been deployed independently of our production SPFx Web Part and Extension project**s**.

## Has there been any tangible benefit from this exercise?

Absolutely! Though I am not sure that this approach will work for everyones' use case.

With this decoupling comes much better code re-use and complete [separation of concerns](https://en.wikipedia.org/wiki/Separation_of_concerns) from the consuming projects. This separation has been at the behest of ease of debugging though - I'm still trying to figure out how best to handle this outside of Unit Testing.

This was a long process and there are many steps that, I'm sure you can tell, have not even been mentioned in this post. Check back for new posts where I will go into detail about how this was implemented, configured within Azure DevOps CI to build and publish the project package, and consumed within our SPFx projects...

...when I get around to writing them that is...

Here are links to all of the posts on this topic:
- SPFx projects and Shared Libraries - numero uno
- [Building a shared Library - SPFx projects and Shared Libraries - part deux](https://dreamsof.dev/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-2/)
- [Building a shared Library - SPFx projects and Shared Libraries - iii](https://dreamsof.dev/2019-04-06-building-shared-library-spfx-projects-and-shared-libraries-part-3/)
- [Building a shared Library - SPFx projects and Shared Libraries - May the 4th be with you ;)](https://dreamsof.dev/2019-05-04-building-shared-library-spfx-projects-and-shared-libraries-part-4/)
