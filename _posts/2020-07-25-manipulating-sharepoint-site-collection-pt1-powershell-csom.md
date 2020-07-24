---
layout: post
title: Primer - Manipulate SharePoint Site Collections part 1, PowerShell and CSOM
date: 2020-07-25 +13:00
published: true
category: Development
tags: [sharepoint, powershell, spo, pnp, powershell, csom, primer, entry level]
---

There are a multitude of ways to manipulate SharePoint, I will take the opportunity to give you a primer in connecting to your tenant using SPO and PnP PowerShell, and extending the default functionality with CSOM.

### Prep work

First up, you'll need to update your local machines Execution Policy to allow for these cmdlets to to be executed.

Open your PowerShell console as an administrator and run the following commands:


#### Check and set your machines' execution policy

~~~powershell
> Get-ExecutionPolicy -List

# expected default results:
#
#           Scope ExecutionPolicy
#           ----- ---------------
#   MachinePolicy       Undefined
#      UserPolicy       Undefined
#         Process       Undefined
#     CurrentUser       Undefined
#    LocalMachine       Undefined

> Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope LocalMachine -Force # update your local machine scope to be unrestricted
> Get-ExecutionPolicy -List

# expected results:
#
#           Scope ExecutionPolicy
#           ----- ---------------
#   MachinePolicy       Undefined
#      UserPolicy       Undefined
#         Process       Undefined
#     CurrentUser       Undefined
#    LocalMachine    unrestricted
~~~

