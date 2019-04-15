---
layout: post
title: From Blackjack Dealer to SharePoint Developer
date: 2019-02-14 +13:00
published: true
image: /img/blackjack.jpg
category: Career
tags: [mystory, earlydays, storytime, careerdevelopment]
---


I'm often asked how I got into this game. There's a lot to this story as the title of this post would suggest, and it begins almost 2 decades ago way back in 2000 when I tried my hand at going to university to study Computer Science. I don't think my brain was ready, was probably too stupid. It didn't gel. I dropped out.

I tried again in early 2003, and even though I made it through the first semester, I unfortunately had to leave soon after due to financial pressures at the time.

Later in 2003 I began working at the Christchurch Casino as a Blackjack Dealer. I needed money.

I expressed my interest in the Surveillance side of the operation to the Security Manager at my induction. A few weeks later he approached me on the floor (I thought I'd done something wrong). Taking me aside he asked whether I was sincere in my interest in joining his Surveillance Department - I jumped at the opportunity.

Fast forward a year or so, I was entrenched in my role and was loving it. I worked closely with DIA Inspectors and continually displayed excessive levels of pedantic attention to detail - a trait that was well appreciated - and a trait that serves me well now, I digress.

Each month during a day-shift excess staff were rostered on for training activities. Sometimes this would revolve around learning to count cards, other times it'd be learning to cheat at Roulette, other times it'd be purely theoretical.


### A spark was lit

One training afternoon we were given a very simple brief; **Find out whether a group of players could theoretically make a profit from chasing our gaming machine jackpots**.

The brief was well on topic - we'd been inundated for weeks by syndicated play groups from overseas hammering our jackpots. The Gaming department wanted to know whether there was a risk.

I started by choosing a bank of machines, and exporting the register data, which was significant, and I needed a way to manipulate it easily. Batter up Excel!

After poring over the documentation I managed to cobble together some initial findings, including the possibility where a group of players playing maximum credits and maximum lines at a certain point could theoretically guarantee a profit. A break-even point if you will.

I handed over my work, naturally passing the training exercise with flying colours due to how thorough I had been. What I didn't expect was for my work to illicit a bigger question.


#### Do your findings highlight a risk to the business?

I had a meeting with Management and was given a timeboxed task of further analysis on other banks of machines to find out whether there was any kind of risk.

I had thought the volume of data from a single bank of machines during a single jackpot chase was large, what I found when I opened the flood-gates was cumbersome.

I already had a solid understanding of Excel formulas and was pretty efficient at validating data using the tools available. Yes I had a little experience with Java, but there was no way they'd allow me to install an IDE on their systems. I needed a better solution, and that was inevitably going to have to be something they already had on their systems; cue up [Visual Basic for Applications (VBA)](https://docs.microsoft.com/en-us/office/vba/library-reference/concepts/getting-started-with-vba-in-office).

I again poured over the documentation and built a model whereby any bank of machine data could be imported and analysed. The results were surprising.


#### A risk was highlighted

I cannot go into details, but there were a few of the jackpots that could mathematically guarantee a significant profit through a group of syndicated players working together between a surprisingly wide break-even window.

These findings triggered a not-insignificant period of focused monitoring and reporting based upon theoretical scenarios, and watching of actual jackpot chases. We'd plug in the actual chase numbers after the fact, and surprisingly the model held up.

DIA was notified of the findings as were the heads of gaming department.

From a business perspective, the people chasing the jackpots would statistically lose all of the money they won and then some on-site. For that reason a monitoring process was put in place to curb the activity when it got out of hand, as opposed to putting the hammer down on the activity.


#### Fanning the flames

I didn't know it then, but the work I'd done had given us the opportunity to retrospectively detect where jackpot chases had occurred. We were able to go back and watch historical chases and detect Syndicated Play. To be clear; Syndicated Play is "against the rules" and people will be trespassed as it's deemed cheating.

One of the criteria we'd set in the model was to monitor how many notes had been fed into any machine stacker in any time period, and where there was a very low level of turn-over at the same time, we'd receive an alert. This was intended to be a near real-time notification that a jackpot chase might be about to occur as that was a typical machine hoarding and stalling tactic of the syndicates.

There was another interesting side effect of this process.

During the handover to a day-shift I was about to start, a fellow Operator told me that she'd received a notification that a chase might be about to happen the night before. She brought up a camera and saw that a customer was sitting at a machine, feeding in huge wads of $20 notes, but there had been no play.

She didn't know what she had been seeing, however the Shift Manager recognised the guy as a known drug dealer in the city. We monitored him for the rest of his time on-site and reported our findings to the Inspectorate.

Sans more detail, suffice to say we'd been able to detect money laundering, and the guy was arrested as such within a month.

There are other aspects of syndicated casino groups that these new processes allowed us to track and monitor more effectively; there's a  social impact whereby they would coerce young students into effectively working well below the minimum wage, oftentimes merely for packates of cigarettes or the like. These activities are delicate by nature and often heavily tied into foreign cultures, superstitions and fears. We were able to highlight these aspects to DIA whom were able to raise charges against certain members with proof to back it up.

This was all achieved with Excel and VBA - flames were indeed fanned.


#### A period of not a hell of a lot

I left the Casino after almost 5 years to go to Wellington, where I got a job with a company that was writing software for Ford and Mazda dealerships around the country. I was a Project Manager on the deployment and roll-out of a new module of their product. I used my Excel skills to keep them fresh, but nowhere near the level of complexity I had been enjoying previously. My skills didn't atrophy, but I did get bored... really bored.

I returned to Christchurch in early 2010 and began working at the [Kathmandu](https://www.kathmandu.co.nz/) head office doing, you guessed it - data analysis. The processes within that business were archaic at best - manual data entry of their entire product catalogue as well as humongous numbers of manually entered sales lists, that also had to be entered into multiple systems including POS.


#### The embers were still glowing

I was back in my element. I dredged up my old Excel and VBA skills and set about automating as much of my job as I could. And automate I did.

Reporting, sales lists, product imports/exports, CSV file prep for integrations into other systems, Purchase Orders...

I made myself indispensible.

And then [the earthquakes hit](https://my.christchurchcitylibraries.com/christchurch-and-canterbury-earthquakes/).

I persisted for around 6 months, but family had been hurt in the second large quake. I was forced to abandon my city, and again found myself in Wellington.

I began work at Transpower in Wellington in late 2011 as a Data Analyst on the Half Hour team. The mandate was to basically aggregate and validate all commercial half hour energy volume data.

Each month the Data Analyst responsible for submission of every energy retailers numbers must have them all submitted to the Reconciliation Manager by 4pm on business day 13. This often means that there is horrendous amounts of overtime a few days prior as retailers would provide their data exceptionally late. I was run down within 2 months, and near over it.

This was a well paid job. And it was again completely manual. And I was bored. I couldn't have any of that. I had an Ace up my sleeve.

I waited until I understood my role and what was required before I broached the subject of automation. I was good at my job, but frustrated and articulated that I'd likely leave if I didn't have a chance to make the process better. After a quick mock-up and presentation I was given the green light to again use Excel and VBA to automate as much as possible.


#### Stoking the fire

Things were going swimmingly, and I was on top of my game. My tools had been adopted by the company and my role managed an financial year of zero overtime and zero errors for my function (errors have a significant financial impact on energy retailers). I was relatively stress free and the company was happy.

I had time on my hands so the company got me involved in User Acceptance Testing of a new product that a local dev shop had built for us using Ruby. I thoroughly enjoyed myself and got deeper and more involved in the project (not the source code mind you). I must have impressed the owner of the company because he offered me a job as a developer - I laughed it off as a joke, I hadn't been developing nor had I even seen their source code.

What that conversation did do though was really get me thinking about my career trajectory. At the time I was near a plateau in Data Analysis - yes the money was good, but it wasn't going to get much better any time soon - and I was still pretty bored.


#### A raging inferno

Some personal issues cropped up, and I wanted to be back home. I'd always felt that I'd abandoned Christchurch, and people needed me. I also wanted a change.

I returned to Kathmandu in Christchurch in early 2014 on a 3 month contract, and immediately began to learn Java in my spare time.

Shock horror - it was actually sticking this time.

I lasted the 3 months and was offered a renewal, which I negotiated to be part time. It was time to go back to school.

I started at a local private training institution in June 2014 working towards a National Diploma in Advanced Software Development Level 6. The bug had bitten, and there was no putting out this fire.

You can [read a little more about that time of my life here](https://dreamsof.dev/aboutme/).

Many projects, much learning and tens of months later I graduated with my diploma in late 2016. A huge achievement many years in in the making for someone who had no other tertiary qualifications.

As mentioned within my [about page](https://dreamsof.dev/aboutme/), I got myself involved with a farming project that was doomed to not succeed as a business. What it did give me was a huge amount of experience, albeit rough.


#### Couldn't put the fire out, detours inevitable

Fast forward to early 2018 and I was in desperate need of money. I had to look for a job. One simple listing stood out, and another long story short I was hired as a .NET Developer by a small digital agency in Christchurch.

I had [skills in .NET](https://dreamsof.dev/resume) and had built something I was pretty proud of. It wasn't perfect, it wasn't enterprise grade. But what it was was exrensible, secure, and working perfectly.

Sooo, I was a .NET Developer. What was my first project? You waht now? Sharepoint?

My first project was a trial by fire custom Classic SharePoint (Office 365) deployment for Trojan Holdings Ltd based in Queenstown, including custom layouts and WebParts. I'd never seen SharePoint before. Better roll my sleeves up then huh?

I had 6 weeks to sufficiently learn SharePoint Administration to provision one Site and 9 sub-sites for the client's companies/divisions. I had to build 4x WebParts (News Carousel, News Archive, Wayfinding, Introducing) and style the sites ready for the client to begin their migration.

I nailed the deadline with 2 days to spare. It was stressful, and it was difficult, but I persisted and I got it done.

I learned a huge amount.


#### The end of the story

Not likely.

I am now 13 months into the same role and am neck deep in the development of a productised [SharePoint Accelerator](https://solarworkplace.co.nz/) including near stock look and feel custom WebParts and Extensions using the SharePoint Framework (SPFx), Information Architecture, SharePoint Administration and Configuration, and automated tenant provisioning including IA.

I now work directly with half a dozen large NZ companies delivering near stock Intranet experiences with just enough customisation to make everything better (imho).

To say that getting into SharePoint was unexpected is an understatement, and I'd be lying if I said I didn't still pine for .NET, but the challenge is real, as are the transferrable skills (specifically front end web-dev with React).

I can confidently say that I am now a SharePoint Developer first and foremost.

Thanks for reading :)
