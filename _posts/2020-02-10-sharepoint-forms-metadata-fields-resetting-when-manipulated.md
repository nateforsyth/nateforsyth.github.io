---
layout: post
title: SharePoint Forms Managed Metadata Fields Resetting when Manipulated
date: 2020-02-10 +13:00
published: true
category: Dev
tags: [microsoft, sharepoint, term store, managed metadata, bug, modern, metadata field]
---


It started two weeks ago. We were about to deploy a significant framework piece to a client environment that included, among many other components, an out of the box SharePoint List with custom Content Type. The Content Type includes multiple Managed Metadata Fields, all of which have default values selected at the Field level. Pretty basic stuff.

Except it wasn't as simple as expected was it. Upon conducting final testing, 2 days before go-live, we observed that pre-selected meta data values would disappear when you make an entry in any other field.


#### This issue is still current as of 10th February 2020


### Replicating the issue

This is very easy to replicate.

Create a new Term Set, something like this:

![A basic SharePoint Term Set](/img/SPO MMD default fields 20200203 a.png)

Then we will create a new Field upon a SharePoint Site with a name similar to the Term Set **[A]**, selecting the Term Set **[B]**, and finally selecting a Default Value on the Field **[C]**:

![A SharePoint Managed Metadata Field](/img/SPO MMD default fields 20200203 b.png)

Now we'll create a Content Type, adding the Managed Metadata Field we just created, something like this:

![A SharePoint Content Type with a Managed Metadata Field](/img/SPO MMD default fields 20200203 c.png)

Next up, we'll create a new List on the Site we're working on, enable List Content Types and add the Content Type we just created.

We're now ready to replicate the issue.

Return to the default List View, and create a new Item. Note that the default value selected at the Field level within the SharePoint Field is populated as expected.

![A SharePoint List Item with Managed Metadata Field default value](/img/SPO MMD default fields 20200203 d.png)

Now, let's type something into the Description field - all good so far. Now tab into the next Field - note how the default value that was there before has disappeared...

![A SharePoint List Item with Managed Metadata Field default value cleared](/img/SPO MMD default fields 20200203 e.png)

Not good!


### Microsoft, we have a problem

We were able to confirm this upon 2 client tenants, our own production tenant as well as our test tenant.

We raised support request **18532002** with Microsoft on the 2nd of February 2020, and they were able to replicate this on the 3rd advising that a recent update had likely caused the problem.

On the 7th of February Microsoft confirmed that this *appeared* to be a bug affecting every tenant globally, and that they'd made the Engineering Team aware of it, and that it would be scheduled for a fix sometime *in the coming months* (yeah, yeah... I know, nowhere near good enough).


### The work-around

It sure is a pain, but there is a simple work-around, and while less than ideal, it does return SharePoint List Item creation to near expected functionality.

Note: This will only work if you have required Fields that won't have values applied by default.

- Create a new List Item.
- Immediately click the **Save** button.
- Your required Fields will complain that you've not entered anything.
- Fill in the rest of the Item.
- Note that you no longer lose the default values.


### In closing

In the ideal world regression testing would be completed across the entire eco-system well before Microsoft pushes breaking changes, but that's not the way it's done. Sometimes it feels like we're the testers, even though we're paying for a production system. It is what it is.

Are you encountering this issue? If not, please try to replicate it.

Cheers!
