# Command Line tool

DevToys comes with a command line app. It can be helpful on operating systems that don't have a GUI such as a server, or when being used in a Continuous Integration scenario.

## Define a command line tool

To create a command line-based tool, you can implement @"DevToys.Api.ICommandLineTool" and expose it to [MEF](https://learn.microsoft.com/en-us/dotnet/framework/mef/) using @"System.ComponentModel.Composition.ExportAttribute" attribute.

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;

namespace MyProject;

[Export(typeof(ICommandLineTool))]
[Name("My Tool")]
[CommandName(
    Name = "mytool",
    Alias = "mt",
    ResourceManagerBaseName = "MyProject.MyResxClass",
    DescriptionResourceName = nameof(MyProject.Strings.MyToolDescription))]
internal sealed class MyCommandLineTool : ICommandLineTool
{
    public async ValueTask<int> InvokeAsync(ILogger logger, CancellationToken cancellationToken)
    {
        // [...]
        return 0; // Exit code.
    }
}
```

The @"DevToys.Api.NameAttribute" is required and its value should be an unique name that stays internal. This name may appear in logs as it can help for debugging purposes.

The @"DevToys.Api.CommandNameAttribute" is also required and defines multiple information needed for the command:
- **Name**: The name of the command. You will be able to invoke the tool using the following command:
  ```powershell
  > DevToys.CLI.exe mytool
  ```
- **Alias**: An optional alias for the command. It's often shorter than the **Name**.
- **ResourceManagerBaseName**: The full name (namespace and class) to the [.resx](https://learn.microsoft.com/en-us/dotnet/core/extensions/create-resource-files#resources-in-resx-files) file containing localized strings for the tool. This parameter assumes the resource file is in the same assembly than the implementation of @"DevToys.Api.ICommandLineTool".
- **DescriptionResourceName**: The name of the resource in the resource file pointed by **ResourceManagerBaseName** to use as a description of the tool. The description will be displayed when invoking DevToys CLI with the `--help` parameter
  ```powershell
  > DevToys.CLI.exe mytool --help
  ```

When the tool is invoked by the user, the `InvokeAsync` method will be invoked. This is where your tool perform the task the user expect from your tool. A `logger` argument can be used to log some informations on how your tool behaved. It is a good practice to **_not_** log user personal information. A `cancellationToken` argument can be used to know when the user wish to stop the tool (using `Ctrl+C` in the terminal to attempt to stop the process). You should try to stop the work of the tool as soon as possible when the cancellation token gets canceled.

The tool can display an output to the user using, for example @"System.Console" API.

Finally, the tool should return an integer that gives an insight on how the tool behaves. Often, tools return `0` when everything worked as expected, or `-1` when an error occured.

## Define parameters

To define a parameter for a command line tool, you can add a property to the class with the @"DevToys.Api.CommandLineOptionAttribute" attribute.

```csharp
[CommandLineOption(
    Name = "input",
    Alias = "i",
    IsRequired = true,
    DescriptionResourceName = nameof(Strings.InputOptionDescription))]
internal string Input { get; set; }
```

In the example above, the `Input` property will be automatically set before `InvokeAsync` is invoked. User will be able to use it in a command line like the following:
```powershell
> DevToys.CLI.exe mytool -i "hello world"
```

### Required parameters

The @"DevToys.Api.CommandLineOptionAttribute.IsRequired" property indicates whether the command should fail when the command parameter isn't set. For example, the following command will result in an error:
```powershell
> DevToys.CLI.exe mytool
Option '--input' is required.

Description:
  My tool description

Usage:
  DevToys.CLI mytool [options]

Options:
  -i, --input <input> (REQUIRED)    My parameter description
  -?, -h, --help                    Show help and usage information
```

When @"DevToys.Api.CommandLineOptionAttribute.IsRequired" property is `false` and that the user does not specify the parameter in the command, the property's value will be equal to the [default](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/default) value valid for the property type. For example, if the property's type is @"System.String", the value will be `null`. If the the property's type is @"System.Int32", the value will be `0`.

### Parameter types

The parameter properties can be of many types. Not every types are supported though. Only some common types that can be easily parsed from a command line prompt are supported:
- @"System.Boolean"
- @"System.DateOnly"
- @"System.DateTime"
- @"System.DateTimeOffset"
- @"System.TimeOnly"
- @"System.TimeSpan"
- @"System.Decimal"
- @"System.Double"
- @"System.Single"
- @"System.Int16"
- @"System.Int32"
- @"System.Int64"
- @"System.UInt16"
- @"System.UInt32"
- @"System.UInt64"
- @"System.Byte"
- @"System.SByte"
- @"System.String"
- @"System.Guid"
- @"System.IO.DirectoryInfo"
- @"System.IO.FileInfo"
- @"System.IO.FileSystemInfo"
- @"System.Enum"
- @"System.Array" of all the above

Examples:

```csharp
[CommandLineOption(Name = "input", Alias = "i", DescriptionResourceName = nameof(Strings.InputOptionDescription))]
internal string Input { get; set; }

enum Fruit
{
    Banana,
    Strawberry,
    Mango
}

[CommandLineOption(Name = "fruit", Alias = "f", DescriptionResourceName = nameof(Strings.FruitOptionDescription))]
internal Fruit Fruit { get; set; }

[CommandLineOption(Name = "positions", Alias = "p", DescriptionResourceName = nameof(Strings.PositionsOptionDescription))]
internal int[] Positions { get; set; }
```

This tool can be invoked using the following command sample:

```powershell
> DevToys.CLI.exe mytool --input "Hello world!" --fruit Banana --positions 42 64 123
```

### Multiple types parameters

Sometime, a single parameter can accept multiple types of input. For example, a JSON Formatter tool could accept 2 types of input:
1. A path to a JSON file.
2. A JSON directly (as a string).

To achieve this, DevToys comes with support of [OneOf](https://github.com/mcintyre321/OneOf) .NET library. You can see this library as _a compile time checked switch statement_.

Example:

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;
using OneOf;

namespace MyProject;

[Export(typeof(ICommandLineTool))]
[Name("My Tool")]
[CommandName(
    Name = "mytool",
    Alias = "mt",
    ResourceManagerBaseName = "MyProject.MyResxClass",
    DescriptionResourceName = nameof(Strings.MyToolDescription))]
internal sealed class MyCommandLineTool : ICommandLineTool
{
    [CommandLineOption(Name = "input", Alias = "i", IsRequired = true, DescriptionResourceName = nameof(Strings.InputOptionDescription))]
    internal OneOf<FileInfo, string> Input { get; set; }

    public ValueTask<int> InvokeAsync(ILogger logger, CancellationToken cancellationToken)
    {
        return Input.Match(
            file =>
                TreatFileInputAsync(
                    file,
                    logger,
                    cancellationToken),
            text =>
                TreatTextInputAsync(
                    text,
                    logger,
                    cancellationToken));
    }

    private async ValueTask<int> TreatFileInputAsync(FileInfo file, ILogger logger, CancellationToken cancellationToken)
    {
        // Ensure the file has the expected extension or format...etc
        return 0; // Exit code.
    }

    private async ValueTask<int> TreatTextInputAsync(string input, ILogger logger, CancellationToken cancellationToken)
    {
        // [...]
        return 0; // Exit code.
    }
}
```

This tool can be invoked using the following command samples:

```powershell
> DevToys.CLI.exe mytool --input "C:/an-existing-file-on-the-hard-drive.txt"
```

```powershell
> DevToys.CLI.exe mytool --input "hello world"
```

In the first example, the `Input` property will have a @"System.IO.FileInfo" value if the a file is located at `C:/an-existing-file-on-the-hard-drive.txt`. Otherwise, it will be a @"System.String".

#### Helpers

Some extension methods are available to quickly read `OneOf<FileInfo, string>` parameters: @"DevToys.Api.OneOfExtensions".

### Parameter's default value

When a parameter is optional (when @"DevToys.Api.CommandLineOptionAttribute.IsRequired" property is `false`), a default value can be set directly to the property.

Example:
```csharp
[CommandLineOption(Name = "input", Alias = "i", DescriptionResourceName = nameof(Strings.InputOptionDescription))]
internal string Input { get; set; } = "Default string";

[CommandLineOption(Name = "index", Alias = "id", DescriptionResourceName = nameof(Strings.IndexOptionDescription))]
internal int Index { get; set; } = 42;
```

## Show a progression

Some tools can be takes many seconds or minutes to run depending on their goal and user's device capabilities. It is important to indicate to the user a progression of a task. DevToys provides a class that displays a progress bar in the console: @"DevToys.Api.ConsoleProgressBar".

You can use it as the following:
```csharp
using DevToys.Api;
using System.ComponentModel.Composition;

namespace MyProject;

[Export(typeof(ICommandLineTool))]
[Name("My Tool")]
[CommandName(
    Name = "mytool",
    Alias = "mt",
    ResourceManagerBaseName = "MyProject.MyResxClass",
    DescriptionResourceName = nameof(Strings.MyToolDescription))]
internal sealed class MyCommandLineTool : ICommandLineTool
{
    public async ValueTask<int> InvokeAsync(ILogger logger, CancellationToken cancellationToken)
    {
        using (var progressBar = new ConsoleProgressBar())
        {
            progressBar.Report(20);
            await Task.Delay(2000);
            progressBar.Report(80);
            await Task.Delay(5000);
        }

        Console.WriteLine("We're done!");
        return 0;
    }
}
```

The example below will show the following progress bar for 5 seconds:
```
> DevToys.CLI.exe mytool
[########--] 80%
```
Then the displayed progress bar disappears when `progressBar` gets disposed. The program continues by showing an output:
```
> DevToys.CLI.exe mytool
We're done!
```

## Interacting with files

DevToys provides a cross-platform service for accessing files, called @"DevToys.Api.IFileStorage" and can be injected in your MEF exported class:

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;

namespace MyProject;

[Export(typeof(ICommandLineTool))]
[Name("My Tool")]
[CommandName(
    Name = "mytool",
    Alias = "mt",
    ResourceManagerBaseName = "MyProject.MyResxClass",
    DescriptionResourceName = nameof(Strings.MyToolDescription))]
internal sealed class MyCommandLineTool : ICommandLineTool
{
    [Import]
    private IFileStorage _fileStorage = null!;

    public async ValueTask<int> InvokeAsync(ILogger logger, CancellationToken cancellationToken)
    {
        // [...]
        return 0;
    }
}
```

The field `_fileStorage` will be set automatically when the `MyCommandLineTool` class is instantiated.

This service provides multiple API that can help with:
- Accessing a path to a DevToys app cache.
- Creating temporary files that automatically gets deleted from the hard drive when not needed anymore.
- Prompting the user to enter a file to open or save, with specific file extension.

There are more methods available.

## Limitations

Some DevToys MEF services are not supported in CLI tool, such as:
- @"DevToys.Api.IClipboard"
- @"DevToys.Api.IThemeListener"
- @"DevToys.Api.IFontProvider"

## Debugging a command line tool

To debug a command line tool, use the `DevToys CLI` startup configuration, as defined in [Debug and extension](../getting-started/debug-an-extension.md). Change the `commandLineArgs` in your **launchSettings.json** to invoke the command and options you created. For example:
```json
 {
   "$schema": "https://json.schemastore.org/launchsettings.json",
   "profiles": {
     "DevToys GUI": {
       "commandName": "Executable",
       "executablePath": "%DevToysGuiDebugEntryPoint%",
       "environmentVariables": {
         "EXTRAPLUGIN": "$(TargetDir)"
       }
     },
     "DevToys CLI": {
       "commandName": "Executable",
       "executablePath": "%DevToysCliDebugEntryPoint%",
       "commandLineArgs": "mytool --input \"hello world\"",
       "environmentVariables": {
         "EXTRAPLUGIN": "$(TargetDir)"
       }
     }
   }
 }
```