If you haven't done so already, you'll need to install the the SharePoint PowerShell cmdlets from [PowerShell Gallery: Microsoft.Online.SharePoint.PowerShell](https://www.powershellgallery.com/packages/Microsoft.Online.SharePoint.PowerShell/).

We will do this via the PowerShell console. Run the following commands:

```powershell
> Install-Module -Name Microsoft.Online.SharePoint.PowerShell -AllowClobber -Force;

> Get-Module -Name *SharePoint.PowerShell -ListAvailable | Select-Object Name,Version;

# expected results:
#
# Name                                   Version    
# ----                                   -------    
# Microsoft.Online.SharePoint.PowerShell [latestVersionNumber]
```

Now you'll need to do the same for [PNP PowerShell](https://www.powershellgallery.com/packages/SharePointPnPPowerShellOnline/), run the following commands:

```powershell
> Install-Module SharePointPnPPowerShellOnline -AllowClobber -Force

> Get-Module -Name SharePointPnPPowerShell* -ListAvailable | Select-Object Name,Version

# expected results:
#
# Name                          Version    
# ----                          -------    
# SharePointPnPPowerShellOnline [latestVersionNumber]
```

**A note on the -AllowClobber switch**

If you import a command with the same name as a command in the current session, the imported command hides or replaces the original commands when -AllowClobber is specified. This goes a long way towards avoiding ambiguity. We'll be using this somewhat regulrarly.

You're now good to continue.


### SPO PowerShell


#### Notes about the SPO cmdlets

Now, we're going to use the SPO cmdlets to connect to the admin admin centre, this is what we use these for, **but please be aware that you can do a lot of harm there, tread carefully**. PNP PowerShell is used for, and is far better at manipulating individual site collections, we'll come onto that soon in the next section.

Please take a moment to familiarise yourself with the documentation for [Connect-SPOService](https://docs.microsoft.com/en-us/powershell/module/sharepoint-online/connect-sposervice?view=sharepoint-ps), specifically:

> Connects a SharePoint Online administrator or Global Administrator to a SharePoint Online connection (the SharePoint Online Administration Center). This cmdlet must be run before any other SharePoint Online cmdlets can run.


#### Connect to the SharePoint Online Administration Center

```powershell
> Connect-SPOService -Url https://[tenantName]-admin.sharepoint.com/ # you'll be prompted for credentials

# you should now be connected to the admin centre

> $sites = Get-SPOSite # get all sites within this tenant

> $sites # display the sites you just retrieved

# example results:
#
# Url                                                Owner                Storage Quota
# ---                                                -----                -------------
# https://[tenantName].sharepoint.com/search                              26214400
# https://[tenantName].sharepoint.com/teams/team1    owner@company.com    26214400
# https://[tenantName].sharepoint.com/teams/team2    owner@company.com    26214400
# https://[tenantName].sharepoint.com/                                    26214400
# https://[tenantName].sharepoint.com/sites/site1                         26214400
# https://[tenantName].sharepoint.com/teams/team3                         26214400
# https://[tenantName].sharepoint.com/sites/site2                         26214400
#
#   remainder elided for brevity
```

You can now continue and work on your tenant using the SPO cmdlets, though remember, be careful! One last thing...

```powershell
> Disconnect-SPOService # always disconnect when you're finished!
```


### PNP PowerShell

Next up, we're going to do something very similar using PNP PowerShell, though this time we will be connecting directly to an existing Site Collection.

```powershell
> Connect-PnPOnline -Url https://[tenantName].sharepoint.com/sites/[siteName] # enter your credentials


# if the previous method fails, you've likely got MFA enabled on your tenant, use this instead

> Connect-PnPOnline -Url https://[tenantName].sharepoint.com/sites/[siteName] -UseWebLogin # this will provide a web login interface

# you should now be connected to your chosen site

> $site = Get-PNPSite # retrieve a reference to the current site and persist it to a variable

> $site

# example results:
#
# Url                                                     CompatibilityLevel
# ---                                                     ------------------
# https://[tenantName].sharepoint.com/sites/[siteName]    15

# now you can do some cool stuff, let's retrieve this site collections' recycle bin items

> $rec = Get-PnPRecycleBinItem

# example results:
#
# Title                  Id       ItemType   LeafName
# -----                  --       --------   --------
# Test Document 1.docx   [guid]   File       Test Document 1.docx
# Test Doc 3.docx        [guid]   File       Test Doc 3.docx
# Test Doc 3.docx        [guid]   File       Test Doc 3.docx
# Test Doc 1.docx        [guid]   File       Test Doc 1.docx
# Test Doc 2.docx        [guid]   File       Test Doc 2.docx
```

You could now continue removing those recycle bin items if you felt like it.


#### SharePoint Client-Side Object Model in PnP PowerShell (CSOM)

I'm now going to demonstrate using [CSOM](https://www.c-sharpcorner.com/article/sharepoint-client-object-modal-csom/#:~:text=Introduction,outside%20without%20using%20web%20services.) while we're still connected to the previous site collection with PNP.

One important part to note is that all of these cmdlets are built using CSOM behind the scenes (both SPO and PnP). CSOM is a derivative of C#, so if you know that language you'll do well here - if not, read along and use some Google-Fu to catch up where necessary.

```powershell
# we should still be connected to a site collection, if not, reconnect using Connect-PnPOnline

# retrieve the context of the connected site
> $ctx = Get-PnPContext

> $ctx

# expected results:
#
# RetryCount                   : 10
# Delay                        : 1000
# PropertyBag                  : {}
# Web                          : Microsoft.SharePoint.Client.Web
# Site                         : Microsoft.SharePoint.Client.Site
# RequestResources             : Microsoft.SharePoint.Client.RequestResources
# FormDigestHandlingEnabled    : True
# ServerVersion                : 16.0.20308.12005
# Url                          : https://[tenantName].sharepoint.com/sites/[siteName]
# ApplicationName              : SharePoint PnP PowerShell Library
# ClientTag                    : PnPPS:2006:Get-PnPRecycleBinItem
# DisableReturnValueCache      : True
# ValidateOnClient             : True
# AuthenticationMode           : Default
# FormsAuthenticationLoginInfo : 
# Credentials                  : Microsoft.SharePoint.Client.SharePointOnlineCredentials
# WebRequestExecutorFactory    : Microsoft.SharePoint.Client.DefaultWebRequestExecutorFactory
# PendingRequest               : Microsoft.SharePoint.Client.ClientRequest
# HasPendingRequest            : True
# Tag                          : 
# RequestTimeout               : 1800000
# StaticObjects                : {[Microsoft$SharePoint$SPContext$Current, Microsoft.SharePoint.Client.RequestContext]}
# ServerSchemaVersion          : 15.0.0.0
# ServerLibraryVersion         : 16.0.20308.12005
# RequestSchemaVersion         : 15.0.0.0
# TraceCorrelationId           : [guid]
```

Great, we've got far more information than we had before. What can we do with it?

Let's try to retrieve all of the Lists that have been provisioned on this Site Collection, this is still just PnP, we'll get back to CSOM soon, I promise:

```powershell
> $lists = Get-PnPList # retrieve all lists on this Site Collection

> $lists

# example results:
#
# Title                               Id       Url                                         
# -----                               --       ---                                         
# _catalogs/hubsite                   [guid]   /sites/[siteName]/_catalogs/hubsite
# appdata                             [guid]   /sites/[siteName]/_catalogs/appdata
# appfiles                            [guid]   /sites/[siteName]/_catalogs/appfiles
#
# -------- elided
#
# Documents                           [guid]   /sites/[siteName]/Shared Documents
#
#   remainder elided for brevity
```

Let's isolate our Shared Documents Library from within our `$lists` object.

```powershell
> $docLib = $lists | Where-Object {$_.Title -eq "Documents"}

> $docLib

# expected results:
#
# Title       Id       Url
# -----       --       ---
# Documents   [guid]   /sites/OrgDemo/Shared Documents
```

We're getting there, I promise.

Let's say that we now need to retrieve all of the Site Content Types that are attached to this Library. Surely we can just do this right?

```powershell
> $docLib.ContentTypes

# An error occurred while enumerating through a collection: The collection has not been initialized. It has not been requested or the request 
has not been executed. It may need to be explicitly requested..
```

Ruh-roh!

What's happened here? The explanation is in the exception output.

> The collection has not been initialized

And:

> It may need to be explicitly requested

**So what do we do?**

This is going to get a little heavy, bare with me.

```powershell
# we've already got our context, but just to be sure...
> $ctx # retrieve it again if it's null with $ctx = Get-PnPContext

# retrieve Content Types from $docLib
> $contentTypes = $docLib.ContentTypes # <- CSOM
> $ctx.Load($contentTypes) # <- CSOM
> $ctx.ExecuteQuery() # <- CSOM
> $contentTypes

# example results:
#
Name                   Id       Group           Description
----                   --       -----           -----------
Document               [guid]   Content Types   Generic Document
Folder                 [guid]   Content Types   Create a new folder
```

CSOM to the rescue! You can take this further by slowly building up the objects you're working with by retrieving all Fields on each Content Type, or whatever else you need to do. Pretty cool huh?

Don't forget to disconnect from your PnP Session!

```powershell
> Disconnect-PnPOnline
```

Let me know if you've got any questions.

Job's another goodun!
