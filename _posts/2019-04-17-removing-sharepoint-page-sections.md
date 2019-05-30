---
layout: post
title: Removing SharePoint Page Sections using PnP PowerShell
date: 2019-04-17 +13:00
published: true
category: Provisioning
tags: [powershell, pnp, sharepoint, windows]
---


A scenario came up at work today which I actually had an answer for, and I thought to myself *wow, this might make a nice quick blog post*... so here goes.


### How can you programmatically remove a section from a SharePoint page?

Start up your favourite terminal; I prefer PowerShell using PnP cmdlets where applicable.


#### Connect to the site where the page is located.

~~~powershell
Connect-PnPOnline -Url https://[tenantName].sharepoint.com/sites/intranet
~~~

Enter your credentials. You're now connected.


#### Next up, retrieve the page.

~~~powershell
$pg = Get-PnPClientSidePage -Identity _sectionTest
~~~

Got the page? Now time for the sections.


#### View the sections on the page

~~~powershell
$pg.Sections

# results

Type          : TwoColumn
Order         : 1
Columns       : {OfficeDevPnP.Core.Pages.CanvasColumn, OfficeDevPnP.Core.Pages.CanvasColumn}
Page          : OfficeDevPnP.Core.Pages.ClientSidePage
Controls      : {}
DefaultColumn : OfficeDevPnP.Core.Pages.CanvasColumn

Type          : ThreeColumn
Order         : 2
Columns       : {OfficeDevPnP.Core.Pages.CanvasColumn, OfficeDevPnP.Core.Pages.CanvasColumn, 
                OfficeDevPnP.Core.Pages.CanvasColumn}
Page          : OfficeDevPnP.Core.Pages.ClientSidePage
Controls      : {}
DefaultColumn : OfficeDevPnP.Core.Pages.CanvasColumn

Type          : OneColumnFullWidth
Order         : 3
Columns       : {OfficeDevPnP.Core.Pages.CanvasColumn}
Page          : OfficeDevPnP.Core.Pages.ClientSidePage
Controls      : {}
DefaultColumn : OfficeDevPnP.Core.Pages.CanvasColumn
~~~

Page sections are based upon a zero indexed array, ignore the "Order" property in the results; there are 3 here, between index 0 and 2.


#### Remove the desired page section.

~~~powershell
$pg.Sections.RemoveAt(0)
~~~

I don't know whether it's a bug, but if you don't publish the page at this point the changes will not persist. I liken this to where you need to invoke ExecuteQuery in CSOM.


#### Publish the page and retrieve a fresh instance of the page.

~~~powershell
Set-PnPClientSidePage -Identity $pg -Publish
$pg = Get-PnPClientSidePage -Identity _sectionTest
~~~

You now have a fresh instance of the page to interrogate.

#### View the page sections again.

~~~powershell
$pg.Sections

# results

Type          : ThreeColumn
Order         : 1
Columns       : {OfficeDevPnP.Core.Pages.CanvasColumn, OfficeDevPnP.Core.Pages.CanvasColumn, 
                OfficeDevPnP.Core.Pages.CanvasColumn}
Page          : OfficeDevPnP.Core.Pages.ClientSidePage
Controls      : {}
DefaultColumn : OfficeDevPnP.Core.Pages.CanvasColumn

Type          : OneColumnFullWidth
Order         : 2
Columns       : {OfficeDevPnP.Core.Pages.CanvasColumn}
Page          : OfficeDevPnP.Core.Pages.ClientSidePage
Controls      : {}
DefaultColumn : OfficeDevPnP.Core.Pages.CanvasColumn
~~~

Note the results where the page section at position 0 was removed leaving only two.

Refresh your page in your browser and confirm.

#### Don't forget to disconnect from PnP when you're done.

~~~powershell
Disconnect-PnPOnline
~~~


Job done, hope this helps someone.
