---
layout: post
title: Upgrading SPFx projects to version 1.8
date: 2019-04-09 +13:00
published: true
category: Dev
tags: [spfx, sharepoint, typescript, apis, termstore, improvement]
---


My previous understanding was that separate API requests to the term store were necessary in order to decorate what's returned from the Pages library. I was a noob back then, still am in some respects, but this post highlights that I am still learning and retaining.


### Background

Page items (and list items) using Term Set data in their fields merely return the ID and GUID of the Term that's been assigned when using a Get request. In fact, the Id is returned in the label field. More on that later.

That necessitated queries for the selected terms to decorate the items with the actual human readable label - this data is used to populate the filtering drop down controls.

![SPFx News WebPart improved with filtering controls](/img/spfx-news-improvements.jpg)

As alluded to previously, my focus is near-stock implementations with added functionality. In this particular example, the WebPart looks like the default, with the addition of Filtering controls to aid in end-user digestion of content int heir interest area.

We are currently doing 5x API requests (all "Get").

1) Site Pages query (to retrieve the pages per defined criteria).
2) Term Store query to retrieve all first Filter terms.
3) Term Store query to retrieve all second Filter terms.
4) Profile service query to retrieve Page Author meta data.
5) Search service query to retrieve number of page views.

The process then applies the returned Terms to the respective drop down.

The user can then select combinations of first and second Filter to drill down to the Page item/s they want to view.


### The problem

During work on another WebPart, I learned that that behaviour is actually a [long standing bug that's been in place since the SP 2013 on-prem days](https://social.msdn.microsoft.com/Forums/office/en-US/aa561460-c8d0-4f26-8f28-e77680f42b8b/taxonomy-field-label-when-retrieved-via-rest-is-actually-id?forum=sharepointdevelopment).

> I ran into a strange issue today when using the SharePoint 2013 REST API for Lists with Managed Metadata columns.  For a multi-select taxonomy column, the List service returns Label text as expected.  But if you change that same column to single-select, suddenly an ID is returned in the Label field!

A Google search highlights many such support requests over the last 5 years. Bugs outstanding for that long are *unbelievable* - but it is what it is.


### The work-around

My issue was slightly different though, it was simply that I couldn't force the REST API to decorate the selected item Field Terms witht heir Label, only the Id (there's the similarity).

Using the many suggestions, here's my *modern* work-around using a Post request off of an instance of **sp-pnp-js/Web** within TypeScript, and additionally using a provided CamlQuery object.

~~~ts
  private retrieveItemsWithTermData(listName: string, camlQuery: CamlQuery): Promise<any> {

    if (camlQuery !== null && camlQuery !== undefined) {
      return this.web.lists.getByTitle(listName).getItemsByCAMLQuery(camlQuery, "FieldValuesAsText")
      .then((response: any) => { // note: there is no "result" on this response object so checking whether the request fails isn't possible - you may need to further work around this

        return response;
      }, (err: any): void => {
        console.error(`retrieveItemsWithTermData error encountered: ${err}`);
      }) as Promise<any>;
    } else {
      console.warn(`CamlQuery not supplied`);
      const nullResponse: any = {};

      return nullResponse as Promise<any>;
    }
  }
~~~

This is consumed by using something similar to the following (the CAML is filtering by date and sorting by the same):

~~~ts
  private loadListItemsFromService(listName: string): void {

    const camlQuery: CamlQuery = {};
    camlQuery.ViewXml =
      `<View>
        <Query>
          <Where>
            <Geq>
              <FieldRef Name="MyDateField" />
              <Value>
                <Today IncludeTimeValue="True" OffsetDays="-30" />
              </Value>
            </Geq>
          </Where>
          <OrderBy>
            <FieldRef Name="MyDateField" Ascending="${false}"/>
          </OrderBy>
        </Query>
      </View>`;

    this.retrieveItemsWithTermData(listName, camlQuery)
      .then((response) => { // note: there is no "result" on this response object
        response.forEach((element: any) => {
          console.log(element);
        }
      }, (err: any): void => {
        console.error(`loadListItemsFromService error encountered: ${err}`);
      });
  }
~~~

Check your console; this approach returns data you'd expect for the selected Term/s:
- GUID
- Label (this is the important one, and indeed the element impacted by the bug)

I hope this helps others to not get as frustrated as I did.

Until next time.
