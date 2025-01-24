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
