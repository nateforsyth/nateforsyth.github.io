---
layout: post
title: Retrieving SharePoint Choice Field options in SPFx using REST
date: 2019-04-23 +13:00
published: true
category: Dev
tags: [typescript, sharepoint, spfx, webparts, choice field options, rest]
---

I came across a scenario recently whereby I needed to retrieve all choice field options from a specific field before a specific SPFx WebPart had loaded onto the page. The reason for this was that I had to pre-populate the Property Pane configuration with the choices to allow for the end-user to select a specific option for List filtering purposes.

I came across a Stack Overflow request for SharePoint 2013 with [a specific answer](https://sharepoint.stackexchange.com/a/228022/82938) that indeed still works, however I had no intention of either copy/pasting the solution, [nor using jQuery](https://dreamsof.dev/2019-04-21-weaning-myself-off-of-jquery.md/), so I set about using the same end-point with the far more modern [es6-promise](https://www.npmjs.com/package/es6-promise) functionality.

I have the following:
- a list called **MyCoolList**.
- which includes a Choice field called **ItemCategory**.


### Retrieving your required EntitySet identifier

As mentioned within the [answer to the historical question](https://sharepoint.stackexchange.com/a/228022/82938), you must first find out what your EntitySet identifier is.

In practice this is really simple to do, even within your browser (I'm using Chrome). We simply hit the following end-point by pasting it into the address bar:

~~~text
// This end-point provides all EntitySet records on the specified site
https://[tenantName].sharepoint.com/sites/[siteName]/_vti_bin/listdata.svc
~~~

This gives us a result similar to this:

~~~xml
<service xmlns:atom="http://www.w3.org/2005/Atom" xmlns:app="http://www.w3.org/2007/app" xmlns="http://www.w3.org/2007/app" xml:base="https://[tenantName].sharepoint.com/sites/[siteName]/_vti_bin/listdata.svc/">
  <workspace>
    <atom:title>Default</atom:title>
      /* other collections elided for brevity */
      <collection href="MyCoolListItemCategory">
        <atom:title>MyCoolListItemCategory</atom:title>
      </collection>
      /* other collections elided for brevity */
  </workspace>
</service>
~~~

Now, I know from what I've learned recently that my EntitySet identifier is simply the internal List Name and internal Field Name concatenated together. I could have simply done that and skipped the above step, but one line of text does not a good blog post make (you OK there Yoda?).

Here it is anyway:

~~~text
list = MyCoolList
field = ItemCategory

EntitySet = MyCoolListItemCategory
~~~

Now that we've got that (either by using the grey-matter or using the end-point), we can simply hit the end-point again making the following minor change.

~~~text
https://[tenantName].sharepoint.com/sites/[siteName]/_vti_bin/listdata.svc/[EntitySet]

// or in this scenario:
https://[tenantName].sharepoint.com/sites/[siteName]/_vti_bin/listdata.svc/MyCoolListItemCategory
~~~

This gives us an XML response including all of our required Choice field Options (which I won't paste here to maintain some semblence of brevity).

Job done right? No, of course not, we need to be able to use this data, so we need to replicate what we've just done programmatically.


### Assumptions have been made

I'm going to make some assumptions so that I don't need to step you through scaffolding a WebPart before we can get started.

- you already have a WebPart project that you are going to retrofit this functionality into.
 - if not, hit up [this basic WebPart creation tutorial](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/build-a-hello-world-web-part).
- you know how to retrieve your absolute site collection URL from the WebPart Context.
 - if not, have a read of [this Stack Overflow post](https://sharepoint.stackexchange.com/questions/211810/how-to-retrieve-pagecontext-in-spfx).
- you know how to run your WebPart on the Workbench.
 - if not, refer to [the preview section of the tutorial](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/web-parts/get-started/build-a-hello-world-web-part#preview-the-web-part).


### Retrieve all EntitySet records

Add the following method.

~~~ts
private retrieveEntitySetRecords(siteColUrl: string): void {
  const endPoint: string = `${siteColUrl}/_vti_bin/listdata.svc`;

  this.context.spHttpClient
    .get(endPoint, SPHttpClient.configurations.v1)
    .then((response: HttpClientResponse) => {
      if (response.ok) {
        response.json()
          .then((jsonResponse: JSON) => {
            console.log(jsonResponse);
          }, (err: any): void => {
            console.warn(`Failed to fulfill Promise\r\n\t${err}`);
          });
      } else {
        console.warn(`EntitySet interrogation failed.`);
      }
    });
}
~~~

Righty, now it's time to actually retrieve the EntitySet records like we did previously using the browser.

Update the WebParts' render method like so.

~~~ts
public render(): void {
  const siteColUrl: string = this.context.pageContext.site.absoluteUrl; // if you don't understand this, read the link previously mentioned under "Assumptions have been made"
  
  // invoke the EntitySet retrieval
  this.retrieveEntitySetRecords(validatedSiteColUrl);
  
  // the rest of the method has been elided for brevity
}
~~~

Using gulp, invoke your Workbench. Add your WebPart to the Workbench page, open your dev tools, and all going well you'll see something similar to this:

~~~json
{d: {…}}
  d:
    EntitySets: Array(49)
      // previous elided for brevity
      45: "MyCoolListItemCategory"
      // remainder elided for brevity
      length: 49
    __proto__: Array(0)
  __proto__: Object
__proto__: Object
~~~

Note that **MyCoolListItemCategory** is within the json object returned. Now we can add that to our end-point.

Add another method, this time to retrieve and display all Choice field Options within the dev console:

~~~ts
private retrieveAllChoicesFromListField(siteColUrl: string, entitySetName: string): void {
  const endPoint: string = `${siteColUrl}/_vti_bin/listdata.svc/${entitySetName}`;

  this.context.spHttpClient
    .get(endPoint, SPHttpClient.configurations.v1)
    .then((response: HttpClientResponse) => {
      if (response.ok) {
        response.json()
          .then((jsonResponse: JSON) => {
            for (const result of jsonResponse["d"]["results"]) {
              console.log(result["Value"]);
            }
          }, (err: any): void => {
            console.warn(`Failed to fulfill Promise\r\n\t${err}`);
          });
      } else {
        console.warn(`List Field interrogation failed; likely to do with interrogation of the incorrect listdata.svc end-point.`);
      }
    });
}
~~~

Again, update the render method of the WebPart as follows.

~~~ts
public render(): void {
  const siteColUrl: string = this.context.pageContext.site.absoluteUrl;
  
  // invoke the EntitySet retrieval
  this.retrieveEntitySetRecords(validatedSiteColUrl);
  
  // invoke the retrieval of our Choice field Options
  this.retrieveAllChoicesFromListField(validatedSiteColUrl, "MyCoolListItemCategory");
  
  // the rest of the method has been elided for brevity
}
~~~

Save, then refresh the browser page that has the Workbench open, and note the results where we should have received something similar to the following within the dev tools:

~~~json
{__metadata: {…}, Value: "Branch Maps"}
  Value: "Branch Maps"
  __metadata: {uri: "https://[tenantName].sharepoint.com/sites/orga….svc/MyCoolListItemCategory('Branch%20Maps')", type: "Microsoft.SharePoint.DataService.WayfindingLinksLinkCategoryValue"}
  __proto__: Object
{__metadata: {…}, Value: "Business Units"}
  Value: "Business Units"
  __metadata: {uri: "https://[tenantName].sharepoint.com/sites/orga…c/MyCoolListItemCategory('Business%20Units')", type: "Microsoft.SharePoint.DataService.WayfindingLinksLinkCategoryValue"}
  __proto__: Object
// remainder elided for brevity
~~~

We can now go about building a suitable data structure by iterating over **jsonResponse["d"]["results"]** and processing however we see fit by refactoring the **retrieveAllChoicesFromListField** method, something similar to the following simple console output:

~~~ts
.then((jsonResponse: JSON) => {
  let optionCount: number = 0;
  for (const result of jsonResponse["d"]["results"]) {
    console.log(`Option ${optionCount++}: ${result["Value"]}`);
  }
}
~~~

Job done, hope this helps someone.
