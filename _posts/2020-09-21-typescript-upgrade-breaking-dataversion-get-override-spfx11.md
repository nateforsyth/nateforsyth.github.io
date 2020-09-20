---
layout: post
title: Automatic TypeScript update breaks dataVersion getter in SPFx 1.11
date: 2020-09-21 +13:00
published: true
category: Development
tags: [sharepoint, spfx, react, typescript, bugs, features]
---


I noticed today that all of our SPFx solutions were throwing a compiler error within Visual Studio Code. Apparently the `dataVersion` getter included in the boilerplate of Yeoman scaffolded projects is suddenly incompatible.

Here's the code:

```ts

  protected get dataVersion(): Version {
    return Version.parse('1.0');
  }
  
```

Here's the error:

```
'dataVersion' is defined as a property in class 'BaseClientSideWebPart<[IMyWebPart]>', but is overridden here in 'MyWebPart' as an accessor.
```

![TypeScript update breaks SPFx dataVersion - error message](/img/TypeScript update breaks SPFx dataVersion 1.png)

I did a quick Google search, but didn't find anything specific to the Framework. Why would I expect to? These were (literally) working on Friday when I left.

That got me thinking... **TypeScript version**

Sure enough.

![TypeScript update breaks SPFx dataVersion - TypeScript version](/img/TypeScript update breaks SPFx dataVersion 2.png)

Thankfully this is very easy to resolve. But why did it happen? My Visual Studio Code instance updated when I started it this morning, and it appears to have installed the latest version of TypeScript along with it. We all know that this is *less than ideal* with SharePoint/SPFx development.

Let's get to fixing it.

Within Visual Studio Code; press [**F1**] -> type "TypeScript" -> select "TypeScript: Select TypeScript version..."

Now, select **"Use Workspace version x.y.zzzz"**

![TypeScript update breaks SPFx dataVersion - selecting the workspace version](/img/TypeScript update breaks SPFx dataVersion 3.png)

And now the compiler error has been resolved.

![TypeScript update breaks SPFx dataVersion - compiler error resolved](/img/TypeScript update breaks SPFx dataVersion 4.png)

That was a nice easy fix for something I didn't want to have to deal with on a Monday morning but *it is what it is*.

Hope that helps someone.

Until next time...
