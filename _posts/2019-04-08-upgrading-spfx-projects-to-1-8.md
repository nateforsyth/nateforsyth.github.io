---
layout: post
title: Upgrading SPFx projects to version 1.8
date: 2019-04-08 +13:00
published: true
category: Dev
tags: [spfx, upgrade, sharepoint, spfx 1.8, typescript, npm, dependency hell]
---


As you're no doubt already aware, Microsoft announced the GA release of [version 1.8 of the SharePoint Framework](https://developer.microsoft.com/en-us/sharepoint/blogs/announcing-the-general-availability-of-sharepoint-framework-1-8/) in the middle of last month. If you've not seen it, [here's a link to the release notes](https://github.com/SharePoint/sp-dev-docs/wiki/SharePoint-Framework-v1.8-release-notes) for your perusal.

Cool beans... so what now?


### Time to upgrade your projects

With early adoption of general availability comes the usual slew of undocumented errors, but before we can get into that we actually have to run through the process of updating an existing project to SPFx v1.8 - thankfully the PnP team have provided us with some rather sweet functionality within the [PnP Office 365 CLI tool](https://pnp.github.io/office365-cli/).


### Let's get started.

First up, we need to install the Office 365 CLI tool, do so by adding it globally:

```powershell
npm i -g @pnp/office365-cli

**or:**

yarn global add @pnp/office365-cli
```

Next, we need to find out what needs to be updated in our SPFx project to bring it up to date with the latest version of the framework.

Open a PowerShell window, navigate to the project directory you want to update, and run the following command:

~~~powershell
o365 spfx project upgrade --output md > spfx_upgrade_report.md
~~~

What the above command does is interrogate your existing project structure and provides you with an easy to follow step-by-step guide to updating your project to SPFx v1.8; here's an example.

![Example of SPFx 1.8 upgrade tasks](/img/spfx-update-18.jpg)


### Running the actual upgrade

The process of updating your project can be automated, however I would not recommend doing so. Indeed, if you blindly update everything as advised you'll inevitably encounter many issues on first build.

Instead, applying some grey matter to the process will give you a much better update experience - including a very good chance of a successful first build.

It's much better to run through the report and manually update the packages and configuration files while monitoring what else is happening to your project.

Running npm/yarn updates is a trivial task if you're experienced in doing so, but you've still got to be careful.


### My particular Gotchas

In saying all of the above, I was caught out expecting everything to go swimmingly - it didn't, though I was able to resolve the problems I encountered by either rolling back the changes, installing different versions or outright ignoring the suggestions.

These are what I encountered.
- tslint.json
  - member-access
- package-solution.json
  - isDomainIsolated
- tsconfig.json
  - es6-promise
- WebPart Property Pane
  - property configurations


#### tslint.json

##### member-access

What I found was that, at least with the way that we are consuming our own Libraries, the [TSLint member-access Rule](https://palantir.github.io/tslint/rules/member-access/) was causing failed builds - not just warnings but complete failures.

I don't know why this was, but the fix was to simply not enforce the rule. While you're at it, you should have been directed to update the "extends" property, if not - do so now.

~~~json
{
    "defaultSeverity": "error",
    "extends": "@microsoft/sp-tslint-rules/base-tslint.json",
    "jsRules": {},
    "rules": {
      "member-access": false,
      // other rules elided for brevity
    },
    "rulesDirectory": []
}
~~~


#### package-solution.json

##### isDomainIsolated

My particular upgrade report had a **Required** change whereby I had to add an *isDomainIsolated* property to my package-solution.json file.

![Upgrade Report advising to set isDomainIsolated](/img/spfx-update-18-isDomainIsolated.jpg)

This caused another build failure due to "not being supported" (sorry, I didn't get a screenshot of the error).

I resolved this by removing the property from the file.


#### tsconfig.json

##### es6-promise

Again, my upgrade report demanded that I install typings for es6-promise from npm as a Dev dependency. This yet again caused a complete failure of my build process, this time with convoluted error messages. I believe that this is to do with typings having already been included within a non-dev dependency (I may be wrong, as I say - it was convoluted). My fix for the issue was simply to not include it.

![Upgrade Report advising to install ES6 Typings](/img/spfx-update-18-es6-promise.jpg)


#### WebPart Property Pane

##### Import Property Pane components from a different location

Want to blow up your actual WebPart? Follow this suggestion (**just kidding - DON'T!**).

![Upgrade Report advising to import property pane components from a different library](/img/spfx-update-18-propert-pane.jpg)

I resolved this issue by not following the recommendation, and importing all of the components from **sp-webpart-base**; note: the commented out code is the suggested change - it does not work, don't do it.

~~~ts
import { BaseClientSideWebPart, IPropertyPaneConfiguration, PropertyPaneTextField, PropertyPaneToggle } from "@microsoft/sp-webpart-base";
// import { BaseClientSideWebPart } from "@microsoft/sp-webpart-base";
// import { IPropertyPaneConfiguration, PropertyPaneTextField, PropertyPaneToggle } from "@microsoft/sp-property-pane";
~~~


### Final clean up and build

I followed my normal clean up process when I do something as drastic as this.

- delete the node_modules folder
- delete the npm-shrinkwrap.json file

I then ran the following in order:

~~~powershell
npm install
npm install typescript tslint tsutils --save-dev # <- this was due to SPFx 1.8 still targeting TypeScript 2.x with gulp tools
npm shrinkwrap
gulp clean
gulp build
~~~

I was then left with a clean build sans errors - I was able to import any components I wanted from MS Office Fabric UI (which consequently is why I had to do the update from SPFx 1.4.1 in the first place).

Job done - hope this helps others.
