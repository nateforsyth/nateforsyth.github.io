---
layout: post
title: Building a shared Library - SPFx projects and Shared Libraries - part deux
date: 2019-02-21 +13:00
published: false
category: Dev
tags: [spfx, sharepoint, sharedlibraries, spfxextensions, spfxwebparts, typescript, npm]
---

When we left off [last time](https://nateforsyth.github.io/2019-02-15-spfx-projects-and-shared-libraries-part-1/), I'd highlighted an issue I had faced whereby there was no supported way to build a shared code package _within_ the SharePoint Framework (SPFx). I closed with a very brief allusion to the way that I structured a solution to that problem.

It's now time to continue...

## What this post will cover.

- A litle more about what I do.
- What the actual requirements of the Shared Code project were.
- The type of project chosen to meet those needs, and why.
- Scaffolding and configuration of the project.
- Early issues that were encountered.
- building the project.

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



### Centralised storage of a built package accessible by any legacy or new SPFx project we use.



### The end package and where it was stored had to be private to the company.

We've done a significant amount of development on our custom solutions, and keeping our IP private is the top priority.

### The chosen solution _should_ use our existing resources and not unnecessarily cost more money.

We're a relatively new company and it's logical to keep costs as low as possible. In addition, we have the new equivalent of Visual Studio Online Enterprise subscriptions and MSDN benefits; it makes sense to leverage Azure DevOps, our existing Azure subscriptions as well as our existing Office 365 licences as much as possible.


## The type of project chosen to meet those needs, and why.




## Scaffolding and configuration of the project.




## Early issues that were encountered.




## building the project.




