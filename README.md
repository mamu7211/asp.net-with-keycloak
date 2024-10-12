# ASP.NET with Keycloak

## Intro

This repository tries to connect Keycloak with an ASP.NET Web App following a tutorial on Medium ([How to Implement Keycloak Authentication in a .NET Core Application](https://medium.com/@ahmed.gaduo_93938/how-to-implement-keycloak-authentication-in-a-net-core-application-ce8603698f24)).

## Log

### Prerequisits

- Create a Keycloak instance via this [Docker Compose File](./Docker-Compose/docker-compose.yaml)
- Setup a Simple [ASP.NET MVC App](./WebApp/) using Rider
- Create a new Realm in Keycloak called `"test-realm"`.
- A user in `"test-realm"` called `"user"`, which has `Email verified` set to `"Yes"` and a password `"pass"`, with no action after first login.

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

### Page not found

**Problem**: After logging in I got an 404 saying `https://localhost:7048/Account/AccessDenied?ReturnUrl=%2FHome%2FAdminPanel` was not found.

**Solution**: 