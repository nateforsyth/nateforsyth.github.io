---
layout: post
title: SharePoint Page columns in sections are no longer returned in order
date: 2022-10-26 +13:00
published: true
category: C#
tags: [csom, sharepoint, reflection, regression, site page copying, cross tenant]
---

In the not-so-distant past, we used to be able to retrieve SharePoint Page content Columns (inside Sections) and could count on them being in order. This appears to no longer be the case, necessitating a rework of understood functionality and some reflection (pun intended).


## The problem

This is important because I need to make exact copies of Site Page items across SharePoint tenancies, but I **cannot** ever copy meta data between them. This means that code is used to make an exact **clean** replication of the page content. This removed the ability to simply copy pages using ootb functionality.

I don't remember if an individual [CanvasColumn](https://learn.microsoft.com/en-us/dotnet/api/officedevpnp.core.pages.canvascolumn?view=sharepoint-pnpcoreol-3.2.1810) within a [CanvasSection](https://learn.microsoft.com/en-us/dotnet/api/officedevpnp.core.pages.canvassection?view=sharepoint-pnpcoreol-3.2.1810) was simply ordered, or if they actually had an Order property.

I'd simply iterate over each CanvasColumn in the CanvasSection and copy properties and content to newly provisioned sites, often cross tenant.

These are now being retrieved out of order, and are confirmed to not have an Order property.

![CanvasColumn properties no Order](/img/CanvasColumn_properties.png)

However, during debugging, I observed that there is a Non-Public member for an Order property, respecting positioning within the SharePoint Page Section.

![CanvasColumn non-public properties has Order](/img/CanvasColumn_non-public-properties.png)

We can use this, let's get into it.


## The solution

Whatever was done needed to be reusable, therefore I was going to create a utility method.


### A utility method

The only way I'm aware of to do this is using Reflection, some googling confirmed enough of that for me to commit to the solution. First up is a method to be able to retrieve a value from a Non-Public member on any object instance.

~~~cs
/// <summary>
/// Retrieve a Non-Public member value from a defined property title included within the provided object
/// </summary>
/// <param name="obj">Object to retrieve a value from</param>
/// <param name="propTitle">Title of the Non-Public member</param>
/// <returns>Value of the defined property, of Type object</returns>
public static object GetNonPublicPropertyValue(object obj, string propTitle)
{
	PropertyInfo reflectedProp = element.GetType().GetProperties(BindingFlags.NonPublic | BindingFlags.Instance).Single(info => string.Equals(info.Name, propTitle));
	object reflectedVal = reflectedProp.GetValue(element, null);

	return reflectedVal;
}
~~~

The retrieved value is always an object, it's up to the consuming code to cast this as an expected value for further processing.

This code can be reused for many use-cases - I think it'll be an auto-include in my projects from now on.


### Now we use it

Now, for this example, I have already iterated into a collection of CanvasSection objects.

Remember how Microsoft is no longer sorting CanvasColumn objects logically in back-end code? My use case necessitated that my results be sorted, so I've implemented a List.Sort using a Comparer<CanvasColumn>.

~~~cs
// previous elided - with a Page CanvasSection object selected (oldSection), retrieve all CanvasColumn objects
List<CanvasColumn> newSectionColumns = oldSection.Columns;

// sort the List of columns using a Comparer which in turn uses the new GetNonPublicPropertyValue method above, validating sort order in a verbose if statement
newSectionColumns.Sort(
	Comparer<CanvasColumn>.Create((a, b) =>
	{
		int.TryParse(Utils.GetNonPublicPropertyValue(a, "Order").ToString(), out int aOrder);
		int.TryParse(Utils.GetNonPublicPropertyValue(b, "Order").ToString(), out int bOrder);

		if (aOrder > bOrder)
		{
			return 1;
		}
			else if (aOrder < bOrder)
		{
			return -1;
		}
		else
		{
			return 0;
		}
	}));

foreach (CanvasColumn column in newSectionColumns) { // elided, do something with every column }
~~~

I now have my CanvasColumn objects sorted based upon the templated Site Page I am copying and can recreate all Columns in order, ready to recreate all Web Parts to replicate the content cross-tenant - we'll leave that for another post.

Hope this helps someone.
