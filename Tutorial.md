# REST API with ASP.NET Core Tutorial

A REST API (Representational State Transfer Application Programming Interface) is software that allows applications to communicate with one another over HTTP.

HTTP (Hypertext Transfer Protocol) is the standard communication protocol used for transferring data between a client and a server over the internet or a network. In simpmle terms, it defines how a request is sent and how a response is returned. 

Later on, when we build a web/mobile application, instead of directly communicating with our database, our applications can send HTTP requests to an API which validates the requests, peforms business logic, communicates with the database, and returns a response! This design seperates the frontend from the backend and allows multiple applications to use the same backend without duplicating code.s

### What is ASP.NET Core Web API?

ASP.NET Core is Microsoft's modern framework for building web applications and backend services. One project type inside ASP.NET Core is called 'Web API'. A Web API project exposes endpoints that clients can call using HTTP. 

### The Goal of this Tutorial...

In the previous tutorial we built a SQL Server database that stores university admissions information. Our goal is to create an API that allows other applications to access this information safely. By the end of this tutorial, we will have an API capable of doing several necessary CRUD (Create, Read, Update, and Delete) operations to interact with our database.

<br>

# Step 1: Create an ASP.NET Core Web API Project

Run the following command to create a ASP.NET Core Web API project template: `dotnet new webapi`

Now we have a minimal fully functional REST API with routing, dependency injection, configuraiton files, and an embedded web server already configured.

<br>

# Step 2: Connect to the SQL Server

### (2.1) Install Entity Framework Core Packages

In order to connect to the SQL Server and executute database operations, we will use **Entity Framework Core** (EF Core), Microsoft's **Object Relational Mapper** (ORM) for .NET. An ORM is a library that lets you work with database data using C# objects instead of writing SQL for every operation. In other words, it translates your C# code into SQL queries behind the scenes. For example, instead of writing `SELECT * FROM Students`, you might write `var students = context.Students.ToList();`. EF Core automatically generates the SQL, executes it against SQL Server, and converts the results into C# objects. Although EF Core is primarily an ORM, it also manages the application's interaction with the database. It uses your connection string to connect to SQL Server, executes the generated SQL (or raw SQL when needed), and maps the results back into C# objects.

So first, before we use EF Core, we will install a two pacakges:

```
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

* The first, `EntityFrameworkCore.SqlServer`, is the SQL Server database provider for Entity Framework Core. EF Core itself does not know how to communicate with every database; it relies on database providers, each of which understands the SQL dialect and connection proticol for a specific database. Without this package, EF Core has no way to communicate with SQL Server.
* The second, `EntityFrameworkCore.Tools`, providers developer tools for working with EF Core. This tools/commands help update the database schema, generate C# model classes from an existing database, etc. These tools will be useful later.

### (2.2) Create a Connection String

Next, we will create a **Connecting String**. This is simply the information the application needs to find and connect to a database. 

Open `appsettings.json` and add a connection string pointing to your SQL Server Database:

```
"ConnectionStrings": {
    "DefaultConnection": "Server=localhost,1433;Database=Admissions;User Id=sa;Password=YourStrongPassword123!;TrustServerCertificate=True;"
},
```

### (2.3) Create Database Context

Entity Framework Core needs a central object that represents our database and manages all communication with it. This object is called a `DbContext`. You can think of the `DbContext` as the gateway between our ASP.NET Core application and SQL Server. Whenever we want to query, insert, update, or delete data, we do so through the `DbContext`. It is responsible for managing our connection, executing SQL queries, etc. 

In addition, the `DbContext` contains a DbSet property for each table in the database. Each DbSet represents one table and allows us to query and modify the rows in that table.

Create a folder named `data` and inside it, create a file named `ApplicationDbContext.cs` with the follwing:

```csharp
using Microsoft.EntityFrameworkCore;
namespace tutorial_aspnet_core_web_api.Data
{
    public class ApplicationDbContext : DbContext
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
        // Database tables will be added here
        // Example:
        // public DbSet<Student> Students { get; set; }
    }
}
```

Right now, our DbContext is empty because we haven't added any database tables yet. Later, we'll add a DbSet for each table in our SQL Server database.

### (2.5) Register Database Context

Creating the `ApplicationDbContext` class isn't enough—we also need to tell ASP.NET Core how to create it whenever our application needs to access the database. ASP.NET Core uses a feature called Dependency Injection (DI) to automatically create and provide objects throughout the application. By registering our ApplicationDbContext, we're telling ASP.NET Core: "Whenever a controller or service requests an ApplicationDbContext, create one using this connection string and configure it to use SQL Server."

Open `Program.cs` and add the following:

```csharp
using Microsoft.EntityFrameworkCore;
using tutorial_aspnet_core_web_api.Data;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<ApplicationDbContext>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

This code (1) Registers ApplicationDbContext with ASP.NET Core's dependency injection container, (2) onfigures Entity Framework Core to use SQL Server, and (3) Reads the connection string named AdmissionsDatabase from appsettings.json so EF Core knows which database to connect to.

### Note on Namespaces

In the above two pieces of code, you might notice that we are using:

```csharp
namespace tutorial_aspnet_core_web_api.Data
```

`tutorial_aspnet_core_web_api` is the root namespace of our project, which is defined in the .csproj file. The `.Data` portial in a sub-namespace that groups together database-related classes. With our current setup, the `ApplicationDbContext` class belongs to the `tutorial_aspnet_core_web_api.Data` namespace. 

Namespaces help organize our C# code and allow classes in different files to reference each other. For example, in `Program.cs` when we do `using tutorial_aspnet_core_web_api.Data;`, we can refrence `ApplicationDbContext` by its class name instead of writing its full name `tutorial_aspnet_core_web_api.Data.ApplicationDbContext`. 

### (2.6) Test the Connection

Next lets test our conneciton. In `Program.cs` add a simple test endpoint:

```csharp
app.MapGet("/test-db", async (ApplicationDbContext db) =>
{
    var canConnect = await db.Database.CanConnectAsync();
    return canConnect ? "Database connection successful" : "Could not connect to database";
});
```

Then run:

```
dotnet run
```

And visit

```
http://localhost:5288/test-db
```

#### Problem 1

In my case, we got "Could not connect to database", and in the terminal, I saw "Failed to determine the https port for redirect"

First, open docker and make sure the sqlserver container is running. If it isn't, start it. You can verify that the container is running by executing the command:

```
(base) ryanzafft@Ryans-MBP-3 tutorial-aspnet-core-web-api % docker ps
CONTAINER ID   IMAGE                                        COMMAND                  CREATED       STATUS         PORTS                                         NAMES
184f2007a37d   mcr.microsoft.com/mssql/server:2022-latest   "/opt/mssql/bin/laun…"   2 weeks ago   Up 2 minutes   0.0.0.0:1433->1433/tcp, [::]:1433->1433/tcp   sqlserver
```

Also make sure the database name and credentials in the connection string are correct.

<br>


