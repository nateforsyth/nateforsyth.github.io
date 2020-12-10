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


# Using SP PNP JS to access the SharePoint APIs

We will go over a few different use cases that we can use sp-pnp-js to solve.

- Initial dependencies and requirements.
- Retrieval of SharePoint List Items (GET).
 - Filter List items and extract additional information using an odata query.
 - Filter List items using CAML a query.
 - Separately extracting additional data for items retrieved using CAML query.
- SharePoint User Profile and Group Membership.
- MS Graph API User Profile.
- Adding a new SharePoint List item (POST).
- Updating an existing SharePoint List item (MERGE/UPDATE).

## Initial dependencies and requirements

Let's get started. We're obviously going to need an SPFx project to invoke this our REST queries from. If you don't already have one, [scaffold a new SPFx Extension projectt](https://dreamsof.dev/2020-01-09-scaffolding-new-spfx-extension-project/) to keep this as simple as possible.

Serve your extension project and test to ensure it works as expected.

There are only two `npm` dependencies that are required, let's go ahead and install them now.

> npm install sp-pnp-js @microsoft/sp-http --save

We've got everything we need now, let's continue.


## Retrieval of SharePoint List Items

First, add a new folder to the `src` folder of the project called `./src/Services/SharePoint/`.

Next, add a new file called `SharePointInterfaces.ts`, and add content as follows:

```ts
// SharePointInterfaces.ts

import { Web, SharePointQueryableCollection, Item } from "sp-pnp-js";
import { SPHttpClient, SPHttpClientResponse, ISPHttpClientConfiguration, ODataVersion, SPHttpClientConfiguration, HttpClientResponse, ISPHttpClientOptions } from "@microsoft/sp-http"; // not all dependencies are needed yet, they'll be used later

export interface ISharePointService {
    web: Web;
    httpClient: SPHttpClient;
    siteCollectionUrl: string;
        
    getGenericListItemsFromSharePoint(url: string): Promise<any> | undefined;
}

```


Add another new file called `SharePointService.ts`:

```ts
// SharePointService.ts

import { Web, SharePointQueryableCollection, Item } from "sp-pnp-js";
import { IILDSListItem, IILDSSharePointService } from "./Model/ILDSSharePointServiceInterfaces";
import { SPHttpClient, SPHttpClientResponse, ISPHttpClientConfiguration, ODataVersion, SPHttpClientConfiguration, HttpClientResponse, ISPHttpClientOptions } from "@microsoft/sp-http";

export class SharePointService implements ISharePointService {
    private web?: Web;
    private httpClient?: SPHttpClient;
    private siteCollUrl?: string;

    constructor(siteCollUrl?: string, httpClient?: SPHttpClient) {
        if (siteCollUrl !== undefined && httpClient !== undefined) {
            this.web = new Web(siteCollUrl);
            this.siteCollUrl = siteCollUrl;
            this.httpClient = httpClient;
        }
    }

    public getGenericListItemsFromSharePoint(url: string): Promise<any> | undefined {
        if (this.httpClient !== undefined) {
            return this.httpClient.get(url, SPHttpClient.configurations.v1)
                .then((response: HttpClientResponse) => {
                    return response.json()
                        .then((responseJson: any) => {
                            console.log(responseJson);
                            return responseJson;
                        })
                        .catch((err: any) => {
                            console.log(`getGenericListItemsFromSharePoint Error 003\r\n\t$err: {err}`);
                            return err;
                        });
                })
                .catch((err: any) => {
                    console.log(`getGenericListItemsFromSharePoint Error 002\r\n\t$err: {err}`);
                    return err;
                }) as Promise<any>;
        } else {
            console.log(`getGenericListItemsFromSharePoint Warning 001\r\n\thttpClient is undefined`);
            return undefined;
        }
    }
 }

```


### Filter List items and extract additional information using an odata query


### Filter List items using CAML a query


#### Separately extracting additional data for items retrieved using CAML query


## SharePoint User Profile and Group Membership


## MS Graph API User Profile


## POSTing a SharePoint List item


## Updating an existing SharePoint List item (MERGE/UPDATE)


