# About C# and the .NET Web API

This file will...

# 1. About C#

* Created by Microsoft
* Modern Object Oriented programming language
* Compiled
* Statically typed (data type of a variable is checked and known before the program runs, usually during compilation)
* Runs on the .NET runtime
* Cross platform


<br><br>

# 2. Project Structure

After creating a ASP.NEt Core Web API project template using `dotnet new webapi`, your directory will contain the falling files and subdirs. Lets get to know them.

1. `Program.cs`: Application startup and configuration

    This is the main entry point and startup configuration file for an ASP.NET Core application. It is the first piece of application code that runs when the API starts and is responsible for creating the web application, configuring the services the application uses, defining how HTTP requests are processed, and starting the web server. In older versions of ASP.NET Core, this functionality was separated into files such as Program.cs and Startup.cs, but modern ASP.NET Core versions combine this setup into a single Program.cs file. 
    
    When the application starts, Program.cs creates a WebApplicationBuilder, which sets up the application environment, loads configuration files such as appsettings.json, and prepares the built-in dependency injection system. Dependency injection allows the application to automatically provide classes and services when they are needed instead of requiring developers to manually create those objects. Services such as controllers, database connections, authentication systems, and custom application services are registered here. 
    
    After services are configured, Program.cs builds the application and defines the HTTP request pipeline. The request pipeline is the sequence of steps that every incoming HTTP request passes through before reaching the API endpoint. These steps are called middleware and can perform tasks such as redirecting HTTP requests to HTTPS, handling authentication, logging requests, processing errors, and routing requests to the correct controller or endpoint. 
    
    Finally, Program.cs starts the application by calling app.Run(), which launches the built-in ASP.NET Core web server (Kestrel) and begins listening for HTTP requests. Every request sent to the API flows through the configuration defined in this file, making Program.cs the central location where the application's startup behavior, available services, and request processing rules are defined. It is a required source file and should not be deleted because without it ASP.NET Core would not know how to initialize and run the application.

2. `tutorial-aspnet-core-web-api.csproj`: Project definition

    This is the C# project file for the ASP.NET Core Web API application. The .csproj file defines the structure, settings, and dependencies of the project and is used by the .NET SDK to understand how the application should be built and run. It tells .NET important information such as which version of the .NET framework the application targets, what external libraries (NuGet packages) the project requires, and additional build settings or project configuration options. 
    
    When you run commands such as 'dotnet build', 'dotnet run', or 'dotnet restore', the .NET SDK reads this file to determine what actions need to be performed. For example, if the project requires an external package like Microsoft.AspNetCore.OpenApi, the .csproj file contains a PackageReference that tells .NET to download and include that library when building the application. 
    
    The .csproj file replaces the older project configuration files used in previous versions of .NET and provides a single location for managing the application's dependencies and build configuration. This file is a required part of the application and should not be deleted. Unlike generated directories such as obj/ or bin/, the .csproj file is part of the source code and must be included when sharing, version controlling, or deploying the project. Without it, the .NET SDK would not know how to restore dependencies, compile the application, or run the project.

3. `appsettings.json`: Application configuration

    This is the main configuration file for an ASP.NET Core application. It stores application settings that the application needs while running, such as logging configuration, database connection strings, API settings, authentication settings, and other values that should be available to the application. 
    
    Instead of hardcoding values directly into C# code, developers place configurable values in this file and allow the application to read them at runtime through ASP.NET Core's built-in configuration system. When the application starts, ASP.NET Core automatically loads this file and makes the values available through dependency injection using the IConfiguration service. 
    
    This file applies to all environments, including development, testing, and production, unless a more specific environment configuration file overrides a value. Because this file can contain sensitive information such as database credentials or API keys, production applications commonly use environment variables, secret managers, or other secure configuration systems instead of storing sensitive values directly in this file.

4. `appsettings.Development.json`: Development-specific configuration

    This is an environment-specific configuration file that contains settings that should only apply when the application is running in the Development environment. ASP.NET Core determines the current environment using the ASPNETCORE_ENVIRONMENT environment variable, which is typically set to Development in launchSettings.json for local development. When the application starts in the Development environment, ASP.NET Core loads appsettings.json first and then loads appsettings.Development.json. If both files contain the same setting, the value in appsettings.Development.json takes priority and overrides the value from appsettings.json. This allows developers to use different settings locally without changing the main configuration file. 
    
    For example, the production configuration might use a real database connection and minimal logging, while the development configuration might use a local test database and more detailed debug logging. Like appsettings.json, this file is configuration only and does not contain application logic. It is intended to make it easier to maintain different settings for different environments without modifying the application's source code.

