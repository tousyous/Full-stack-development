# Essential C# features

This chapter uses the LanguageFeatures project.

The `Program.cs` file is the only file in a project containing **top-level statements** = code statements outside of a namespace or file that are used to remove unnecessary code structure from class files

At the top of a file `using`-statements will declare dependencies on the namespace that it requires
* if the same dependecies are used in multiple files, it is possible to declare a **`global using`** statement
* first create a file named `GlobalUsings.cs`
* next, include the *global using* statements in the file:

```cs
global using Microsoft.AspNetCore.Mvc;
global using LanguageFeatures.Models;
```

**Implicit using** statements are *global using* statements that are always available, without the need to declare them.

### Outline of this chapter

1. [Null state analysis](#null-state-analysis) !
2. [String interpolation](#string-interpolation) !
3. [Using object and collection initializers](#using-object-and-collection-initializers) !
4. [Target-typed new expressions](#target-typed-new-expressions)
5. [Pattern-matching](#pattern-matching)
6. [Extension methods](#extension-methods)
7. [Lambda expressions](#lambda-expressions) !

## Null state analysis

To prevent *null reference exceptions* during runtime, the compiler will try to identify these attempts in what we call a **null state analysis**.
* there are two types of C# variables: *nullable* and *non-nullable*
* a `?` is appended to the data type to specify a **nullable** type

```cs
public decimal? Price { get; set; }
```

* this means that when you try to access a feature of Price, you first need to check is the value is null or not
* when the variable is non-nullable, you can safely access the features it provides


### There are different ways on how to address (non-)nullable variables:

* include the keyword **required** for a non-nullable variable
    * the compiler will throw an error if no value is provided

```cs
public required string Name { get; set; }
```

* an alternative is to provide **a default value** for non-nullable variables

```cs
public string Name { get; set; } = string.Empty;
```

* if a variable can be initialised with a nullable value, the variable needs to be **a nullable type**, therefore the `?` is used
    * for array and collections the type `Product?[]` depicts an array that can contain a *null* value, **not** an array that can be *null*
    * in the opposite direction, `Product[]?` can be *null*, but **cannot** contain a *null* value

```cs
return new Product?[] { kayak, lifejacket, null };
```

* a problem with nullable variables is when you try to access fields or methods of null values
    * therefore, you can use the **null conditional operator**: `string? val = products[0]?.Name` will return *null* if there is no value at index 0
    * using the **null-coalescing operator**, you can provide a fallback value after the `??`:

```cs
string? val = products[0]?.Name ?? "no value"
```

* if you are sure the variable cannot be null, you can use the **null-forgiving operator** `!` to override the null state analysis

```cs
string val = products[0]!.Name
```

* finally, it is also possible to **disable** the null state analysis:

```cs
#pragma warnng disable CS8602
string val = products[0].Name
```

## String interpolation

= creating output by inserting variables in strings

```cs
string s = $"My name is {students[0]?.Name ?? "Tom"} and I am {students[0]?.Age ?? 5} years old."
```

## Using object and collection initializers

To initiliaze an **object** or a **collection**, you can replace:

```cs
// an object
Product kayak = new Product();
kayak.Name = "Kayak";
kayak.Price = 275M;

// an array of strings
string[] names = new string[2];
names[0] = "Jan";
names[1] = "Piet";
```

by making use of `{  }`:

```cs
Product kayak = new Product {Name = "Kayak", Price = 275M};

string[] names = new string[] {"Jan", "Piet"};
```

Also for **dictionaries** (Map ADT), **index intilializers** have been developed:

```cs
Dictionary<string, Product> products = new Dictionary<string, Product> {
    ["kayak"] = new Product {Name = "Kayak", Price = 275M},
    ["boat"] = new Product {Name = "Boat", Price = 165M}
};
```

## Target-typed new expressions

To shorten the initialization of a variable, you can use the `new ()` keyword, without defining the collection type:

```cs
Dictionary<string, Product> products = new () {
    ["kayak"] = new Product {Name = "Kayak", Price = 275M},
    ["boat"] = new Product {Name = "Boat", Price = 165M}
};
```

## Pattern-matching

In C# you can easily perform a **type-check** using the `is` keyword:

```cs
if(data[i] is decimal d) {
    total += d;
}
```

This can also be used in **switch** statements, without the *is* keyword, w or w/o the `when` keyword:

```cs
switch (data[i]) {
    case decimal dec:
        s = "this is a decimal";
        break;
    case int number when int > 50:
        s = "this is an int greater than 50";
        break;
}
```

## Extension methods

Extension methods are created to extend third party classes with new methods, when the source code of these classes is not available.

```cs
public static class MyExtensionMethods {
    public static decimal TotalPrices(this IEnumerable<Product?> products) {
        decimal total = 0;
        foreach (Product? prod in products) {
            total += prod?.Price ?? 0;
        }
        return total;
    }
}
```
* both the class and the extension methods are **static**
* the **this** keyword in front of the first paramater marks the method as an extension method
* the first parameter depicts the class to which the extension method applies
    * in this example the method was applied to the `IEnumerable<Product?>` interface

You can use extension methods **to filter on collections**:

```cs
public static IEnumerable<Product?> FilterByPrice(this IEnumerable<Product?> productEnum, decimal minimumPrice) {
    foreach (Product? prod in productEnum) {
        if ((prod?.Price ?? 0) >= minimumPrice) {
            yield return prod;
        }
    }
}
```
* the **return type** is the same as the first parameter: an `IEnumerable<Product?>`
* the keyword `yield` is used before return of the items

## Lambda expressions

= a shortcut to defining an implementation of a functional interface (= an interface with only one abstract method)

An example: 

```cs
decimal priceFilterTotal = productArray
    .Filter(p => (p?.Price ?? 0) >= 20)
    .TotalPrice();
```

* the lambda expression is `p => (p?.Price ?? 0) >= 20` 
* with one parameter `p`, w/o a type, which is inferred automatically
    * for multiple parameters `( )` are used
* `=>` depicts *goes to*
* and returns a boolean value
    * for multiple statements `{ }` are used