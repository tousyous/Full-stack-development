# First MVC Project

## Preparing the project

### 1. Set the port to listen for HTTP requests

`Properties/Settings.json`

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:5000",
      "sslPort": 0
    }
  },
  "profiles": {
    "PartyInvites": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

### 2. Create a clean starting point for the application, with a single action method that selects the default view

`Controllers/HomeController.cs`

```cs
using Microsoft.AspNetCore.Mvc;

namespace PartyInvites.Controllers
{
    public class HomeController : Controller
    {
        public IActionResult Index()
        {
            return View();
        }

    }
}
```

### 3. Create a welcome message 

`Views/Home/Index.cshtml`

```html
@{
    Layout = null;
}

<!DOCTYPE html>
<html>
    <head>
        <meta name="viewport" content="width=device-width"/>
        <title>Party!</title>
    </head>
    <body>
        <div>
            <div>
                We're going to have an exciting party! <br />
                (To do: sell it better. Add pictures or something.)
            </div>
        </div>
    </body>
</html>
```

## The data model

All data model classes are added to the `Models` directory

### 4. Add model class PartyInvites

```cs
namespace PartyInvites.Models
{
    public class GuestResponse
    {
        public string? Name { get; set; }
        public string? Email { get; set; }
        public string? Phone { get; set; }
        public bool? WillAttend { get; set; }
    }
}
```

Het `?` na het datatype duidt aan dat een variabel *nullable* is.

## Addign actions and views

### 5. Add action to HomeController

```cs
public ViewResult RsvpForm()
{
    return View();
}
```

Naast `public IActionResult Index()` is er nu een tweede methode `public ViewResult RsvpForm()` die de **View** methode aanroept.
* De Razor engine gebruikt hiervoor de naam van de actie methode om de juiste view file te bepalen
    * `public IActionResult`**`Index()`** verwijst naar `Views/Home/`**`Index.cshtml`**
    * `public ViewResult`**`RsvpForm()`** verwijst naar `Views/Home/`**`RsvpForm.cshtml`**

### 6. Linking actions methods

To create a link to a view, you create a link to the action invoking the view.

In `Index.cshtml` we add:

```html
<a asp-action="RsvpForm">RSVP Now!</a>
```
* het `asp-action` attribuut van het `a`-element bepaalt welke actie er wordt uitgevoerd
