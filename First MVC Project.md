# First MVC Project

Het volledige (finale) project met alle code is in de map [PartyInvites](./PartyInvites/) terug te vinden. 

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

## Adding actions and views

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

## Building a form

To build a form in `RsvpForm.cshtml` include this at the top of the page:

```html
@model PartyInvites.Models.GuestResponse
```
* Dit geeft aan dat de view een **GuestResponse** object als view model verwacht

De form zelf ziet er als volgt uit:

```html
<form asp-action="RsvpForm" method="post">
	<div>
		<label asp-for="Name">Name:</label>
		<input asp-for="Name" />
	</div>
	<div>
		<label asp-for="Email">Email:</label>
		<input asp-for="Email" />
	</div>
	<div>
		<label asp-for="Phone">Phone:</label>
		<input asp-for="Phone" />
	</div>
	<div>
		<label asp-for="WillAttend">Will you attend?</label>
		<select asp-for="WillAttend">
		<option value="">Choose an option</option>
		<option value="true">Yes</option>
		<option value="false">Yes</option>
		</select>
	</div>
	<button type="submit">Submit RSVP</button>
</form>
```

* voor elk attribuut van het **GuestResponse** object wordt een form-element met `asp-for` tag helper attribuut aangemaakt
    * deze tag helper voorziet het **for** attribuut van het label en de **id** en **name** attributen van de input-elementen
* de form wordt met de `asp-action` en `post` methode naar dezelfde actie (en dus view) gestuurd


## Receiving data from a form

In de form wordt `asp-action="RsvpForm" method="post"` gebruikt om de RsvpForm methode in de Home controller aan te roepen.

We voegen twee methoden toe:
* een HttpGet methode die het blanko formulier weergeeft
* een HttpPost methode die de form-data ontvangt en verwerkt

Bovenaan de `HomeController.cs` file wordt volgende namespace toegevoegd:

```cs
using PartyInvites.Models;
```

De bestaande RsvpForm method wordt vooraf gegaan door `[HttpGet]`:

```cs
[HttpGet]
public ViewResult RsvpForm()
{
    return View();
}
```

Een tweede methode krijgt de `[HttpPost]` prefix:

```cs
[HttpPost]
public ViewResult RsvpForm(GuestResponse guestResponse)
{
    // TODO: store guest response
    return View();
}
```

## Storing data "in-memory"

Hiervoor geen we een nieuwe class aanmaken in de `Models` folder, genaamd `Repository.cs`:

```cs
namespace PartyInvites.Models
{
    public static class Repository
    {
        private static List<GuestResponse> responses = new();
        public static IEnumerable<GuestResponse> Responses => responses;
        public static void AddResponse(GuestResponse response)
        {
            Console.writeline(response);
            responses.Add(response);
        }
    }
}
```

* de class en variabelen zijn allen **static**
* hierin kunnen alle responses tijdelijk worden opgeslagen

We passen vervolgens de HttpPost methode in de HomeController aan om de respons op te slaan:

```cs
[HttpPost]
public ViewResult RsvpForm(GuestResponse guestResponse)
{
    Repository.AddResponse(guestResponse);
    return View("Thanks", guestResponse);
}
```

* hier wordt niet alleen data opgeslaan door de `Repository.AddResponse(guestResponse)` methode aan te roepen
* daarnaast wordt een view met naam "Thanks" geselecteerd via `return View("Thanks", guestResponse);` 

## Bevestigingsboodschap na opslaan data

De HttpPost methode zal een view "Thanks" aanroepen. Om deze aan te maken: `Views/Home > RMK > Add > Razor View`

The `Thanks.cshtml` view:

```html
@model PartyInvites.Models.GuestResponse
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>Thanks</title>
</head>
<body>
    <div>
		<h1>Thanks, @Model?.Name!</h1>
		@if (Model?.WillAttend == true)
		{
			@:It's great that your coming.
			@:The drinks are already in the fridge!
		}
		else
		{
			@:Sorry to hear that you can't make it,
			@:but thanks for letting us know.
		}
    </div>
	Click
	<a asp-action="ListResponses">here</a> to see who's coming.
</body>
</html>
```

* de link op het einde verwijst naar een actie `ListResponses` om de antwoorden weer te geven
* de acties zijn in de HomeController terug te vinden

## Responsen weergeven met asp-action

De link `<a asp-action="ListResponses">here</a>` vereist een nieuwe **actie** om de responsen weer te geven. Deze actie wordt aan de HomeController toegevoegd:

