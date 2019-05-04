---
layout: post
title: Building a shared Library - SPFx projects and Shared Libraries - May the 4th be with you ;)
date: 2019-05-04 +13:00
published: true
category: Dev
tags: [spfx, sharepoint, shared libraries, spfx extensions, spfx webparts, typescript, npm, azure devops, devops]
---

I cannot believe that it's been a month since I last posted about this topic. It's time to finish it off before I forget how I have gone about building, bundling and using this shared code project.

Here are links to all of the posts on this topic:
- [SPFx projects and Shared Libraries - numero uno](https://dreamsof.dev/2019-02-15-spfx-projects-and-shared-libraries-part-1/)
- [Building a shared Library - SPFx projects and Shared Libraries - part deux](https://dreamsof.dev/2019-02-21-building-shared-library-spfx-projects-and-shared-libraries-part-2/)
- [Building a shared Library - SPFx projects and Shared Libraries - iii](https://dreamsof.dev/2019-04-06-building-shared-library-spfx-projects-and-shared-libraries-part-3/)
- Building a shared Library - SPFx projects and Shared Libraries - May the 4th be with you ;)

As previously mentioned, our environment was set and I was to go about using some specific pieces of technology to achieve our goals.


## What this post will cover

- assumptions and notes before we start.
- building the project.
- bundling the project.
- using the project.


### Assumptions (re-iterated) and notes before we start

- You are using Azure DevOps for your source control.
- You are willing to use Azure DevOps as an npm Registry replacement.
- I have purposefully obfuscated company specific details in all screenshots.


### Building the project

First up, navigate to your Azure DevOps Pipeline Build interface for your specific project.

~~~text
https://dev.azure.com/[companyName]/[projectName]/_build
~~~

![Azure DevOps Pipeline Build interface](/img/devops1.png)

Next, click the **+ New** button and select **New build pipeline**.

Then select the following:
- Select a source = Azure Repos Git
- Team project = {projectName}
- Repository = {repositoryName}
- Default branck = {master} (recommended)

Note; you can select any source you want, just be aware that if you do, this post will differ from what you see.

![Azure DevOps Pipeline Build interface](/img/devops2.png)

Click **Continue**

On the Select a template page, select **Empty job**

It's now time to configure the Pipeline build job.

On the **Tasks tab**, enter a **name** for the build, and leave the **Agent pool** selection to default (*Hosted VS2017* in my example).

![Azure DevOps Pipeline Build interface](/img/devops3.png)

Next click into the **Triggers tab**

### Bundling the project




### Using the project


