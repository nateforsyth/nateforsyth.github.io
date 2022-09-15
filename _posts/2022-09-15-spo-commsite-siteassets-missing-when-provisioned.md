---
layout: post
title: SPO fresh Communication Site missing Site Assets Library when provisioned, a work-around
date: 2022-09-15 +13:00
published: true
category: sharepoint
tags: [sharepoint, delayed processing, queue, assets missing until used]
---

I've observed today that SharePoint Communication Sites are no longer being provisioned with the default Site Assets Library.

This is however temporary, because as soon as you add a News Post and upload a file from your device, SharePoint creates the missing folder and saves the content from the Page as you'd expect.


# Why is this a problem?

There are scenarios where we as developers rely upon the existence of Libraries for the products we work upon. Changes made by Microsoft can cause headaches for us, especially when we weren't aware in the first place.


# A fix

Thankfully, there are 3 fixes, the first is the simplest; add a News post and upload an image from your local environment. The folder will be created per usual.

What about if you need the site to stay clean? Activate the missing Site Collection Feature; Video and Rich Media.

Or run PowerShell to do the same thing.

~~~ps
$siteURL = "https://[tenantName].sharepoint.com/sites/[siteName]"; 
Connect-PnPOnline -url $siteURL -Interactive;
$Web = Get-PnPWeb;
$Web.Lists.EnsureSiteAssetsLibrary();
Invoke-PnPQuery;
~~~

The end-result is that you'll have a Site Assets folder in your targeted site and you'll be able to target it programmatically.

Job's a good'un.
