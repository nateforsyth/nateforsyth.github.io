---
layout: post
title: Learning Blazor - numero uno
date: 2019-05-02 +13:00
published: true
category: Dev
tags: [C#, blazor, failure, never stop learning, visual studio 2019, .net core 3.0, better late than never]
---

How many times have you installed the [Visual Studio 2019 preview](https://visualstudio.microsoft.com/vs/preview/) this week?

I'm sure you can tell that that's a loaded question, we'll revisit it again at the end.

I was initially following the VS2019 guide to [getting started with Blazor and .NET Core 3.0](https://docs.microsoft.com/en-us/aspnet/core/blazor/get-started?view=aspnetcore-3.0&tabs=visual-studio). Everything was going swimmingly.


### First install (and failed attempt)

I followed the guide to a tee, installing everything in order, and creating my first Blazor client-side app.

I then hit the good ol' *F5* button... and everything worked as expected. I looked at the IDE and saw the red squiggly indicating something was wrong.

> The type or namespace name 'HttpClient' could not be found (are you missing a using directive or an assembly reference?)

But it was running... wtf?

I then thought meh, must be a bug, I'll continue, afterall - everything was running right. Right?

I added some classes, connected to my [Deployd](https://deployd.com/) instance, restarted the app and... screeching halt - 200+ errors regarding missing using/assembly references in the scaffolded components.

Time for some Googling. Initial findings leaned towards, for some reason, Blazor not liking certain naming conventions for projects (I cannot find the Stack Exchange link that I found stating that - it's irrelevant now).

Deleted the project, recreated from scratch with different naming convention - won't build - same errors. Double wtf.

Deleted the project, tried to create a .NET Core 3.0 console app w/ `Console.WriteLine("wtf is going on???")` - similar errors. wtf<sup>indeed</sup>

I uninstalled Visual Studio 2019 preview.


### Second install (failure #2)

Something had obviously gone awry, I'd assumed only with Visual Studio, so I set about reinstalling it. I had to install the [Blazor Extension](https://go.microsoft.com/fwlink/?linkid=870389) again as expected. I then set the **Use previews of the .NET Core SDK** option.

I created another Blazor client-side extension. It built as I expected, so I hit F5...

~~~text
The type or namespace name '{elided}' could not be found (are you missing a using directive or an assembly reference?)
The type or namespace name '{elided}' could not be found (are you missing a using directive or an assembly reference?)
The type or namespace name '{elided}' could not be found (are you missing a using directive or an assembly reference?)
etc etc etc
~~~

I updated my VS2019 installation, tried again - same problem.

At this point I'm pulling my hair out, I'm scaffolding something beyond simple and yet things are falling apart.

I removed the extension, and uninstalled Visual Studio again.

I then removed the .NET Core 3.0 Preview SDK.


### Third install

By now I was sick of it, but I wanted to give it one more go - this time in what I deem to be a more logical order.

I did the following:
- (again) downloaded and installed the latest [Visual Studio 2019 preview](https://visualstudio.microsoft.com/vs/preview/) with the ASP.NET and web development workload.
  - I then ran Visual Studio and ensured that I could create and run a generic console app - worked.
- installed the [SDK 3.0.100-preview4-011223](https://dotnet.microsoft.com/download/thank-you/dotnet-sdk-3.0.100-preview4-windows-x64-installer) using the 64bit installer.
  - I then set the **Use previews of the .NET Core SDK** option.
  - I then ran Visual Studio again, created another console app, this time using .NET Core 3.0 - success.
  - I then updated Visual Studio, running the previous console app again - success<sup>2</sup>
- installed the [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the market place.
  - I ran Visual Studio, scaffolded a new Blazor client side app, build, F5... all worked.
  - I then added a basic POCO and updated the code a little, clean, build, F5... working again.
- I then set about adding connectivity to my Deployd end-points, restart... huzzah!

**GREAT SUCCESS!**

You'll note that the preceding order of operations is only slightly different to what was written within the guide, and although I don't necessarily believe it'll solve your problems, installing everything in that logical order has worked for me.

I've been able to return to the project the last two nights and work on it without encountering any further issues - [ymmv](https://dictionary.cambridge.org/dictionary/english/ymmv) of course.


### So what happened here?

Click-bait-esque first paragraph aside, I've installed VS2019 3 times this week. Blazor doesn't feel overly stable when **things go wrong**<sup>TM</sup> - but what actually went wrong? I have no damned idea if I'm being completely honest. I'm just happy that I was able to resolve the issues by following some good old take a step and test it before the next logic.

I should do that more often ;)


### Some resources

Here are some of the links that helped me to kinda but not really narrow down my problem.

These are the most similar issues to what I was encountering:
- [[Blazor] Template is not working in both Visual Studio 2019 preview 3.0 and VS Code](https://github.com/aspnet/AspNetCore/issues/7952)
- [blazor not start with vscode](https://github.com/Microsoft/vscode/issues/72701)

And sort of:
- [The default Blazor template solution doesn't run](https://github.com/aspnet/AspNetCore/issues/8074)
