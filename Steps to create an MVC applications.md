# Steps to create an MVC application

1. In Visual Studio create a new **ASP.NET MVC Core 8** application

a. Kies voor ASP.NET Core Web App (MVC)

b. Kies .NET 8.0 (LTS) & Configure for HTTPS 

2. First create a **Controller class**

    a. The Controller automatically has an IActionResults Index method that returns a view to the 
3. Maak de map **ViewModels** aan
4. Maak een **nieuw ViewModel** aan in de ViewModels map
5. Voeg *een verwijzing naar het ViewModel* toe aan je Controller class: `using Projectname.ViewModels;`
