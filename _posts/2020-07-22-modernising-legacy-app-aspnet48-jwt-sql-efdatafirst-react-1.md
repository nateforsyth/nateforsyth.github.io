---
layout: post
title: Modernising a legacy application using ASP.NET 4.8, JWT, SQL Server, Entity Framework Data First and a React front-end - part 1
date: 2020-07-22 +13:00
published: true
category: Development
tags: [asp.net framework, mvc, web application, identity, RBAC, C#, sql database, entity framework, linq, legacy, intermediate, prototyping, rapid development, proof of concept, real world]
---

I intend for this tutorial series to highlight what I believe to be some very important aspects of dealing with legacy ASP.NET Web Applications, how to modernise them with a wrapper of sorts that can handle things like [JWT session/passport Authorisation](https://www.webskeleton.com/webdev/2019/10/22/JWT-Primer-and-Should-You-Use-It.html), securely exposing the existing database as a data layer while abstracting away all of the gubbins and heavy lifting into a business layer. The intention is for this wrapper to be able to encapsulate the entirety of a legacy product and provide immense security and usability benefits over the current implementation with minor refactoring to handle dependency injection (though I may not cover that).

Later on I'll be implementing a React front end so stick around for that too!

One final point. This tutorial is going to be long so I will have to cut off each post, hopefully it's not too jarring. It also may be too much for newer developers. I'll link to primer tutorials where I think necessary.


### Background and why this tutorial

I've noticed recently that there are hundreds of high quality ASP.NET Core based Web Api project tutorials on the web. There are far less up-to-date ASP.NET Framework tutorials and discussions. I've recently been working within legacy ASP.NET, and although framework versions have been updated, it could only be as far as 4.8 due to the legacy nature of the server environment they're hosted upon. This has necessitated a lot of research and development to be able to build a secure proof of concept to justify further investment into the product.

Finally, this tutorial series is meant to give a birds eye view into what the day-to-day may look like for a developer working around a legacy code base who has been given remit to prototype a proof of concept where nothing is off the table.


### Caveats

I will be touching on some very important security related aspects within this tutorial series, but I am intentionally not fleshing them all out because this is meant to highlight rapid development of a prototype for a proof of concept intended to drive discussions revolving around further time investment. Please keep that in mind if you're following along.

All code within this tutorial series is conceptual, follows best practice as much as I can, and is written for tutorial purposes outside of any workplace. Any of this information can be found by trawling many other sources, I am merely bringing it all together to highlight some very specific use cases.


### Assumptions

I'm going to make a few assumptions about your skill level:

- you have installed [SQL Server 2019](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) and are [familiar with SQL in general](http://w3schools.sinsixx.com/web/web_sql.asp.htm).
- you have installed [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).
- you have an existing database with a basic structure we can use to build upon; you need to have a User table which includes a UserID and Password at a minimum. I do provide a SQL snippet below to create new database tables.
- you are familiar with [C#](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/intro-to-csharp/).
- you are familiar with [ASP.NET Framework](https://dotnet.microsoft.com/learn/aspnet/hello-world-tutorial/intro) and can scaffold a project (4.8).
- you have used [Entity Framework](https://www.entityframeworktutorial.net/what-is-entityframework.aspx), and understand the fundamental differences between **Code First** and [**Data First**](https://docs.microsoft.com/en-us/ef/ef6/modeling/designer/workflows/database-first) (we'll be using the latter to integrate with a pre-existing database/application).
- you are familiar with LINQ [Language INtegrated Queries](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries).
- you are using Visual Studio 2019; if not, some things may differ (I'll leave you to sort that out).
- you are comfortable using the [NuGet Package Manager Console](https://docs.microsoft.com/en-us/nuget/consume-packages/install-use-packages-powershell) within Visual Studio.
- you have used and are comfortable with [Postman](https://www.guru99.com/postman-tutorial.html).


### First steps

Scaffold a new ASP.NET 4.8 Web API project within Visual Studio.

Install the following dependencies using the package manager console:

- System.IdentityModel.Tokens.Jwt.5.5.0
- Microsoft.Owin.Security.Jwt.4.1.0
- Microsoft.AspNet.WebApi.Owin.5.2.7
- Microsoft.Owin.Host.SystemWeb.4.1.0


#### Run the project and test the out of the box endpoint with Postman

Press F5 to launch your app in debug mode.

Once it has started up, open Postman and do a GET request as follows:

```json
// POSTMAN; End point: https://localhost:[yourPort]/api/values

Response:
200 OK

Response body:
[
    "value1",
    "value2"
]
```


#### End result of the first steps

You have scaffolded a *new* web application shell that we can continue to work with, you've successfully run the app, and you've retrieved some JSON data within Postman.

Let's take a look at the Controller that was provided to us when we scaffolded this project. This is what we have just hit with Postman.

```cs
./Controllers/ValuesController.cs

public class ValuesController : ApiController
{
    // GET api/values
    public IEnumerable<string> Get()
    {
        return new string[] { "value1", "value2" };
    }

    // remainder elided for brevity
}
```

Note how we have a comment dictating the naming convention for the endpoint; api/values. We'll further refine this later on.

You can go ahead and delete **ValuesController** now, we don't need it anymore.

Gotten this far? Great, let's continue to build out the back end.


### Beginning to implement DRY and SOC

Hopefully you've heard about it before, DRY is the principle of [Don't Repeat Yourself](https://www.c-sharpcorner.com/article/software-design-principles-dry-kiss-yagni/), which in a nutshell means that there is some code that you'll use almost *everywhere*, and it should be centralised and reused. You shouldn't necessarily reuse *everything*, but for the purposes of this tutorial we are going to implement a helper class shared by all of our API Controllers that will expose common useful functionality.

Separation of Concerns (SOC) is the process of ensuring that your various layers of architecture are decoupled from each other. We do this so that our data layer can do its job (creating, reading, updating and deleting of data) with no knowledge of the business layer for example. Conversely, the business layer doesn't care how the data is retrieved, only that it can request something from the data layer, and what is returned is what it expects. Again, the Presentation layer doesn't care about how the Business Layer manipulates data, merely that it can retrieve data so that it can render what the end user sees.

Let's build the shell of our helper class.

Create a new folder called **Helpers**.

Within the new folder, create a new class called **DataLayerHelper**.

We are going to add some regions within this new class, this is what we'll continue to build out. Note how the new class is extending ApiController, you'll see why soon.

```cs
// ./Helpers/DataLayerHelper.cs

public class DataLayerHelper : ApiController
{
    #region Constructors
    #endregion

    #region Shared Database retrieval functionality
    #endregion

    #region Shared Database update and patch functionality
    #endregion

    #region Shared Claim validation functionality
    #endregion

    #region Token extension methods
    #endregion
}
```


### Building the data model from a database context using Entity Framework

We need to build our data layer model that will be used to reflect the state of our database.

Remember, we are using Data First Migrations for Entity Framework to highlight a specific real life example where we are developing against a pre-existing database.


#### If you don't already have a database

If you haven't already got a database create one within SSMS, we will then create tables and seed them with some data - run these commands:

```sql

-- Create a Users table
CREATE TABLE tblAppUsers(
  UserId INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
  FirstName NVARCHAR(30) NOT NULL,
  LastName NVARCHAR(30) NOT NULL,
  UserName NVARCHAR(30) NOT NULL,
  Email NVARCHAR(50) NOT NULL,
  Password NVARCHAR(20) NOT NULL,
  CreatedDate DATETIME DEFAULT(GetDate()) NOT NULL,
  PRIMARY KEY (ID))
GO

-- Seed the Users table with data
INSERT INTO Users(FirstName, LastName, UserName, Email, Password) 
  VALUES ('Task', 'Admin', 'TaskAdmin', 'TaskAdmin@company.com', '$adminOfTasks@2020')
  VALUES ('Task', 'User', 'TaskUser', 'TaskUser@company.com', '$userOfTasks@2020')


-- Create a Tasks table
CREATE TABLE tblAppTasks(
  TaskId INT IDENTITY(1,1) Primary Key,
  TaskName NVARCHAR(100) NOT NULL,
  Category NVARCHAR(100),
  OwnerUserId INT,
  CreatedDate DATETIME DEFAULT(GetDate()) NOT NULL,
  FOREIGN KEY (OwnerUserId) REFERENCES Users(ID),
  PRIMARY KEY (ID))
GO

-- Seed the Tasks table with data
INSERT INTO Tasks(Name, Category)
  VALUES
    ('Buy some groceries', 'Shopping', 1),
    ('Put petrol into the car', 'Transportation', 1),
    ('Pay the insurance', 'Bills', 1),
    ('Pay the mortgage', 'Bills', 1),
    ('Clean the bathroom', 'Housekeeping', 1),
    ('Mow the lawns', 'Housekeeping', 2)
```


#### Building the data model in ASP.NET

Return to Visual Studio.

Open the NuGet Package Manager Console; Tools > NuGet Package Manager > **Package Manager Console** and run the following command once it's opened.

`Scaffold-DbContext “Server=******;Database=[yourDatabaseName];Integrated Security=True” Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models`

All going according to plan you'll notice that a new folder has been created called **Models**, and within the folder will be 3 files:

```cs
/*
  ./Models/Users.cs
    This class is a model of the database table tblAppUsers
 */

public partial class tblAppUsers
{
    public int UserId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string UserName { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
    public string CreatedDate? { get; set; }
}
```

```cs
/*
  ./Models/Tasks.cs
    This class is a model of the database table tblAppTasks
 */

public partial class tblAppTasks
{
    public int TaskId { get; set; }
    public string TaskName { get; set; }
    public string Category { get; set; }
    public int OwnerUserId { get; set; }
    public string CreatedDate? { get; set; }
}
```

```cs
/*
  ./Models/[yourDatabaseName]Context.cs
    This class is the database context of your database which facilitates connectivity between your ASP.NET back end and your database
 */
 
public partial class [yourDatabaseName]Context : DbContext
{
    public [yourDatabaseName]Context()
    {
    }

    public [yourDatabaseName]Context(DbContextOptions<[yourDatabaseName]Context> options)
        : base(options)
    {
    }
    
    public virtual DbSet<tblAppUsers> Users { get; set; }
    public virtual DbSet<tblAppTasks> Tasks { get; set; }
    
    // remainder elided for brevity
}
```


#### Configuration of database connectivity from ASP.NET

Righty, you've now built your model classes within your project, and now it's time to connect to the database and retrieve some data. Slow down slick, we still haven't configured connectivity to your database! Let's do that now.

We need to add some new App Settings and a Connection String to our Web.config file:

```xml
// ./Web.config

<configuration>
  <connectionStrings>
    <!-- If there is already a Connection String defined, replace it with this -->
	  <add name="[yourDatabaseName]Context" connectionString="Data Source=localhost;Initial Catalog=[dbCatalog];Persist Security Info=True;User ID=[dbUserId];Password=[dbPassword];" providerName="System.Data.SqlClient" />
  </connectionStrings>
  <appSettings>
    <!-- Other entries elided for brevity -->
    <add key="dbCatalog" value="[yourDatabaseName]" />
    <add key="dbUserId" value="[yourDatabaseUserId]" />
    <add key="dbPassword" value="[yourDatabasePassword]" />
    
    <!-- Add these too, we will explain them later -->
    <add key="jwtKey" value="" />
    <add key="jwtIssuer" value="" />
    <add key="jwtAudience" value="" />
    <add key="jwtValidForDays" value="" />
    <add key="jwtValidForMinutes" value="" />
    <add key="refreshValidForMinutes" value="" />
  </appSettings>
<configuration>
```

Return to your Context class, locate the **OnConfiguring** method, and replace the contents as follows:

```cs
/*
  ./Models/[yourDatabaseName]Context.cs
 */

using System.Configuration; // add this using reference

public partial class [yourDatabaseName]Context : DbContext
{
    // Constructors elided for brevity
    
    // DbSet methods elided for brevity

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        var catalog = ConfigurationManager.AppSettings["dbCatalog"];
        var userId = ConfigurationManager.AppSettings["dbUserId"];
        var password = ConfigurationManager.AppSettings["dbPassword"];

        var connectionString = ConfigurationManager.ConnectionStrings["[yourDatabaseName]Context"].ConnectionString
            .Replace("[dbCatalog]", catalog)
            .Replace("[dbUserId]", userId)
            .Replace("[dbPassword]", password);

        if (!optionsBuilder.IsConfigured)
        {
            optionsBuilder.UseSqlServer(connectionString);
        }
    }
}
```

The purpose of this code is to use App Settings from within IIS (when it's finally deployed) to be able to target any relevant database by just changing those variables. Pretty cool huh?


#### Actually connecting and testing the connection

We're still not done yet.

Before we can test this connectivity, we'll need to build a new Controller which we will use to encapsulate the database retrieval functionality and expose the data via an endpoint.

We need to make some adjustments to our **DataLayerHelper** class.

Locate the Constructors region and add the following code

```cs
// ./Controllers/DataLayerHelper.cs

public class DataLayerHelper : ApiController
{
    #region Constructors
    protected readonly [yourDatabaseName]Context context _context;

    public DataLayerHelper() // parameterless constructor
    {
        _context = new [yourDatabaseName]Context context();
    }

    public DataLayerHelper([yourDatabaseName]Context context context)
    {
        _context = context;
    }
    #endregion

    // remainder elided for brevity
}
```

Now, locate the **Shared Database retrieval functionality** region and add the following code.

```cs
// ./Helpers/DataLayerHelper.cs

public class DataLayerHelper : ApiController
{
    // prior elided for brevity
    
    #region Shared Database retrieval functionality    
    protected async Task<IQueryable<tblAppTasks>> GetAllTasks()
    {
        var tasks = await _context.tblAppTasks
            .ToListAsync();

        return tasks;
    }
    
    // retrieve Task items from database owned by a particular user; we won't use this until next time
    protected async Task<IQueryable<tblAppTasks>> GetTasksByOwnerIdAsync(int userId)
    {
        var userTasks = await _context.tblAppTasks
            .Where(ut => ut.OwnerUserId == userId)
            .ToListAsync();

        return userTasks;
    }
    #endregion

    // remainder elided for brevity
}
```

Can you see what we're doing now? We are providing implementations for what will end up being common functionality used many times in a single centralised file.

We now need to add a new model class that will help us to drive data acquisition from the body of API requests. Add a new folder to your **Models** folder called **ViewModels**, and then add a new class called **TasksViewModel**. This is a very simple model (for now) that will indicate that we are only going to use a passed UserId property.

```cs
// ./Models/ViewModels/TasksViewModel.cs

public class TasksViewModel
{
    public string UserId { get; set; }
}
```

Create a new class within the **Controllers** folder called **TaskApiController** and add the following code, paying particular attention to the class we're extending, **DataLayerHelper**. Shared functionality is available to this Controller.

```cs
public class TaskApiController : DataLayerHelper
{
    public TaskApiController() : base() { }
    public TaskApiController([yourDatabaseName]Context context) : base(context) { }

    [HttpGet]
    [Route("api/tasks/getAll")]
    public async Task<IHttpActionResult> GetAllTasks()
    {
        try
        {
            var allTasks = await GetAllTasks(); // shared functionality from our Helper class
            
            if (allTasks != null) // tasks retrieved, return them
            {
                return Ok(allTasks); // 200
            }
            else // no tasks found, return Not Found (this shouldn't occur if you've seeded the database correctly)
            {
                return NotFound(); // 404
            }
        }
        catch (Exception ex) // something went wrong, return an internal server error
        {
            return InternalServerError(ex); // 500
        }
    }

    [HttpGet]
    [Route("api/tasks/getAllForUser")]
    public async Task<IHttpActionResult> GetTasksForUser([FromBody] TasksViewModel tasksViewModel) // note that we're using the new View Model here
    {
        try
        {
            if (!string.IsNullOrEmpty(tasksViewModel.UserId)) // ensure a UserId has been provided
            {
                int.TryParse(tasksViewModel.UserId, out int ownerId);
                
                if (ownerId > -1) // UserId successfully parsed
                {
                    var allTasksForUser = await GetTasksByOwnerIdAsync(); // shared functionality from our Helper class
            
                    if (allTasksForUser != null) // tasks retrieved for user, return them
                    {
                        return Ok(allTasksForUser); // 200
                    }
                    else // no tasks found for user, notify no content
                    {
                        return NoContent(); // 204
                    }
                }
                else // user not parsed correctly, return not found
                {
                    return NotFound(); // 404
                }
            }
            else // UserId has not been provided, return not found
            {
                return NotFound(); // 404
            }            
        }
        catch (Exception ex) // something went wrong, return an internal server error
        {
            return InternalServerError(ex); // 500
        }
    }
}
```


### Test database connectivity and API using Postman

Wow, that's been a lot of work (and even more typing on my part). Now comes the fun part; let's test!

Run your project from within Visual Studio by pressing **F5**.

Return to Postman and run the following requests:

#### Retrieve all Tasks from the Database

```json
// POSTMAN; End point: https://localhost:[yourPort]/tasks/getAll

Request body:
null

Response:
200 OK

Response body:
[
    {
        "TaskId": 1,
        "TaskName": "Buy some groceries",
        "Category": "Shopping",
        "OwnerUserId": 1,
        "CreatedDate": 2020-07-23T10:13:00.933
    },
    {
        "TaskId": 2,
        "TaskName": "Put petrol into the car",
        "Category": "Transportation",
        "OwnerUserId": 1,
        "CreatedDate": 2020-07-23T10:13:00.933
    },
    {
        "TaskId": 3,
        "TaskName": "Pay the insurance",
        "Category": "Bills",
        "OwnerUserId": 1,
        "CreatedDate": 2020-07-23T10:13:00.933
    },
    {
        "TaskId": 4,
        "TaskName": "Pay the mortgage",
        "Category": "Bills",
        "OwnerUserId": 1,
        "CreatedDate": 2020-07-23T10:13:00.933
    },
    {
        "TaskId": 5,
        "TaskName": "Clean the bathroom",
        "Category": "Housekeeping",
        "OwnerUserId": 1,
        "CreatedDate": 2020-07-23T10:13:00.933
    },
    {
        "TaskId": 6,
        "TaskName": "Mow the lawns",
        "Category": "Housekeeping",
        "OwnerUserId": 2,
        "CreatedDate": 2020-07-23T10:13:00.933
    }
]
```

#### Retrieve all Tasks from the Database for a specific User

```json
// POSTMAN; End point: https://localhost:[yourPort]/tasks/getAllForUser

Request body:
raw > JSON
{
    "UserId": "2"
}

Response:
200 OK

Response body:
[
    {
        "TaskId": 6,
        "TaskName": "Mow the lawns",
        "Category": "Housekeeping",
        "OwnerUserId": 2,
        "CreatedDate": 2020-07-23T10:13:00.933
    }
]
```

#### Retrieve all Tasks from the Database for a specific User that doesn't exist

```json
// POSTMAN; End point: https://localhost:[yourPort]/tasks/getAllForUser

Request body:
raw > JSON
{
    "UserId": "3" // note that we are intentionally providing a UserId that doesn't own any Task records
}

Response:
204 No Content

Response body:
[]
```

#### Retrieve all Tasks from the Database but don't specify a UserId

```json
// POSTMAN; End point: https://localhost:[yourPort]/tasks/getAllForUser

Request body:
raw > JSON
{
    "UserId": "" // note that we are intentionally not providing a UserId
}

Response:
404 Not Found

Response body:
[]
```


### In closing

What have we achieved today?

- scaffolded a new project to integrate an existing SQL Server Database into ASP.NET.
- configured ASP.NET Framework to connect to SQL Server.
- connected to a SQL Database from ASP.NET Framework.
- built a POCO model based upon a SQL Database usign Entity Framework Data First Migrations.
- created a helper class to assist us in implementation of DRY principles (Don't Repeat Yourself).
- begun to encapsulate the Data layer of our application to separate it from the other layers of the application.
- tested the API end points using Postman and have successfully retrieved data from the database.
- implemented a basic View Model used to provide data within the API request body to our Controller that we can further validate.
- implemented code to handle where data not found or properties from within a request body are invalid, and provided a relevant HTTP return Status Code.

Whew! This has been a long one! But we've set the stage for all articles that are to follow.

Let me know if you've got questions. See you next time...

Job's a goodun!