5. `properties/`: Local startup configuration

    This directory contains project specific configuration files that control how the application behaves when it is launched during development. The files in this directory do not define the application's functionality or get deployed with the application; instead, they provide instructions for how the application should start in a development environment. 
    
    For example, inside this directory you will see a 'launchSettings.json' file. This file defines different launch profiles for running the application locally. A launch profile is a predefined set of settings that tells .NET how to start the application, including which URL and ports to use, whether to enable HTTPS, which environment the application should run under, and any environment variables that should be set. For example, it commonly sets the ASPNETCORE_ENVIRONMENT variable to Development, which tells ASP.NET Core to load development-specific configuration files such as appsettings.Development.json. 
    
    When you run commands like 'dotnet run' or start the application through Visual Studio, .NET reads this file to determine how the application should be launched. This file only affects local development and debugging, so changes made here typically do not impact the deployed application running in production.


6. `tutorial-aspnet-core-web-api.http`: API testing requests

    This is an HTTP request file used for testing the API during development. It contains pre-written HTTP requests that can be sent directly to the running ASP.NET Core application without needing a separate API testing tool such as Postman or Insomnia. The .http file is supported by development environments like Visual Studio and Visual Studio Code through built-in or extension-based HTTP clients. The purpose of this file is to provide a quick way to manually test API endpoints while developing. Instead of opening a browser, writing a command-line curl request, or configuring a separate testing application, a developer can write an HTTP request in this file and execute it directly against the API. 
    
    For example, the file may contain: 'GET https://localhost:7007/weatherforecast'. When executed, this sends an HTTP GET request to the /weatherforecast endpoint in the API. The application receives the request, processes it through the routing and controller/endpoint logic, and returns an HTTP response. A .http file is not required for the application to run. It is simply a developer convenience for testing and debugging API endpoints. It can be deleted without affecting the application, because the actual API functionality is defined in files such as Program.cs, controllers, models, and services. It is similar in purpose to a collection of saved API requests in tools like Postman, but stored directly alongside the project code so developers can easily test the application during development. 
    
    To use this file in Visual Studio Code, install the REST CLIENT extension, start the API and open the .http file. Above the request, you will see 'Send Request'. Click it. The extension sends the HTTP request to your running API and displays the response.

7. `obj/`: Generated build files

    This is the object files directory. It is automatically created by the .NET SDK to store temporary intermediate files used while converting your source code into a working application. Before .NET can run an application, it must resolve external libraries and dependencies, determine the correct versions of those libraries, generate build instructions, compile the source code, and package the application for execution. The obj/ directory stores the intermediate information required during these processes, such as dependency information, NuGet package details, and generated build configuration files. 
    
    This directory is not part of the application source code and does not need to be manually maintained. It can safely be deleted, and the .NET SDK will automatically recreate it when needed. Commands such as 'dotnet restore' recreate the dependency-related files, 'dotnet build' performs a restore if required before compiling the application, and 'dotnet run' builds the project before starting it, recreating the directory if it is missing. Additionally, commands such as 'dotnet clean' remove generated build output, allowing the project to be rebuilt from a clean state the next time it is compiled.

<br><br>

# 3. Understanding `Program.cs`

Now that we know what the `Program.cs` file is, let's examine the code that ASP.NET Core generates when you create a new Web API project. This file is responsible for configuring the application, defining how it handles HTTP requests, and ultimately starting the web server.

### Create the application builder

```csharp
var builder = WebApplication.CreateBuilder(args);
```

This is the first line of application code that executes when the API starts. The `var` keyword tells the C# compiler to infer the variable's type from the expression on the right-hand side. In this case, `builder` is actually of type `WebApplicationBuilder`. Thus, this line of code is equivalent to `WebApplicationBuilder builder = ....`.


`WebApplication.CreateBuilder(args)` is a static factory method that creates and returns a `WebApplicationBuilder` object. This object is responsible for collecting all of the configuration needed to construct the application before it begins processing HTTP requests.

As discussed in the previous section, this method automatically loads configuration from files such as `appsettings.json` and `appsettings.Development.json`, initializes ASP.NET Core's dependency injection system, configures logging, and determines the current hosting environment.

`WebApplicationBuilder` exposes several important properties that are used throughout `Program.cs`:

* `builder.Services` – An `IServiceCollection` used to register application services.
* `builder.Configuration` – An `IConfigurationManager` used to access configuration values from sources such as `appsettings.json`, environment variables, and command-line arguments.
* `builder.Environment` – An `IWebHostEnvironment` that describes the current hosting environment (Development, Production, etc.).
* `builder.Logging` – An `ILoggingBuilder` used to configure logging.
* `builder.Host` – An `IHostBuilder` used to configure the underlying generic host.

---

### Register services

```csharp
builder.Services.AddOpenApi();
```

The `builder.Services` property returns an object implementing the `IServiceCollection` interface. `IServiceCollection` represents a collection of service registrations. A service registration tells ASP.NET Core what service is available and how it should create that service when it is needed. The service objects themselves are not created at this point. The `AddOpenApi()` method registers all of the services required for ASP.NET Core to generate an OpenAPI document. Internally, it adds multiple service registrations to the `IServiceCollection`.

