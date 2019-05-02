---
layout: post
title: Building a shared Library - SPFx projects and Shared Libraries - part deux
date: 2019-02-21 +13:00
published: true
category: Dev
tags: [spfx, sharepoint, shared libraries, spfx extensions, spfx webparts, typescript, npm]
---

When we left off [last time](https://nateforsyth.github.io/2019-02-15-spfx-projects-and-shared-libraries-part-1/), I'd highlighted an issue I had faced whereby there was no supported way to build a shared code package _within_ the SharePoint Framework (SPFx). I closed with a very brief allusion to the way that I structured a solution to that problem.

Here are links to all of the posts on this topic:
- [SPFx projects and Shared Libraries - numero uno](https://dreamsof.dev/2019-02-15-spfx-projects-and-shared-libraries-part-1/)
- Building a shared Library - SPFx projects and Shared Libraries - part deux
- [Building a shared Library - SPFx projects and Shared Libraries - iii](https://dreamsof.dev/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-3/)

It's now time to continue...

## What this post will cover.

- A litle more about what I do.
- What the actual requirements of the Shared Code project were.
- What was already tried.
- The type of project chosen to meet those needs, and why.
- ~~Scaffolding and configuration of the project.~~
- ~~Early issues that were encountered.~~
- ~~building the project.~~
- ~~bundling the project.~~
- ~~using the project.~~

## A litle more about what I do.

I have spent the last 9-10 months working on a productised wrapper of sorts, which is, in the simplest terms possible, intended to simplify the process of provisioning modern SharePoint experiences including the manipulation of the Term Store, Fields, Content Types, Lists, Libraries, etc. PowerShell PnP scripts are used to automate nearly every part of the process.

A lot of work has gone into creating a unified menu structure that can span across multiple site collections on a tenant, as well as multiple custom Web Parts intended to extend upon the OOTB Microsoft offerings revolving around providing a better UX, as well as introduct completely new functionality to improve end user workflows and processes.

The SharePoint Framework (SPFx) was chosen for the majority of the custom UI work we've implemented, and even though it is now quite a mature offering, it's provided its' fair share of challenges for this [still relatively new developer](https://nateforsyth.github.io/2019-02-14-from-dot-net-to-sharepoint/).

Our solution consists of many individual SPFx projects bundled as Visual Studio Code workspaces, each of which _used to_ have their own divergent SharePoint API wrapper implementations - hence the whole point of this post.


## What the actual requirements of the Shared Code project were.

The requirements were very simple on the surface:

- One shared code project for all service implementations including API wrappers.
- Centralised storage of a built package accessible by any legacy or new SPFx project we use.
- The end package and where it was stored had to be private to the company.
- The chosen solution _should_ use our existing resources and not unnecessarily cost more money.

It's probably worth a few more notes on each of the requirements.

### One shared code project for all service implementations including API wrappers.

It was abundantly clear that the divergent code for our API wrappers, Utilities and other miscellaneous functionality was going to become a problem; having copies of SPOListManipulationService within 3 different Web Part projects, differing only by the fields that were being returned in the shaped data, quickly became a pain to maintain when changes were made in one and not another.

Let's not even talk about when dependencies change or Microsoft makes an update.

### Centralised storage of a built package accessible by any legacy or _new_ SPFx project we use.

We quite simply wanted an [npm](https://www.npmjs.com/) like experience, without having to use a private npm account. We needed to have a single package that we could rebuild as often as necessary, containing our shared code assets, that was always accessible.

As mentioned below, we've got Azure DevOps and are working within Microsoft stacks; any centralised solution needed to leverage what we've already got.

### The end package and where it was stored had to be private to the company.

We've done a significant amount of development on our custom solutions, and keeping our IP out of the public domain is of top priority.

### The chosen solution _should_ use our existing resources and not unnecessarily cost more money.

We're a relatively new company and it's logical to keep costs as low as possible. In addition, we have the new equivalent of Visual Studio Online Enterprise subscriptions and MSDN benefits; it makes sense to leverage Azure DevOps, our existing Azure subscriptions as well as our existing Office 365 licences as much as possible.

## What was already tried.

My first forays into finding a solution for this problem revolved around using the SharePoint Framework, and building it as a Library. On the surface this appeared to be as simple as creating a new SPFx project and updating the manifest _componentType_ to Library (instead of _WebPart_ or _Extension_).

I used the very good tutorial [Building shared code in SharePoint Framework - revisited](https://blog.mastykarz.nl/building-shared-code-sharepoint-framework-revisited/) by Waldek Mastykarz to get started, but as alluded to in my previous post, this approach isn't supported at the moment.

>While the concept of SharePoint Framework libraries is powerful and encourages reusing common code, at this moment, it isn't supported beyond the local workbench. First of all, the current version of the SharePoint Framework Yeoman generator doesn't support packaging projects that define components. Even if you managed to create an .sppkg file for your library, you will get an error if you try to install it to the App Catalog in your SharePoint tenant, which is required for the library to be correctly loaded by your web parts.

~~What we needed was a production solution, an unsupported solution wasn't going to cut it.~~

Scratch that...

Microsoft has recently announced that [SPFx 1.8 has been released to general availability](https://developer.microsoft.com/en-us/sharepoint/blogs/announcing-the-general-availability-of-sharepoint-framework-1-8/), and with it comes (preview) Library Components.

> A common request for developers creating multiple SharePoint Framework applications is more flexibility for code re-use. In SharePoint Framework 1.8, you can use new preview support for library components (via the plusbeta option), which allow you to create libraries of functions that can be re-used across multiple solutions. With library functionality, create one SharePoint Framework solution with all your main reusable functions – and other components in other solutions can call and re-use them.

Now while this looks great on the surface, digging deeper shows that it's still a local package that must be included with every SPFx component you bundle. Let's not even speculate about the issues you can face when using *preview* functionality within production code.

Not for us then, so let's continue...


## The type of project chosen to meet those needs, and why.

My next course of action was to investigate using a simpler structure. My research kept coming back to a very simple concept; use a TypeScript project.

Further analysis on what was possible highlighted that it _should_ be trivial to package a TypeScript project as an npm package, and persist it centrally. I still hadn't realised I could use Azure DevOps.

TypeScript was settled upon due to my comfort with the language, as well as the development environment. It's also what is used within our SPFx projects. It was a no-brainer.


This post has ended up much longer than I'd hoped, I'll continue in part 3, where we'll cover...

- Scaffolding and configuration of the project.
- Early issues that were encountered.
- building the project.

## Until next time...

