---
layout: post
title: TypeScript and JavaScript - not playing nicely together in SPFx React
date: 2019-05-17 +13:00
published: true
category: Dev
tags: [javascript, typescript, jquery, spfx, sharepoint framework, sharepoint, react]
---

**For some background**: SharePoint Search is in a state of flux. Microsoft have been updating the DOM of modern SharePoint regularly over the last 6 months.

We have (had) a solution whereby we intercept the user's actions with the Search box on modern SharePiint Sites, note the arrow in the image below:

![SharePoint Modern Search - the old UX](/img/SearchAndTypeScript01.png)

As of 17th May 2019, Microsoft have continued to obfuscate their Search box, now moving it into a protected part of the DOM.

This is only a problem for "First release" clients. Here's what they're seeing:

![SharePoint Modern Search - the updated UX](/img/SearchAndTypeScript02.png)

Here's what they have done to the DOM elements (note the class names - these change every time they update making pattern matching a nightmare):

![SharePoint Modern Search - the updated DOM](/img/SearchAndTypeScript05.png)

We have a working solution to manipulate the DOM elements, it hasn't changed from our previous implementation.

This example forms a part of an [SPFx](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/sharepoint-framework-overview) [Extension](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/extensions/overview-extensions) project embedded into SharePoint as an [Application Customiser](https://docs.microsoft.com/en-us/sharepoint/dev/spfx/extensions/get-started/using-page-placeholder-with-extensions). SharePoint [PnP PowerShell](https://docs.microsoft.com/en-us/powershell/module/sharepoint-pnp/?view=sharepoint-ps) is used to programmatically [add the customisation](https://docs.microsoft.com/en-us/powershell/module/sharepoint-pnp/add-pnpcustomaction?view=sharepoint-ps).

## I've encountered an issue whereby I am struggling to invoke functionality from within a JS file

This particular issue comes about due to our requirement to use a vanilla JS file to isolate Search based DOM elements due to restrictions we have with specific versions of all frameworks for specific clients.

Functionality has been added to a React component (child) that currently only renders an empty div element. It keeps the state separated from the parent.

### Failing to invoke JavaScript from within TypeScript

Enough background.

**Here's some very basic functionality to highlight the problem:**

~~~js
// SearchOverrideJs.js
var SearchOverrideJs = {
  findElements: function() {
    console.log("SearchOverrideJs.findElements invoked");
  }
}
~~~

**I've created typings to allow me to use the JS file within TypeScript:**

~~~ts
// SearchOverrideJs.d.ts
declare module "searchOverrideJs" {
  public class SearchOverrideJs {
    public findElements(): void;
  }
  var searchOverrideJs: SearchOverrideJs;
  export default searchOverrideJs;
}
~~~

**Here's my config file (much elided for brevity):**

~~~json
// config.json
{
  "$schema": "https://dev.office.com/json-schemas/spfx-build/config.2.0.schema.json",
  "version": "2.0",
  "bundles": {
    // content elided for brevity
  },
  "externals": {
    "searchOverrideJs": {
      "path": "./src/extensions/solarCustomSearch/components/SearchOverrideJs.js",
      "globalName": "SearchOverrideJs"
    }
  }
}
~~~

**Here's my tsconfig file:**

~~~json
// tsconfig.json
{
  "compilerOptions": {
    "outDir": "./built",
    "target": "es5",
    "forceConsistentCasingInFileNames": false,
    "module": "commonjs",
    "jsx": "react",
    "declaration": true,
    "sourceMap": true,
    "experimentalDecorators": true,
    "skipLibCheck": true,
    "typeRoots": [
      "./node_modules/@types",
      "./node_modules/@microsoft"
    ],
    "types": [
      "es6-promise",
      "webpack-env"
    ],
    "lib": [
      "es6",
      "dom",
      "es2015.collection",
      "dom.iterable"
    ]
  }
}
~~~

**Here's the .tsx file that I am attempting to consume the .js file within:**

~~~ts
// SearchOverride.tsx
import * as React from "react";
import searchOverrideJs from "searchOverrideJs"; // specified within config.json
export interface ISearchOverrideProps {
  parentHasLoaded: boolean;
}
export interface ISearchOverrideState {
  btnElsIdentified: boolean;
  inputElsIdentified: boolean;
}
export class SearchOverride extends React.Component<ISearchOverrideProps, ISearchOverrideState> {
  constructor(props: ISearchOverrideProps) {
    super(props);

    this.state = {
      btnElsIdentified: false,
      inputElsIdentified: false
    };
  }
  public render(): React.ReactElement<ISearchOverrideProps> {

    console.log(`SearchOverride.render invoked...`); // this outputs to the console

    searchOverrideJs.findElements(); // running this does nothing and I cannot figure out why
    
    // note: this is why we've had to use vanilla JS
    const searchBoxEl = document.getElementById("O365_SearchBoxContainer");
    const searchBoxBtnEls = searchBoxEl.getElementsByTagName("button");
    
    /*
      notes:
        - this outputs the button elements as expected
        - they cannot be iterated over due to version restrictions in place
        - this is due to HtmlCollectionOf<HtmlButtonElement> not having the expected properties available
     */
    console.log(searchBoxBtnEls[0]);

    return <div/>;
  }
}  
~~~

### Chrome Dev Tools

The Network tab shows the SearchOverrideJs.js file has indeed been loaded, showing me that the component is operating as expected.

**Invoking this within the Chrome Console:**

~~~text
SearchOverrideJs.findElements()

// Results in this (showing me that I am indeed importing the file:
VM14888 SearchOverrideJs.js:3 SearchOverrideJs.findElements invoked
~~~

I'm getting myself into a flap because of something so seemingly benign.

How can I invoke my .JS file within TypeScript?

Appreciate any and all help :)
