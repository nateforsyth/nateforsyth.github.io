


## But first, some background

If you're not aware, the default Content Type on a Library is used when a user clicks to create a new Spreadsheet for example. This is good, but we've lost that in provisioning... let's get that back.

Here's the default on a clean Library:

![Library new menu item template - default](/img/LibNewItemTemplate01.png)

Here's what happens when we've manipulated the Library Content Types:

![Library new menu item template - post powershell](/img/LibNewItemTemplate02.png)

And here's what we're trying to achieve:

![Library new menu item template - end result](/img/LibNewItemTemplate03.png)

**Why am I doing this in Azure Functions?**

We're building an automated provisioning tool that mimics our PowerShell provisioning functionality. It's tied to a request form and uses Azure Logic Apps to control programmatic flow, and Azure Functions to invoke the heavy lifting required during Site Provisioning.


## Some assumptions

I am assuming here that you're familiar with Azure Functions, if you're not [check out this primer](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-vs).

This post is C# specific, so I am assuming you're comfortable with the language, eco-system and Visual Studio.

I'm also assuming that you have an existing Azure Functions project that you've deployed to Azure - I won't be covering that here.


## Preparation


First up, we're going to do a bit of prep work within your *existing* Azure Functions project.

Create a new class to be used as a template for your data structure, as below:

~~~c#
public class ListNewItemMenuTemplateItem
{
    [JsonProperty("title")]
    public string Title { get; set; }
    [JsonProperty("templateId")]
    public string TemplateId { get; set; }
    [JsonProperty("visible")]
    public bool Visible { get; set; }
    [JsonProperty("contentTypeId")]
    public string ContentTypeId { get; set; }
    [JsonProperty("isContentType")]
    public bool IsContentType { get { return !string.IsNullOrWhiteSpace(this.ContentTypeId); } }

    public ListNewItemMenuTemplateItem(string title, string templateId, bool visible, string contentTypeId = "")
    {
        Title = title;
        TemplateId = templateId;
        Visible = visible;
        ContentTypeId = contentTypeId;
    }
}
~~~

Next, we have to add another class that has no other purpose than to prepare a List of **Microsoft.SharePoint.Client.View.NewDocumentTemplates** compatible items (from the previous class). This is a utility class, you can do this in any way you prefer.

It's worth noting now that the **NewDocumentTemplates** property on a **Microsoft.SharePoint.Client.View** is a string that holds a JSON object - that's what we're preparing with this class.

For further reference [check out this blog post regarding a similar PowerShell implementation](https://cann0nf0dder.wordpress.com/2019/03/24/programmatically-change-the-new-menu-in-sharepoint-online-using-powershell/).

Add the class, as below:

~~~c#
public static class MenuItemTemplateFactory
{
    public static List<ListNewItemMenuTemplateItem> BuildDefaultMenuItemTemplate()
    {
        var listNewMenuItemModel = new List<ListNewItemMenuTemplateItem>();

        // create default Item templates and add to listNewMenuItemModel
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Folder", "NewFolder", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Word Document", "NewDOC", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Excel Workbook", "NewXSL", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Power Point presentation", "NewPPT", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("One Note Notebook", "NewONE", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Visio Drawying", "NewVSDX", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Forms for Excel", "NewXSLForm", true));
        listNewMenuItemModel.Add(new ListNewItemMenuTemplateItem("Link", "Link", true));

        return listNewMenuItemModel;
    }
}
~~~


## Add functionality to invoke the build of a New Item Menu on a Library


It's now time to add the functionality to actually persist the items to the drop down menu off the New button on the Library.

~~~c#
private bool SetListNewMenuTemplates(string targetWebUrl, string userName, SecureString pwd, string listTitle, Logger logger)
{
    bool result = false;

    try
    {
        using (var ctx = new ClientContext(targetWebUrl))
        {
            ctx.Credentials = new SharePointOnlineCredentials(userName, pwd);
            ctx.RequestTimeout = Timeout.Infinite;

            // Just to output the site details.
            Web web = ctx.Web;
            ctx.Load(web, w => w.Title);
            ctx.ExecuteQueryRetry();

            // Get the List/Library by title.
            List list = ctx.Web.Lists.GetByTitle(listTitle);

            // Load List/Library Content Types
            ctx.Load(list,
                l => l.ContentTypes);
            ctx.ExecuteQueryRetry();

            // Load List /Library Views
            ctx.Load(list,
                l => l.Views);
            ctx.ExecuteQueryRetry();

            View defaultView = null;

            foreach (var view in list.Views)
            {
                if (view.DefaultView)
                {
                    defaultView = view;
                }
            }

            var listNewMenuItemModel = MenuItemTemplateFactory.BuildDefaultMenuItemTemplate();

            // iterate over List Content Types and add to listNewMenuItemModel (ignoring Folder and Hudden CTs)
            foreach (var listCt in list.ContentTypes)
            {
                if (listCt.Name == "Folder" || listCt.Hidden) {} // ignore Folder CT (as it's already added) or when a CT is set to Hidden
                else
                {
                    var menuItem = new ListNewItemMenuTemplateItem(listCt.Name, "NewDOC", true, listCt.Id.StringValue);

                    listNewMenuItemModel.Add(menuItem);
                }
            }

            var listNewMenuItems = JsonConvert.SerializeObject(listNewMenuItemModel, Formatting.None,
                new JsonSerializerSettings
                {
                    NullValueHandling = NullValueHandling.Ignore
                });

            if (defaultView != null)
            {
                defaultView.NewDocumentTemplates = listNewMenuItems;

                defaultView.Update();
                ctx.ExecuteQueryRetry();
            }
            else
            {
                logger.Warning($"SetListNewMenuTemplates: InstanceID: {InstanceID}: Default view was not found, List: '{listTitle}'");
                result = false;
            }
        }
    }
    catch (ServerUnauthorizedAccessException sUAEx)
    {
        logger.Error(sUAEx, $"SetListNewMenuTemplates: InstanceID: {InstanceID}, List: '{listTitle}'");
        result = false;
    }
    catch (Exception ex)
    {
        logger.Error(ex, $"SetListNewMenuTemplates: InstanceID: {InstanceID}, List: '{listTitle}'");
        result = false;
    }
    return result;
}
~~~


we're going to scaffold a new Azure Function within Visual Studio - this is the default, we'll come back to this later.
