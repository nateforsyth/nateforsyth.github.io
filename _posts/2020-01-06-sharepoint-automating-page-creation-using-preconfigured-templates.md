---
layout: post
title: Creating many Pages using pre-configured Page Templates within SharePoint
date: 2020-01-06 +13:00
published: true
category: Development
tags: [sharepoint, PowerShell, automation]
---

First day back at work after a relaxing xmas break. I hope you've all had a good break yourselves. I want to touch on how to best utilise [Custom Page Templates in Modern SharePoint](https://support.office.com/en-us/article/page-templates-in-sharepoint-online-faa92408-0c84-4e3d-8460-3c28065e7873), and how to use a CSV file to automate the provisioning of many such pages within a Modern Site Collection.


### Preparation

First up, ensure that you have a site collection for testing, and that it includes 1 or more templates that you want to use to create your pages. If you haven't already done so, [follow this guide to create your templates](https://support.office.com/en-us/article/page-templates-in-sharepoint-online-faa92408-0c84-4e3d-8460-3c28065e7873).

Next, prepare a CSV file with the following columns at a minimum, here's some test data as well:

|PageName|PageTitle|PageCommentsEnabled|PagePublish|PageCustomTemplate|
|---|---|---|---|---|
|TestPageOne|Test Page One|FALSE|TRUE|Templates/TemplateOne.aspx|
|TestPageTwo|Test Page Two|TRUE|TRUE|Templates/TemplateOne.aspx|
|TestPageThree|Test Page Three|TRUE|FALSE|Templates/TemplateTwo.aspx|

Using this data, I will create two pages using my pre-configured TemplateOne.aspx template (one without Comments, the other with, both published) and another unpublished page with Comments enabled using the TemplateTwo.aspx template.


### Creating the Pages

Now, we are going to run through the steps required to create multiple pages by importing a CSV file utilising Page Templates, and configuring the Pages per our requirements.


#### Connect to the Site using PnP PowerShell

Using [PnP PowerShell](https://docs.microsoft.com/en-us/powershell/module/sharepoint-pnp/add-pnpalert?view=sharepoint-ps), connect to your test site collection.

```powershell
$siteUrl = "https://[tenantName].sharepoint.com/sites/[siteName]";
Connect-PnPOnline -Url $siteUrl; # you'll be prompted for your credentials

# test that you are connected
$ctx = Get-PnPContext;
$ctx; # this will display the context information of the connected site
```

Here are the results.

```text
RetryCount                   : 10
Delay                        : 1000
PropertyBag                  : {}
Web                          : Microsoft.SharePoint.Client.Web
Site                         : Microsoft.SharePoint.Client.Site
RequestResources             : Microsoft.SharePoint.Client.RequestResources
FormDigestHandlingEnabled    : True
ServerVersion                : [elided]
Url                          : https://[tenameName].sharepoint.com/sites/[siteName]
ApplicationName              : SharePoint PnP PowerShell Library
ClientTag                    : PnPCore:1906:
DisableReturnValueCache      : True
ValidateOnClient             : True
AuthenticationMode           : Default
FormsAuthenticationLoginInfo : 
Credentials                  : Microsoft.SharePoint.Client.SharePointOnlineCredentials
WebRequestExecutorFactory    : Microsoft.SharePoint.Client.DefaultWebRequestExecutorFactory
PendingRequest               : Microsoft.SharePoint.Client.ClientRequest
HasPendingRequest            : True
Tag                          : 
RequestTimeout               : 1800000
StaticObjects                : {[Microsoft$SharePoint$SPContext$Current, Microsoft.SharePoint.Client.RequestContext]}
ServerSchemaVersion          : 15.0.0.0
ServerLibraryVersion         : 16.0.19520.12063
RequestSchemaVersion         : 15.0.0.0
TraceCorrelationId           : [elided]
```


#### Import your CSV file.

Righty, so we're now connected to our site, let's get to importing the CSV file.

```powershell
$csvFilePath = "[pathToYourCsvFile]";
$csvFileDelimiter = ","; # comma delimited

$csvFileDataSet = Import-Csv $csvFilePath -Delimiter $csvFileDelimiter;

# iterate over each row of the CSV file
For-Each ($row in $csvFileDataSet) {
  try {
    $pageFileName = ("{0}.aspx" -f $row.PageName);
    
    $pageTemplate = Get-PnPClientSidePage -Identity $row.PageCustomTemplate;
    $pageTemplate.Save($pageFileName);
    
    $commandToInvoke = ("Set-PnPClientSidePage -Identity {0} -Title {1}" -f $row.PageName, $row.PageTitle);
    
    if($row.PageCommentsEnabled){
      $commandToInvoke = ("{0} -CommentsEnabled" -f $commandToInvoke);
    } else {
      $commandToInvoke = ("{0} -CommentsEnabled:$false" -f $commandToInvoke);
    }
    
    if($row.PagePublish){
      $commandToInvoke = ("{0} -Publish" -f $commandToInvoke);
    } else {
      $commandToInvoke = ("{0} -Publish:$false" -f $commandToInvoke);
    }
    
    Invoke-Expression -Command $commandToInvoke;
  } catch {
    Write-Host ("Error encountered while adding Page: {0} to Site: {1} using Template {2}" -f $row.PageName, $siteUrl, $row.PageCustomTemplate) -ForegroundColor Red;
  }  
}
```

Run the above script.


#### The end result

All going to plan, we have 3x new Pages within the test Site Collection that have been created based upon the Page Templates defined within the CSV file that was imported.

Job's a goodun.

Happy new year!
