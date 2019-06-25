---
layout: post
title: SPFx React - improving console logging - revisited
date: 2019-06-25 +13:00
published: true
category: Dev
tags: [typescript, spfx, sharepoint framework, sharepoint, react, console logging]
---

In my [previous post](https://dreamsof.dev/2019-06-10-improving-console-logging-spfx/) we discussed how we could go about styling browser dev tool console logging to assist with initial triage of issues with our SPFx Extensions.

I was again working on the project today that spawned that post, and didn't like the way I'd handled the conditional nature of the styled console output.


### So, what are we going to talk about today?

[The approach I illustrated](https://dreamsof.dev/2019-06-10-improving-console-logging-spfx/) is fine for a Web Part project because we can easily tie it to the property pane, but I've realised that it isn't very dev-friendly from the context of simplifying the triage of an Extension problem.

Why's that you ask?
- The methodology I've used relies upon being able to update the manifest of the Extension so that we can see debug output.
- This doesn't bode well for quick triage due to having to load up the project and `gulp serve`, while using an updated manifest in the query string.
- This is also not useful if you needed to ask the client to get you some quick triage information if they're having issues.


### Not good enough then... what are we going to do about it?

The idea here is that we will use the presence of a token within `document.location.href` in conjunction with support for the presence of a boolean within the Extension properties. That combination will flag whether we're tracing or not, and handle the console styling as we were before.

Below is the [ImprovedConsoleLoggingApplicationCustomizer class from the previous post](https://dreamsof.dev/2019-06-10-improving-console-logging-spfx/), as well as our minor changes. You'll see by the end that this really improves that all important initial triage process.

~~~ts
// ImprovedConsoleLoggingApplicationCustomizer.ts

export default class ImprovedConsoleLoggingApplicationCustomizer
  extends BaseApplicationCustomizer<IImprovedConsoleLoggingApplicationCustomizerProperties> {

  private doTrace: boolean = this.properties.doTrace || (document.location.href.indexOf(`?traceImprovedConsoleLoggingApplicationCustomizer`) > -1 || document.location.href.indexOf(`&traceImprovedConsoleLoggingApplicationCustomizer`) > -1); // NEW

  @override
  public onInit(): Promise<void> {
    Log.info(LOG_SOURCE, `Initialized ${strings.Title}`);

    const loggingDelay: number = 1500; // milliseconds

    const simulatedLongRunningLogging: string = `\toutputDelayedLogging invoked after ${loggingDelay} ms\r\n\tdebug X: longRunningResult1\r\n\tdebug Y: longRunningResult2\r\n\tdebugZ: longRunningResult3`;

    const debugColourOvr: string = `orange`;
    const debugFontWeightOvr: string = `800`;

    if (this.doTrace) { // UPDATED was this.properties.doTrace
      console.log(`%cLoading extension in trace mode\r\n\tAbout to invoke outputDelayedLogging`, `color: ${debugColourOvr};`);

      this.outputDelayedLogging(simulatedLongRunningLogging, loggingDelay, debugFontWeightOvr);
    } else {
      console.log(`Loading extension normally...`);
    }

    return Promise.resolve();
  }

  private outputDelayedLogging(message, ms: number, fontWeightOvr: string = ""): void {
    setTimeout(() => {
      console.log(`%c${message}`, `color: orange;${fontWeightOvr !== "" ? `font-weight: ${fontWeightOvr}` : ""}`);
    }, ms);
  }
}
~~~

Now all we need to do to trace an Extension on a client environment is add either `?traceImprovedConsoleLoggingApplicationCustomizer` or `&traceImprovedConsoleLoggingApplicationCustomizer` to the end of the URL in the address bar. At that point the console logging will look as it did before.

This means that we can ask a customer to do that for us and simply send us an extract or screenshot of the logging.

Here's what it looks like on one of our live test tenants.

![Styling Console Logging extension - styled logging simplified](/img/StylingConsoleLogging10.png)

Clean. Efficient. Extensible... just the way I like it!

Until next time. Let me know your thoughts.
