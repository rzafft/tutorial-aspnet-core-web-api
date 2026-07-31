# REST API with ASP.NET Core Tutorial

A REST API (Representational State Transfer Application Programming Interface) is software that allows applications to communicate with one another over HTTP.

HTTP (Hypertext Transfer Protocol) is the standard communication protocol used for transferring data between a client and a server over the internet or a network. In simpmle terms, it defines how a request is sent and how a response is returned. 

Later on, when we build a web/mobile application, instead of directly communicating with our database, our applications can send HTTP requests to an API which validates the requests, peforms business logic, communicates with the database, and returns a response! This design seperates the frontend from the backend and allows multiple applications to use the same backend without duplicating code.

### What is ASP.NET Core Web API?

ASP.NET Core is Microsoft's modern framework for building web applications and backend services. One project type inside ASP.NET Core is called 'Web API'. A Web API project exposes endpoints that clients can call using HTTP. 

### The Goal of this Tutorial...

In the previous tutorial we built a SQL Server database that stores university admissions information. Our goal is to create an API that allows other applications to access this information safely. By the end of this tutorial, we will have an API capable of doing several necessary CRUD (Create, Read, Update, and Delete) operations to interact with our database.

<br>

# Step 1: Create an ASP.NET Core Web API Project

Run the following command to create a ASP.NET Core Web API project template: `dotnet new webapi`

Now we have a minimal fully functional REST API with routing, dependency injection, configuraiton files, and an embedded web server already configured.

### Getting to know the project template

After creating your template, you should see the following files. Lets go over each one...

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




<br>

# Step 2: Connect to the SQL Server

<br>

# Create the Database Context
