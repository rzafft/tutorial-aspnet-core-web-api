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

<br>

# Step 2: Connect to the SQL Server

<br>

# Create the Database Context
