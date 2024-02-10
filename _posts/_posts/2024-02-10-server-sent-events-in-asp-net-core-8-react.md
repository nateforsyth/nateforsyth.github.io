---
layout: post
title: Server Sent Events in ASP.NET Core 8 and React
date: 2024-02-10 +13:00
published: true
category: Dev
tags: [job search, asp.net core, web api, server sent events, react, repository pattern]
---

It's no secret that I lost my job before Xmas, with that comes the need to interview and complete take-home projects. This blog post is intended to provide an overview of the project, some trials and tribulations, and a bit of detail revolving around my implementation. This is not intended to be a tutorial, but feel free to answer any questions.

# Server Sent Events in ASP.NET Core 8 and React

This was a project to support my job search.

I was given this project as a pre-technical interview task.

I only had a handful of days to complete this task, which I have done. I struggled with Docker (which I hadn't been exposed to) due to WSL on Windows 11. Time constraints mean that this is far from perfect, but it sufficed for its intended purpose.

# Project requirements

The project requirements are pretty generic, I've seen very similar posted on Reddit before, and suspect it's a lift-and-shift from a repository somewhere.

## Overview

We invite you, a skilled software engineer, to apply for a position that demands proficiency in .NET Core for server-side development, web applications, and edge and IoT compute. Your task is to tackle a challenging problem: implement a messaging system facilitating message distribution between open web pages, without necessitating user actions (e.g., utilizing server-sent sockets). While the selection of tools is flexible, we strongly recommend leveraging .NET Core whenever feasible.

## Challenges

1. Implement a robust .NET Core server-side solution.
2. Craft web applications with a tool of your choice, emphasizing a messaging system that efficiently distributes messages among open web pages, eliminating the need for user actions (e.g., using server-sent sockets).
3. Develop a web application enabling users to set their name, compose messages, and view previous messages, complete with date-time, user, and message details in a scrollable list.
4. Ensure message data persistence in a second Docker container, orchestrating two containers—one for the web service/API and another for data persistence. Messages displayed upon user page reloads should be retrieved from the second Docker container. Persistence is required only as long as the containers are running and can be reset between restarts.

## Requirements

- Demonstrate your development process by committing work to a cloud repository, showcasing regular commits throughout the development lifecycle.
- Design the solution to be easily cloneable. The cloned repository should encompass all necessary files, allowing the solution to be run with a single command line.
- If dependencies are necessary for running the solution, comprehensively document them in a README file to facilitate straightforward installation.

## Evaluation

The primary objective is not merely to identify the best solution but to gain insights into your working process. Feel free to pose questions throughout the process. If selected for a technical interview, you may be prompted to elucidate and demonstrate your solution. Expect discussions on the decisions made during the implementation, focusing on understanding your approach, problem-solving prowess, and the rationale behind your choices.

# Implementation

I chose to implement multiple containers within Visual Studio, including a React app (which was external until it had been implemented). You'll note that this was in a separate folder off the root of the project until [Commit #ca3cfd1](https://github.com/nateforsyth/Project-SEE/commit/ca3cfd12def5a169b921e4e744822911f1877575) where I moved it into the solution (by way of scaffolding a new .NET React stand-alone project for the VS gubbins).

I am new to micro-services, and have a new appreciation for both the simplicity of running multiple containers, and the complexity of understanding how it all fits together in the lead-up to that point.

In a nutshell, the Messaging*App* I have come up with has a React front-end, which directly interfaces with a Messaging*Scheduler* to post messages to, and retrieve the last 24 hours of messages from when a User connects. The Messaging*Scheduler* contains a `Repository` pattern.

You'll note in `Project requirements > Challenges > 4` that the following is stated:

> Persistence is required only as long as the containers are running and can be reset between restarts

The `Repository` pattern was chosen in case I or someone else decides to further flesh out the data layer of this app by adding database functionality. This functionality is mocked in-memory and persists only while the container is running. It uses a [ConcurrentDictionary](https://learn.microsoft.com/en-us/dotnet/api/system.collections.concurrent.concurrentdictionary-2?view=net-8.0) to do this.

The Messaging*Scheduler* is configured as a service. The service utilises [server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events) to Post messages within the Repository to the Messaging*Server*, which adds the deserialised `Message` instances to a CuncurrentDictionary Queue on the Messaging*Server*.

The Messaging*Server* then accepts *subscriptions* from a Client app, and broadcasts anything it has queued to connected Clients.

Upon connection, the Client app directly interrogates the Messaging*Scheduler* to retrieve the last 24 hours of Message data, which is pre-populated for the User.

**Important**: There is currently no access control on the *live* Messaging*Scheduler* or Messaging*Server* cloud services as it wasn't a part of the requirements. I'm aware that this is risky, however as there is no data persistence, it's trivial for me to bring down either service if necessary to restore it to a clean state. See `Known TODOs` below.

## messagingapp

The `messagingapp` is a standard React App with TypeScript and nothing else.

### Dependencies

These are the dependencies for the React app at the time of writing this README.

```json
// ./package.json
{
  "name": "messagingapp",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@auth0/auth0-react": "^2.2.4",
    "@emotion/react": "^11.11.3",
    "@emotion/styled": "^11.11.0",
    "@hookform/resolvers": "^3.3.4",
    "@microsoft/fetch-event-source": "^2.0.1",
    "@mui/icons-material": "^5.15.7",
    "@mui/lab": "^5.0.0-alpha.163",
    "@mui/material": "^5.15.7",
    "@testing-library/jest-dom": "^5.17.0",
    "@testing-library/react": "^13.4.0",
    "@testing-library/user-event": "^13.5.0",
    "@types/jest": "^27.5.2",
    "@types/node": "^16.18.79",
    "@types/react": "^18.2.52",
    "@types/react-dom": "^18.2.18",
    "axios": "^1.6.7",
    "linq-to-typescript": "^11.1.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-hook-form": "^7.50.0",
    "react-scripts": "5.0.1",
    "typescript": "^4.9.5",
    "web-vitals": "^2.1.4",
    "zod": "^3.22.4"
  },
  "scripts": {}, // elided for brevity
  "eslintConfig": {}, // elided for brevity
  "browserslist": {}, // elided for brevity
  "devDependencies": {
    "@babel/plugin-proposal-private-property-in-object": "^7.21.11"
  }
}
```

#### Justifying dependencies

There are only a few dependencies that warrant further justification.

##### Auth0

While I don't permanently store any user data on the scheduler or server (see relevant sections), I did need a way to validate who a person is. I chose [Auth0](https://auth0.com/) for this because I didn't want to have to provide any functionality within this implementation to create user accounts and the like.

Auth0 is extremely simple to set up, and they provide an npm package to simplify usage; [@auth0/auth0-react](https://www.npmjs.com/package/@auth0/auth0-react).

The beauty of using this dependency is that it will make it (relatively) trivial to flesh out more advanced functionality in future if necessary.

##### @microsoft/fetch-event-source

Microsoft provides [fetch-event-source](https://www.npmjs.com/package/@microsoft/fetch-event-source) as a standard `EventSource` consumption package for TypeScript.

##### MUI

I chose [Material UI](https://mui.com/) due to my comfort in using it - it's simple, consistent and elegant.

##### Axios

[Axios](https://www.npmjs.com/package/axios) is a recognised go-to for simplifying the consumption of APIs in node based projects. It provides an elegant and easy to use syntax.

I only use this for a couple of the Controller Action invocations within this app:

- ./Messaging/MessagingScheduler/[MessageController](https://github.com/nateforsyth/Project-SEE/blob/main/Messaging/MessagingScheduler/MessageController.cs)/GetTodaysMessages
- ./Messaging/MessagingScheduler/[MessageController](https://github.com/nateforsyth/Project-SEE/blob/main/Messaging/MessagingScheduler/MessageController.cs)/PostMessage


##### linq-to-typescript

As a .NET Developer, I love LINQ, so naturally I would go looking for something similar for TypeScript; queue [linq-to-typescript](https://www.npmjs.com/package/linq-to-typescript).

While this does greatly simplify collection manipulation in TypeScript, it is quite heavy, but when under time pressure you use what you are comfortable with - it is what it is.

##### zod

I use [Zod](https://www.npmjs.com/package/zod) specifically for React form validation, mainly because it's suggested in many MUI tutorials. In a nutshell, it's used for schema and type validation and is quite easy to use.

## MessagingScheduler

The Messaging*Scheduler* is configured as a service, specifically implementing `IHostedService` and `IAsyncDisposable`, and utilises the aforementioned `Respository` pattern by way of Dependency Injection.

The `SendMessage` method within the `MessagingService` class is invoked by the `StartAsync` method every 2 seconds - providing quick response to the User without incurring too much cost in the Google Cloud Run platform.

The `SendMessage` method retrieves and sorts messages from the `Repository`, iterates over them and Posts them to the Messaging*Server*.

Upon successful Post, the sent Message has its `New` property toggled from `true` to `false`. This is necessary to ensure that the Messaging*Scheduler* doesn't send messages to Messaging*Server* multiple times; this targets `MessagingServer > MessagingController > QueueMessage`, reference that section for detail.

**SwaggerUi is intentionally disabled on the Release build of the Scheduler project**

## MessagingServer

The Messaging*Server* is configured as a standard ASP.NET Core Web API with a single Controller, `MessagingController` which consumes a `MessageQueue` via Dependency Injection.

### MessagingController

There are 3 important Action methods within this Controller:

#### MessageQueue

This is the subscription method that a Client app can invoke. It'll simply dequeue Message objects from the `MessageQueue`, it requires an `id: string` be provided as part of the Uri. This is done for logging purposes currently, but is also intended to enable simple extensibility of the functionality late on (if necessary).

- Example request string: `/api/Messaging/MessageQueue/${userId}`
- method: `GET`
- headers: `{ "Accept": "text/event-stream" }`

#### QueueMessage

This is an Action method that is intended to only be accessible by the Messaging*Scheduler*.

Messages are Posted to this end-point and subsequently queued for broadcasting to connected Client apps from the `MessageQueue`.

**SwaggerUi is intentionally disabled on the Release build of the Server project**

## Project structure

```
📦 Server Sent Events in ASP.NET Core 8 and React
├─ Messaging
│  ├─ messagingapp (React app)
│  │  └─ src
│  │     ├─ components
│  │     │  ├─ appTitle
│  │     │  │  ├─ AppTitle.tsx
│  │     │  │  └─ IAppTitleProps.ts
│  │     │  ├─ message
│  │     │  │  ├─ Message.tsx
│  │     │  │  └─ IMessageProps.ts
│  │     │  ├─ messagingForm
│  │     │  │  ├─ MessagingForm.tsx
│  │     │  │  └─ IMessagingFormProps.ts
│  │     │  └─ userAvatarDisplay
│  │     │     ├─ UserAvatarDisplay.tsx
│  │     │     └─ IUserAvatarDisplayProps.ts
│  │     ├─ model
│  │     │  ├─ IEventSubscribeResult.ts
│  │     │  ├─ IMessage.ts
│  │     │  ├─ IMessageGetResult.ts
│  │     │  └─ IMessagePostResult.ts
│  │     └─ services
│  │        ├─ EventService.ts
│  │        └─ MessageService.ts
│  ├─ MessagingScheduler (ASP.NET Core Web API/Service)
│  │  ├─ Model
│  │  │  ├─ IMessageRepository.cs
│  │  │  ├─ Message.cs
│  │  │  ├─ MessageDTO.cs
│  │  │  ├─ MessageQueueDTO.cs
│  │  │  └─ MessageRepository.cs
│  │  ├─ Services
│  │  │  └─ MessagingService.cs
│  │  ├─ appsettings.Development.json
│  │  ├─ appsettings.json
│  │  ├─ Dockerfile
│  │  ├─ MessageController.cs
│  │  └─ Program.cs (entry-point)
│  ├─ MessagingServer (ASP.NET Core Web API)
│  │  ├─ Controllers
│  │  │  └─ MessagingController.cs
│  │  ├─ Model
│  │  │  ├─ IMessageQueue.cs
│  │  │  └─ MessageQueue.cs
│  │  ├─ appsettings.Development.json
│  │  ├─ appsettings.json
│  │  ├─ Dockerfile
│  │  └─ Program.cs (entry-point)
│  ├─ .dockerignore
│  ├─ docker-compose.Debug.yml
│  ├─ docker-compose.override.yml
│  ├─ docker-compose.Release.yml
│  ├─ docker-compose.yml
│  └─ launchSettings.json
├─ LICENSE
└─ README.md
```
©generated by [Project Tree Generator](https://woochanleee.github.io/project-tree-generator)

# Known TODOs

- Implement access control (using Auth0 and RBAC)
  - Messaging*Scheduler*
  - Messaging*Server*
- Secure end-points
  - MessagingServer.Controllers.MessagingController.MessageQueue
    - Intended to be accessible by Client apps.
  - MessagingServer.Controllers.MessagingController.QueueMessage
    - Intended to be accessible only by the Scheduler.

# Deployment

In addition to implementing a multi-container `docker-compose` solution, I decided to host this project on [Google Cloud Run](https://console.cloud.google.com/run) to expost it to the outside world.

The Projects are first published to [Dockerhub](https://hub.docker.com/), and then manual revisions are configured within Google Cloud Run (due to cost constraints). This applies to the following projects only.

- Messaging*Scheduler*
- Messaging*Server*

**Important**: The Dockerhub repo(s) must be public.

Google Cloud Run uses the following naming convention for Dockerhub repos; `docker.io/<dockerimagename>:version`

Google Cloud Run will tell you which DNS records to add, I needed to add a single new CNAME record; `CNAME > [appname] > ghs.googlehosted.com`

## Continuous Deployment

For the final project, I needed to implement Continuous Deployment from the Github repo to Dockerhub.

Target:
`Github repo > Messaging/messagingapp/Dockerfile`

# Curly issues and solutions

There were only a handful of notable issues encountered, and I'm struggling to remember them all, however the ones worth mentioning are below.

## Thread safe element manipulation in .NET Collections

This could do with a blog post all its own; I tripped up while using ConcurrentDictionary within the Messaging*Scheduler* project (and implemented my learnings in the Messaging*Server* project).

Updating of elements by utilising `TryUpdate` was still having issues where resources weren't being updated in a threadsafe manner.

Where another thread may have updated en element already, it's important to ensure that the element still exists in the Collection before updating it.

I ended up implementing update functionality instead using `AddOrUpdate` as follows:

```cs
/// <summary>
/// Toggles the New property on the specified Message instance within the Repository, ensures that only new Messages are sent to the Server
/// </summary>
/// <param name="message"></param>
/// <returns></returns>
public Message? MarkMessageAsNotNew(Message message)
{
    if (!string.IsNullOrEmpty(message.Id))
    {
        _logger.LogInformation("\t\tMarkMessageAsNotNew invoked for Message {MessageId}", message.Id);
        _ = _messages.AddOrUpdate(
            message.Id,
            message,
            (messageId, existingMessage) =>
            {
                if (message != existingMessage)
                {
                    _logger.LogInformation("\t\t\tMessage {MessageId} already exists in the Repository, proceeding to update", messageId);
                }

                existingMessage.New = false;

                return existingMessage;
            }
        );

        _logger.LogInformation("\t\t\tMessage: {MessageId} has been updated, is new? {IsNew}", message.Id, message.New);

        return message;
    }
    else
    {
        _logger.LogWarning("\t\tThe Id for the Message being updated is null or empty");
        return null;
    }
}
```

Adding new elements to the same Collection was problematic because it's not clear whether another thread may have already added an item, so implented it as follows:

```cs
/// <summary>
/// Add the specified Message object to the Repository
/// </summary>
/// <param name="message"></param>
/// <returns></returns>
public bool AddMessage(Message message)
{
    bool success = false;
    (Message? msg, int threadId) = (message, Environment.CurrentManagedThreadId);

    if (!string.IsNullOrEmpty(msg.Id))
    {
        if (_messages.TryAdd(msg.Id, msg))
        {
            _logger.LogInformation("\tMessage: {MessageId} added to Repository by thread: {ThreadId}", msg.Id, threadId);

            success = true;
        }
        else
        {
            _logger.LogInformation("\tThread: {ThreadId} couldn't add Message: {MessageId}, it's already in the Respository", threadId, msg.Id);
        }
    }
    else {
            _logger.LogInformation("\tThread: {ThreadId} couldn't add Message because MessageId is null or empty", threadId);
    }

    return success;
}
```

Important takeaway is that I utilise retrieval of the `Environment.CurrentManagedThreadId` for further validation when attempting to add an item.

# In closing

This project proved challenging, not from the code perspective, but from the Linux/WSL and Docker side of things that I hadn't worked with much before. I've thoroughly enjoyed every minute, and hope to come back to the project in future to further flesh it out.
