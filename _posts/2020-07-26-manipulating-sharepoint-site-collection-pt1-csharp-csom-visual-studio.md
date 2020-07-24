---
layout: post
title: Primer - Manipulate SharePoint Site Collections part deux, C# and CSOM
date: 2020-07-26 +13:00
published: true
category: Development
tags: [sharepoint, visual studio, C#, csom, primer, entry level]
---

My [previous post](https://dreamsof.dev/2020-07-25-connect-to-sharepoint-site-collection/) detailed how to use PowerShell to connect to your SharePoint tenant using SPO and PnP PowerShell and manipulate it with CSOM. In this post we will do something very similar using C# and CSOM from within a Console App using the .NET Framework.


### Assumptions and Notes

I make some assumptions about your skill level for this blog post. I'll provide links to further reading, Google will help to fill in gaps.

- You are [comfortable with C#](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/intro-to-csharp/).
- You understand the fundamentals revolving around [programmatically working with Sharepoint Site Collections](https://dreamsof.dev/2020-07-25-connect-to-sharepoint-site-collection/).

Additionally:

- The code examples included here are to highlight specific functionality, and in no way imply use of best practices.
- Do not use this code as is in a production environment (I do not obfuscate credentials for these simple examples).
- Refer to my [Web App modernisation using ASP.NET Web API](https://dreamsof.dev/2020-07-22-modernising-legacy-app-aspnet48-jwt-sql-efdatafirst-react-1/) series of posts if you'd like better examples of how to handle sensitive information within .NET Applications.
- I am using very specific versions of the dependencies in this example due to consistent results. You're welcome to use updated versions, but I assume you're using these; you may need to refactor my examples slightly.


### Scaffold and build out a basic .NET Framework Console App

Scaffold a new .NET 4.8 Framework **Console Application** project within Visual Studio.

Install the following dependencies using the package manager console:

- Microsoft.SharePointOnline.CSOM.16.1.8316.1200
- SharePointPnP.IdentityModel.Extensions.1.2.3
- SharePointPnPCoreOnline.3.4.1812.1

Add a new class to your project called **SharePointAccessor**, add using statements as required:

```cs
public class SharePointAccessor
{
    /// <summary>
    /// Test SPO Site Connectivity
    /// </summary>
    /// <param name="userPrincipalName">Email</param>
    /// <param name="password"></param>
    /// <param name="siteUrl"></param>
    /// <param name="tenantUsesMFA">If true, passed credentials are ignored and you'll be prompted to use the web interface to log in</param>
    /// <returns></returns>
    public static bool TestSPOSiteConnectivity(string userPrincipalName, SecureString password, string siteUrl, bool tenantUsesMFA = false)
    {
        var connected = false;

        try
        {
            var authManager = new OfficeDevPnP.Core.AuthenticationManager();

            ClientContext clientContext = null;

            if (tenantUsesMFA)
            {
                clientContext = authManager.GetWebLoginClientContext(siteUrl);
            }
            else
            {
                clientContext = new ClientContext(siteUrl);
            }

            if (clientContext != null)
            {
                using (clientContext)
                {
                    clientContext.Credentials = new SharePointOnlineCredentials(userPrincipalName, password);
                    clientContext.RequestTimeout = Timeout.Infinite;

                    var contextWeb = clientContext.Web;
                    clientContext.Load(contextWeb, cw => cw.Title);

                    var siteTitle = "";

                    try
                    {
                        siteTitle = contextWeb.Title; // this will throw an exception because we've not yet executed the retrieval of additional details on this site
                    }
                    catch (Exception intentionalException)
                    {
                        Console.WriteLine($"\r\n\tTestSPOSiteConnectivity => attempting to retrieve site Title (THIS WILL INTENTIONALLY FAIL BECAUSE QUERY NOT YET EXECUTED)\r\n\t\t{intentionalException.Message}");
                    }

                    // this is the magic, much the same as PowerShell, execute the actual query
                    clientContext.ExecuteQueryRetry();

                    siteTitle = contextWeb.Title; // this will now be populated as expected

                    Console.WriteLine($"\r\n\tTestSPOSiteConnectivity => site Title retrieved: {contextWeb.Title}");

                    connected = true;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception encountered; {ex.InnerException}");
        }

        return connected;
    }
}
```

Now update the **Main** method within the **Program** class as follows:

```cs
public class Program
{
    static string userName = "user@company.com"; // this should be obfuscated
    static string password = "password"; // this should be obfuscated
    static SecureString securePass = new NetworkCredential("", password).SecurePassword;
    static string siteUrl = "https://[tenantName].sharepoint.com/sites/[siteName]";

    public static void Main(string[] args)
    {
        Console.WriteLine($"Attempting to connect to siteUrl: {siteUrl}");

        // This invocation may fail due to invalid credentials if your tenant has MFA enabled
        var tenantUsesMFA = false; // switch this if using MFA
        var connectedToSpoSite = SharePointAccessor.TestSPOSiteConnectivity(userName, securePass, siteUrl, tenantUsesMFA);

        Console.WriteLine($"\r\nConnected to Site? {connectedToSpoSite}");

        Console.Read();
    }
}
```


### Run the console application

Alrighty, you've now built out a very simple Site Connectivity testing app, let's run it - press **F5**.

You will receive an intentional failure followed by a success. This is to highlight the where the query you build with CSOM has to be executed before the properties are available for further manipulation. This highlights the similarity between this example and the [PowerShell example shown previously](https://dreamsof.dev/2020-07-25-connect-to-sharepoint-site-collection/).

```cs
/*
    Expected results:
    
Attempting to connect to siteUrl: https://brighterdays.sharepoint.com/sites/OrgDemo

        TestSPOSiteConnectivity => attempting to retrieve site Title (THIS WILL INTENTIONALLY FAIL BECAUSE QUERY NOT YET EXECUTED)
                The property or field 'Title' has not been initialized. It has not been requested or the request has not been executed. It may need to be explicitly requested.

        TestSPOSiteConnectivity => site Title retrieved: The Hub

Connected to Site? True
 */
```

We've now connected directly to our site and decorated our returned site instance with some additional information that isn't available without specifically doing so.


### What else can we do?

That's a loaded question. There's a huge amount that can be done once you're using CSOM with C# directly connected to a SPO Site Collection. For now, let's build the same functionality we did within PowerShell last time; retrieval of Content Types on the Shared Documents Library.

Return to your Console Application > **SharePointAccessor** class.

Add a new method called ****.

```cs
/// <summary>
/// Retrieve Content Types from a SPO List or Library
/// </summary>
/// <param name="userPrincipalName"></param>
/// <param name="password"></param>
/// <param name="siteUrl"></param>
/// <param name="listOrLibraryName"></param>
/// <param name="tenantUsesMFA"></param>
/// <returns></returns>
public static IList<string> RetrieveListContentTypesFromSpoSite(string userPrincipalName, SecureString password, string siteUrl, string listOrLibraryName, bool tenantUsesMFA = false)
{
    var ctList = new List<string>();

    try
    {
        var authManager = new OfficeDevPnP.Core.AuthenticationManager();

        ClientContext clientContext = null;

        if (tenantUsesMFA)
        {
            clientContext = authManager.GetWebLoginClientContext(siteUrl);
        }
        else
        {
            clientContext = new ClientContext(siteUrl);
        }

        if (clientContext != null)
        {
            using (clientContext)
            {
                clientContext.Credentials = new SharePointOnlineCredentials(userPrincipalName, password);
                clientContext.RequestTimeout = Timeout.Infinite;

                var contextLibrary = clientContext.Web.Lists.GetByTitle(listOrLibraryName);

                clientContext.Load(contextLibrary, cl => cl.ContentTypes); // retrieve the content types on this list/library
                clientContext.ExecuteQueryRetry(); // execute the query

                foreach (var ct in contextLibrary.ContentTypes) // append to the data structure
                {
                    ctList.Add($"\t\t> {ct.Name}|{ct.Id}");
                }

                Console.WriteLine($"\r\n\tRetrieveListContentTypesFromSpoSite => {ctList.Count} Content Types retrieved\r\n");
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine($"Exception encountered; {ex.Message}");
    }

    return ctList;
}
```

Then add the following to your **Main** method and run it with **F5**:

```cs
var targetLibrary = "Documents";
foreach (var ct in SharePointAccessor.RetrieveListContentTypesFromSpoSite(userName, securePass, siteUrl, targetLibrary))
{
    Console.WriteLine($"{ct}");
}
```

```cs
/*
    Expected results:
    
    // previous elided for brevity

    RetrieveListContentTypesFromSpoSite => 2 Content Types retrieved

            > Generic Document|[guid]
            > Folder|[guid]
 */

```

This is where this whole rigmarole has gotten a whole lot more useful. As per the previous PowerShell example, we have retrieved a list of Content Types used within our Shared Documents Library. As before you can take this further by slowly building up the objects you’re working with by retrieving all Fields on each Content Type, or whatever else you need to do.

Job's a goodun!
