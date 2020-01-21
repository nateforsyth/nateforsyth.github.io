---
layout: post
title: Scaffolding an ASP.NET Core MVC Web App with Identity
date: 2020-01-21 +13:00
published: true
category: Development
tags: [asp.net core, mvc, web application, identity, C#, entry level tutorial, sql database]
---

I came across a cross-post on Reddit today where the OP reposted [a very outdated tutorial to create an ASP.NET login page](https://www.reddit.com/r/csharp/comments/er9n1s/how_to_create_aspnet_login_page_using_c/) revolving around old versions of ASP.NET (think *.aspx* pages, yuck!). I thought it would be a good idea to write a quick blog post detailing how easy it actually is to scaffold a new ASP.NET Core MVC App that includes Identity.

I submitted a reply whereby I greatly simplified the process on the surface:

> File -> New project -> ASP.NET Web Application -> name it -> tick "individual user accounts box" -> create
>
> Press F5
>
> Job done

When I thought about it further I realised I wasn't actually that far off the mark.

Obligatory notes:
- this post is intended for a noob ASP.NET Dev and doesn't require any pre-existing knowledge within the eco-system.
- this post assumes that you have Visual Studio 2019 installed with ASP.NET Core dependencies.
- this is intended to form the path of least resistance.
- what I post here is in no way meant to be the job done version of a production app.


### Create a new project

Start up Visual Studio 2019

Either click:

Create a new project

Or click:

File -> New -> Project

Then select:

ASP.NET Core Web Application -> **Next**

![New ASP NET Core Web App project](/img/1 New ASP.NET Core Web App project.png)


Next, name your project and select the folder to store the files in (simple stuff so far, it doesn't get much more complicated).

![New ASP NET Core Web App project config](/img/2 New ASP.NET Core Web App project config.png)


Now we will configure the app, there are a few steps here:

1. Select Web Application
2. Authentication -> click **Change**
3. Change Authentication -> **Individual User Accounts**
4. Click the drop-down and select **Store user accounts in-app** (if not already selected)
5. Click **OK**
6. Click **Create**

![New ASP NET Core Web App identity](/img/3 New ASP.NET Core Web App identity.png)


### Database schema

Return to Visual Studio, navigate to:

Solution Explorer -> [SolutionName] -> [ProjectName] -> Data -> **Migrations**

Within that folder you'll see your database context and a database schema that will form the basis of your database structure.

If this is your first rodeo, [I suggest you do some more reading on Entity Framework Core](https://docs.microsoft.com/en-us/aspnet/core/data/ef-mvc/intro?view=aspnetcore-3.1).

Don't worry too much for now, as mentioned earlier, this is a very shallow dip of the toes merely intended to highlight ease of creating an identity enabled web app - I digress.

![Scaffolded ASP NET Core Web App](/img/4 Scaffolded ASP.NET Core Web App.png)


### Run the app and register a new user

If you know ASP.NET with Entity Framework already you'll observe that I am doing next is out of order, however the intention is to show what the *ASP.NET Dev virgin* will see when they scaffold the project and simply hit run.

Do that now; press [F5].

Your browser will open with your shiny new, albeit very sparse web application; time to register a new user.

1. Click **Register** in the top right.
2. Populate credentials as you normally would.
3. Then click the **Register* button.

![Run ASP NET Core Web App Register User](/img/5 Run ASP.NET Core Web App Register User.png)

Womp womp! Remember me saying I was doing this out of order? Yeah, I knew this would happen.


### Run the database migration

What's happened here is that the database doesn't yet exist. You'd typically run the migration and (potentially) seed the database with initial values before even bothering to try to run it, but no matter, whilst running we're actually given a very easy way to run the migration.

The error messages we receive are very clear on what has occurred here. Simply click the **Apply Migrations** button.

Alternatively, you could run either of the indicated console commands - but that's not necessary in this case.

![ASP NET Core Web App Migrations Not Run](/img/6 ASP.NET Core Web App Migrations Not Run.png)


Return to Visual Studio and open up the Output window. It won't mean much yet, but you can monitor the progress of the database migration to your local machine. It'll tell you when it's complete.

![ASP NET Core Web App Migrations](/img/7 ASP.NET Core Web App Migrations.png)


### Register a new user, part deux

Return to the running app in your browser and Register a user (again). Pay attention to your password. This is what you'll receive upon successful registration, click **Click here to confirm your account**.

**Why must we do this?**

The default configuration requires a confirmed account, just do it and trust me on this one :D

Don't trust me yet? View the Startup.cs file in the root of the project, look for the section with an invocation of `IServiceCollection.AddDefaultIdentity`

```c#
// ./Startup.cs

{
    // elided
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    // elided
}
```

![ASP NET Core Web App Registered User](/img/8 ASP.NET Core Web App Registered User.png)


### Log into the Web App

You're now ready to log in. Click the **Log in** link, enter your credentials and you're greeted by... err... a greeting!

![ASP NET Core Web App User Logged In](/img/9 ASP.NET Core Web App User Logged In.png)


Click on the greeting, you're taken to the account management portal - this is still all out of the box ASP.NET Core! Cool huh?

![ASP NET Core Web App Manage Account](/img/10 ASP.NET Core Web App Manage Account.png)


### Are you sure this has actually persisted to a database?

I know, I know... this seems way too easy, and you're right. You're probably asking "sure, but has it actually saved my credentials to a database somewhere?"

It has, let's confirm though shall we?

Return to Visual Studio -> View -> **SQL Server Object Browser**

![ASP NET Core Web App View SQL](/img/11 ASP.NET Core Web App View SQL.png)


The **SQL Server Object Browser** will open up as one of your tabs on the left of the Visual Studio IDE.

Navigate to your database instance as follows:

SQL Server -> (localdb)\MSSQLLocalDB -> Databases -> **[database name including your project name]**

Expand Tables, wait for it to populate, then right click **dbo.AspNetUsers** and select **View Data**

You'll be greeted by a single record that matches your newly registered user.

![ASP NET Core Web App Table Data](/img/12 ASP.NET Core Web App Table Data.png)


### In closing

What have we achieved today?

As a noob ASP.NET dev, you've created a new ASP.NET Core MVC Web Application, enabled Individual User Accounts on it, migrated a Code First Database instance using Entiry Framework Core, registered a user persisting the record to the new database and been able to log that user on.

If you're new to this, congratulations, that's quite an achievement considering how simple it was.

The barrier for entry for ASP.NET is continually reducing; Microsoft, and indeed the entire open source community building .NET Core are continually stiriving to make it easy for any dev to get started with this eco-system.

Job's a goodun!
