---
layout: post
title: Accessing SharePoint APIs via REST using SP-PNP-JS within the SharePoint Framework
date: 2020-12-09 +13:00
published: false
category: Development
tags: [sharepoint, spfx, react, typescript, rest, sp-pnp-js]
---

I started a new job recently, and I've taken the opportunity to take another look at the APIs and apply some of the knowledge gained over the last 2-3 years, while building a new service layer. The intention for this post is to highlight what I believe to be the simplest and most extensible way to interface with the SharePoint back end services.

I've also had to build another bespoke npm registry hosted on the new company Azure DevOps environment. This gave me an opportunity to take another look at the blog post series I authored back in 2019, which still hold up well; [SPFx and Shared libraries](https://dreamsof.dev/2019-02-15-spfx-projects-and-shared-libraries-part-1/).


## Using SP PNP JS to access the SharePoint APIs

We will go over a few different use cases that we can use sp-pnp-js to solve.

- retrival of SharePoint List Items.
 - filter List items and extract additional information using an odata query.
 - filter List items using CAML a query.
 - separately extracting additional data for items retrieved using CAML query.
- SharePoint User Profile and Group Membership.
- MS Graph API User Profile.


### retrival of SharePoint List Items.


#### filter List items and extract additional information using an odata query.


#### filter List items using CAML a query.


##### separately extracting additional data for items retrieved using CAML query.


### SharePoint User Profile and Group Membership.


### MS Graph API User Profile.


