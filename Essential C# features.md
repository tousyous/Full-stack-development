# Essential C# features

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