Note, `AddOpenApi()` is an extension method. Extension methods are static methods that C# allows to be called as though they were instance methods on an object.

As an application grows, many additional services are typically registered:

```csharp
builder.Services.AddControllers();
builder.Services.AddAuthentication();
builder.Services.AddAuthorization();
builder.Services.AddDbContext<ApplicationDbContext>();
```

Later, when `builder.Build()` is called, ASP.NET Core uses all of these registrations to construct its built-in dependency injection container.

---

### Build the application

```csharp
var app = builder.Build();
```

After all configuration has been completed, the application is built. Like the earlier `builder` variable, the `var` keyword allows the compiler to infer that `app` is of type `WebApplication`.

The `Build()` method uses all of the information stored in the `WebApplicationBuilder` to create and return a `WebApplication` object. While `WebApplicationBuilder` is responsible for configuring the application, `WebApplication` represents the application itself. It is used to configure middleware, define API endpoints, and eventually start the web server.

Although the application has now been built, it is not yet running. The remaining code continues configuring the `WebApplication` before it is started with `app.Run()`.

---

### Configure OpenAPI for development

```csharp
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}
```

The `Environment` property returns an `IWebHostEnvironment` object describing the current hosting environment. The `IsDevelopment()` method returns `true` if the application is running in the Development environment.

As discussed earlier, the Development environment is typically selected during local development by the `ASPNETCORE_ENVIRONMENT` setting in `launchSettings.json`. If the application is running in Development, `MapOpenApi()` registers an HTTP endpoint that serves the generated OpenAPI document.

Earlier we called `builder.Services.AddOpenApi()`. These two methods work together:

* `AddOpenApi()` registers the services needed to generate an OpenAPI document.
* `MapOpenApi()` exposes an HTTP endpoint that clients can request to retrieve that document.

---

### Configure the HTTP request pipeline

```csharp
app.UseHttpsRedirection();
```

The `UseHttpsRedirection()` method adds **HTTPS Redirection Middleware** to the application's HTTP request pipeline.

Whenever an HTTP request reaches the application, this middleware checks whether the request was made using HTTP instead of HTTPS. If so, it automatically returns a redirect response instructing the client to resend the request using HTTPS.

During local development, the HTTP and HTTPS endpoints come from the `applicationUrl` setting in `launchSettings.json`, which we discussed earlier. The middleware uses those configured URLs to determine where HTTP requests should be redirected.

---

### Define an endpoint

```csharp
app.MapGet("/weatherforecast", () =>
{
    ...
});
```

This line defines an HTTP GET endpoint. When a client sends a GET request to `/weatherforecast`, ASP.NET Core executes the supplied function and returns its result as the HTTP response.

The `() => { ... }` syntax is called a lambda expression. A lambda expression defines an anonymous function that can be passed as an argument to another method. Here, it specifies the code that should execute whenever the `/weatherforecast` endpoint receives a GET request.

This style of defining endpoints directly inside `Program.cs` is known as a Minimal API.

---

### Start the application

```csharp
app.Run();
```

Finally, the application is started.

Calling `Run()` launches ASP.NET Core's built-in web server, **Kestrel**, and begins listening for incoming HTTP requests.

During local development, Kestrel listens on the URLs defined in `launchSettings.json`. In production, the hosting environment determines which URLs the application listens on.

At this point, the application is fully configured. Every incoming request flows through the configured middleware before being routed to the appropriate API endpoint.

<br><br>

# 4. Using the .NET CLI

Now that we understand how the application is configured in Program.cs, let's learn how to build and run it using the .NET Command-Line Interface (CLI). Before running any commands, make sure you are inside your project's root directory (the directory containing the .csproj file).

```
dotnet run
```

* This is the command you will use most frequently while developing. When executed, the .NET CLI performs several steps:

    1. Read the projects `.csproj` file
    2. Restores any missing NuGet packages (if necessary)
    3. Compiles the application
    4. Starts the application


```
dotnet build
```

* This command compiles the project without starting it. This command is useful for verifying that the project compiles successfully without running the application. During the build process, the .NET SDK:

    1. Reads the `.csproj` file
    2. Restores packages if neccessary
    3. Compiles the C# source code
    4. Creates or updates the `obj/` and `bin/` directories

```
dotnet restore
```

* This command downloads any NuGet packages required by the project. Normally, you do not need to run it manually because dotnet build and dotnet run automatically restore missing packages when necessary. You will typically use it after cloning a project or after adding new package dependencies.

```
dotnet clean
```

* This command removes the compiled build output generated by the .NET SDK. It deletes files from the project's build directories, allowing the application to be rebuilt from a clean state. This can be useful when troubleshooting build problems or forcing a completely fresh compilation.

```
dotnet watch
```

* Whenever you save a C# source file, configuration file, or other supported project file, the application is automatically rebuilt and restarted. This allows you to make changes without repeatedly stopping and restarting the application manually.
