# ASP.NET with Keycloak

## Disclaimer

All this information and code is a based on several blog posts and Stack Overflow answers. My thanks got to the folks out there who cared to upload tutorials and answer questions.

## Intro

This repository tries to connect Keycloak with an ASP.NET Web App following a tutorial on Medium ([How to Implement Keycloak Authentication in a .NET Core Application](https://medium.com/@ahmed.gaduo_93938/how-to-implement-keycloak-authentication-in-a-net-core-application-ce8603698f24)) by [Ahmed Gado](https://medium.com/@ahmed.gaduo_93938).

## Log

### Prerequisits

This repo is tested with following Versions:

|                 | Version |
|-----------------|---------|
| Keycloak        | 23.0.6  |
| ASP.NET Web App | 8.0.3   |


You should be able to startup a [Docker Compose](https://docs.docker.com/compose/) file and create an [ASP.NET Core MVC](https://learn.microsoft.com/en-us/aspnet/core/tutorials/first-mvc-app/start-mvc?view=aspnetcore-8.0&tabs=visual-studio) application and have a basic understanding of adding controllers and views.

If you know all of this, just follow these simple steps to setup the base:

- Create a Keycloak server instance via this [Docker Compose File](./Docker-Compose/docker-compose.yaml)
- Setup a Simple [ASP.NET MVC App](./WebApp/) using Rider or Visual Studio.
- Create a new Realm in Keycloak called `"test-realm"`.
- A user in `"test-realm"` called `"user"`, which has `Email verified` set to `"Yes"` and a password `"pass"`, with no action after first login.

> **Information**: There's a [Keycloak JSON Export](./Keycloak-Settings/realm-export.json) of the Keycloak realm i used, you can use this to setup your realm if you like.

### Configure Keycloak Client Settings

- Create a new Client with `Client ID` `"test-client"`. For `Access Type` `"confidential"` should be used, as the tutorial is from 2023 and may use a different version, I could not find this setting. It seems that `Client authentication` needs to be switched `"On"`, so a `Client Secret` could be generated.
- Setup the `Root URL` as well as the `Home URL` to `"https://localhost:7048"`, `Valid Redirect URIs` set to `"/*"` and `Web origins`to `"*"`.
- ? `Admin URL` was 
- Fetch the Client Secret from the `Credentials` tab, in my case it's `Jfb0WZe0O8aGLHXU5txGKnzWXlYEwDPC`

### Add the Package

- Add `Microsoft.AspNetCore.Authentication.OpenIdConnect` to your WebApp.

### Configure Authentication Middleware

- As I'm not using a `Startup.cs` but a `Program.cs` we have to chane the Service and Middleware configuration there. See [Program.cs](./WebApp/WebApp/WebApp/Program.cs), sections `Config Auth` and `Enable Auth`.

- I created two Endpoins in the [HomeController](./WebApp/WebApp/WebApp/Controllers/HomeController.cs), `Authorized` and `AdminPanel` as well as the matching views.

## Troubleshooting

### Keycloak HTTPs

**Problem**: When you receive an `InvalidOperationException: The MetadataAddress or Authority must use HTTPS unless disabled for development by setting RequireHttpsMetadata=false.` there are two ways to remove this exception.

**Solution**: Add `options.RequireHttpsMetadata = false;` in `AddOpenIdConnect`.

**Remarks**: I'm not sure if it's a good idea to run Keycloak via http, the WebApp via https and how this impacts the communication between the WebApp and Keycloak. Anyway, for now it works.

### Page not found

**Problem**: After logging in I got an 404 saying `https://localhost:7048/Account/AccessDenied?ReturnUrl=%2FHome%2FAdminPanel` was not found.

**Solution**: None yet.