```cs
public ViewResult ListResponses()
{
    return View(Repository.Responses.Where(r => r.WillAttend == true));
}
```

* Hier wordt een **View** met de naam van de actie-methode aangeroepen

Maak hiervoor een nieuwe View `ListResponses.cshtml` in de Views/Home map:

```html
@model IEnumerable<PartyInvites.Models.GuestResponse>
@{
    Layout = null;
}

<!DOCTYPE html>

<html>
<head>
    <meta name="viewport" content="width=device-width" />
    <title>ListResponses</title>
</head>
<body>
	<h2>Here is the list of people attending the party</h2>
    <table>
        <thead>
			<tr>
				<th>Name</th>
				<th>Email</th>
				<th>Phone</th>
			</tr>
        </thead>
		<tbody>
				@foreach (PartyInvites.Models.GuestResponse r in Model!)
				{
					<tr>
						<td>@r.Name</td>
						<td>@r.Email</td>
						<td>@r.Phone</td>
					</tr>
				}
		</tbody>
	</table>
</body>
</html>
```

## Validatie toevoegen

Validatie kan onder de vorm van **attributen van model classes** worden toegevoegd. Dit vereist de `System.ComponentModel.DataAnnotations` namespace.

Het `Models/GuestResponse.cs` bestand wordt als volgt aangepast:

```cs
using System.ComponentModel.DataAnnotations;

namespace PartyInvites.Models
{
    public class GuestResponse
    {
        [Required(ErrorMessage = "Please enter your name")]
        public string? Name { get; set; }
        [Required(ErrorMessage = "Please enter your email address")]
        [EmailAddress]
        public string? Email { get; set; }
        [Required(ErrorMessage = "Please enter your phone number")]
        public string? Phone { get; set; }
        [Required(ErrorMessage = "Please specify whether you'll attend")]
        public bool? WillAttend { get; set; }
    }
}
```

In de HttpPost actiemethode kunnen we met `ModelState.IsValid` nagaan of de validatie gelukt is:

```cs
[HttpPost]
public ViewResult RsvpForm(GuestResponse guestResponse)
{
    if(ModelState.IsValid)
    {
        Repository.AddResponse(guestResponse);
        return View("Thanks", guestResponse);
    } else
    {
        return View();
    }
}
```

De error messages kan je met de `asp-validation-summary` tag helper in de `RsvpForm.cshtml` visualiseren:
```html
<div asp-validation-summary="All"></div>
```

Input-elementen met validatiefouten krijgen automatisch de `input-validation-error` class als attribuut
* dit laat toe om de **opmaak** van deze elementen aan te passen
* de classen kan je aanpassen in de `wwwroot/css` folder
* voeg een nieuwe Styelsheet toe via RMK op de css-map > Add New Item > C#, ASP.NET Core, Web, Content > Style Sheet

Maak volgende `styles.css` file aan:

```css
.field-validation-error {
    color: #f00;
}
.field-validation-valid {
    display: none;
}
.input-validation-error {
    border: 1px solid #f00;
    background-color: #fee;
}
.validation-summary-errors {
    font-weight: bold;
    color: #f00;
}
.validation-summary-valid {
    display: none;
}
```

Om deze stylesheet toe te voegen aan de RsvpForm.cshtml, pas je de head-tag aan:
```html
<head>
	<meta name="viewport" content="width=device-width" />
	<title>RsvpForm</title>
	<link href="/css/styles.css" rel="stylesheet" />
</head>
```

## De Layout van de view aanpassen met bootstrap

Bootstrap is terug te vinden in de `lib/bootstrap/dist/css/bootstrap.css` file

We voegen de link naar deze stylesheet toe aan de `Index.cshtml` head-tag:

```html
<head>
    <meta name="viewport" content="width=device-width"/>
    <title>Party!</title>
    <link href="/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet" />
</head>
```

Aan de body voegen we vervolges bootstrap classes toe:

```html
<body>
    <div class="text-center m-2">
        <div>
            <h3>We're going to have an exciting party!</h3>
      <h4>Don't miss out!</h4>
            <a class="btn btn-primary" asp-action="RsvpForm">RSVP Now!</a>
        </div>
    </div>
</body>
```

We voeren dezelfde stappen uit voor de `RsvpForm.cshtml` file, de `Thanks.cshtml` file en de `ListResponses.cshtml` file.


