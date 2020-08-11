---
layout: post
title: Modernising a legacy application - part deux - securing the API with RBAC and JWT
date: 2020-08-11 +13:00
published: false
category: Development
tags: [asp.net framework, mvc, web application, identity, RBAC, C#, sql database, entity framework, linq, legacy, intermediate, prototyping, rapid development, proof of concept, real world, JWT]
---

Hi again interweb friends. When [we left off last time](https://dreamsof.dev/2020-07-22-modernising-legacy-app-aspnet48-jwt-sql-efdatafirst-react-1/) we had just finished off the the initial build of our data layer, and had exposed a database table via ASP.NET Web API end points. We'd also run through a few test scenarios on the API using Postman.

We will continue today by further fleshing out this implementation by securing the API using RBAC and JWT. As previously mentioned, the implementations I will be highlighting the process of doing so with a pre-existing database structure that already includes user related tables. We will **not** be using the Microsoft provided Identity library(ies). This tutorial is meant to highlight a real world scenario where a legacy application needs to be retrofit with new functionality.

Before we get started though, here's the list of what was done previously for a reminder:

- scaffolded a new project to integrate an existing SQL Server Database into ASP.NET.
- configured ASP.NET Framework to connect to SQL Server.
- connected to a SQL Database from ASP.NET Framework.
- built a POCO model based upon a SQL Database usign Entity Framework Data First Migrations.
- created a helper class to assist us in implementation of DRY principles (Don't Repeat Yourself).
- begun to encapsulate the Data layer of our application to separate it from the other layers of the application.
- tested the API end points using Postman and have successfully retrieved data from the database.
- implemented a basic View Model used to provide data within the API request body to our Controller that we can further validate.
- implemented code to handle where data is not found or properties from within a request body are invalid, and provided a relevant HTTP return Status Code.


### Implementing User log in and JSON Web Token generation

Our next logical step is to add functionality to allow for us to pass a username and password into an API end-point, validate whether the credentials are valid, that the user has access to the system and build a set of Claims that will be encoded and added to a JSON Web Token (JWT, pronounced "jot") for further dissemination.

We've got a little prep work to do before we can provision our Controller.

We must add some more boilerplate to our project so that ASP.NET knows to surface token generation and validation functionality.

Add a new class to the root folder of the project, call it `Startup.cs`.

// TODO was this a scaffolded item???

```cs
// ./Startup.cs

public class Startup
{
    public void Configuration(IAppBuilder app)
    {
        app.UseJwtBearerAuthentication(
            new JwtBearerAuthenticationOptions
            {
                TokenValidationParameters = new TokenValidationParameters()
                {
                    ValidateIssuer = true,
                    ValidateAudience = true,
                    ValidateIssuerSigningKey = true,
                    ValidIssuer = ConfigurationManager.AppSettings["jwtIssuer"],
                    ValidAudience = ConfigurationManager.AppSettings["jwtAudience"],
                    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(ConfigurationManager.AppSettings["jwtKey"]))
                }
            });
    }
}
```

Now open up the `WebApiConfig.cs` file and refactor the `Register` method to match the following:

```cs
public static class WebApiConfig
{
    public static void Register(HttpConfiguration config)
    {
        config.EnableCors();

        // Web API configuration and services
        config.SuppressDefaultHostAuthentication();
        config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));

        // Web API routes
        config.MapHttpAttributeRoutes();

        config.Routes.MapHttpRoute(
            name: "DefaultApi",
            routeTemplate: "api/{controller}/{id}",
            defaults: new { id = RouteParameter.Optional }
        );
    }
}
```

Add a new model class to your ViewModels folder called **LoginViewModel**.

```cs
// ./Models/ViewModels/LoginViewModel.cs

public class LoginViewModel
{
    public string UserAccountLoginName { get; set; }
    public string UserAccountPassword { get; set; }
    public string RefreshToken { get; set; } // this will not be used yet
}
```

We're now going to Scaffold a new blank API Controller, call it **JwtController**.

Refactor it to extend from `DataLayerHelper`, and add constructors to suit per previous the example. Additionally, add a method stub that'll form the basis of our token retrieval functionality, called `GenerateToken`.

```cs
// ./Controllers/JwtController.cs

public class JwtController : DataLayerHelper
{
    public JwtController() : base() { }
    public JwtController([yourDatabaseName]Context) : base(context) { }
    
    /// <summary>
    /// Generate a JWT to access the demo system
    /// </summary>
    /// <param name="loginVM"></param>
    /// <returns></returns>
    [HttpPost]
    [Route("api/token/generate")]
    public async Task<IHttpActionResult> GenerateToken([FromBody] LoginViewModel loginVM)
    {    
        // boilerplate elided for brevity      
    }
}
```

The `GenerateToken` method will receive the username and password of the user, as well as have the ability to retrieve a refresh token. This is passed in as a model of type `LoginViewModel`, in JSON format; e.g.

```json
{
    "UserAccountLoginName": "string",
    "UserAccountPassword": "string",
    "RefreshToken": "string"
}
```




### Adding a User Controller to our API



