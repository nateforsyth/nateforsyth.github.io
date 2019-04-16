---
layout: post
title: Removing SharePoint Page Sections using PnP
date: 2019-04-17 +13:00
published: true
category: Provisioning
tags: [powershell, pnp, sharepoint, windows]
---


A scenario came up at work today which I actually had an answer for, and I thought to myself *wow, this might make a nice quick blog post*... so here goes.


### How can you programmatically remove a section from a SharePoint page?

Start up your favourite terminal; I prefer PowerShell using PnP cmdlets where applicable.

1. Connect to the site where the page is located.

~~~powershell
Connect-PnPOnline -Url https://[tenantName].sharepoint.com/sites/intranet
~~~

2. Next up, retrieve the page.

~~~powershell
$pg = Get-PnPClientSidePage -Identity _sectionTest
~~~

3. Now we're going to view the sections on the page

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

Page sections are based upon a zero indexed array, ignore the "Order" property in the results; this will send you in the wrong direction.

4. Remove the desired page section.

~~~powershell
$pg.Sections.RemoveAt(0)
~~~

I don't know whether it's a bug, but if you don't publish the page at this point the changes will not persist. I liken this to where you need to invoke ExecuteQuery in CSOM.

5. Publish the page
6. Retrieve a fresh instance of the page.

~~~powershell
Set-PnPClientSidePage -Identity $pg -Publish
$pg = Get-PnPClientSidePage -Identity _sectionTest
~~~

7. View the page sections again, noting the results where the page section @ position 0 was removed.

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

8. Don't forget to disconnect from PnP when you're done.

~~~powershell
Disconnect-PnPOnline
~~~

#### Job done
