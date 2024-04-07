# Use files

Accessing the hard drive is important to perform certain tasks, or simply asking the user to select a location. DevToys provides an API that simplify the cross-platform aspect of accessing user's files.

## File Storage

@"DevToys.Api.IFileStorage" is a MEF service available for accessing user's hard drive in an easy manner. This service can be injected into a class using MEF's @"System.ComponentModel.Composition.ImportAttribute" attribute.

Below is a general example demonstrating how to import the service in a tool. You will find later some common development scenarios and how the file storage service can be used.

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;
using static DevToys.Api.GUI;

namespace MyProject;

[Export(typeof(IGuiTool))]
[Name("My Tool")]
[ToolDisplayInformation(
    IconFontName = "FluentSystemIcons",
    IconGlyph = '\uE670',
    ResourceManagerAssemblyIdentifier = nameof(MyResourceAssemblyIdentifier),
    ResourceManagerBaseName = "MyProject.Strings",
    ShortDisplayTitleResourceName = nameof(Strings.ShortDisplayTitle),
    DescriptionResourceName = nameof(Strings.Description),
    GroupName = "My Group")]
internal sealed class MyGuiTool : IGuiTool
{
    [Import] // Import the file storage service.
    private IFileStorage _fileStorage = null;

    public UIToolView View
        => new UIToolView(
            Stack()
                .Vertical()
                .WithChildren(
                    Button()
                        .Text("Click me")
                        .OnClick(OnButtonClickAsync)));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }

    private async ValueTask OnButtonClickAsync()
    {
        // Create a self-destroying temp file.
        FileInfo tempFile = _fileStorage.CreateSelfDestroyingTempFile();

        // Write into a file in the app's data folder. This is implied by the fact we are using a relative path.
        using FileStream stream = _fileStorage.OpenWriteFile("file.txt", replaceIfExist: true);

        // Ask the user to pick a TXT or JSON file.
        using SandboxedFileReader? fileReader = await _fileStorage.PickOpenFileAsync("txt", ".json");
        if (fileReader is not null)
        {
            using var streamReader = fileReader.GetNewAccessToFileContentAsync(CancellationToken.None);
            // todo: read the file content.
        }
    }
}
```

### Prompting the user to select a file

Whether you're developing a tool with a GUI or working in a CLI, you can prompt the user to select one or multiple files to open or write into.

- @"DevToys.Api.IFileStorage.PickOpenFileAsync(System.String[])": Prompt the user to select a single file to open.
- @"DevToys.Api.IFileStorage.PickOpenFilesAsync(System.String[])": Prompt the user to select one or multiple files to open.
- @"DevToys.Api.IFileStorage.PickFolderAsync": Prompt the user to select one or multiple files to open.
- @"DevToys.Api.IFileStorage.PickSaveFileAsync(System.String[])": Prompt the user to select a single file to save.

Some of these methods return a @"DevToys.Api.SandboxedFileReader". See below how to use them.

>[!NOTE]
>In tools with a GUI, a generic UI element can be used to offer the user to drap & drop or paste files in a tool. [See File selector](UX/basic-input/file-selector.md).

#### Using sandboxed file reader

Some API from @"DevToys.Api.IFileStorage", or @"DevToys.Api.IUIFileSelector", return a @"DevToys.Api.SandboxedFileReader". This class provides a read-only access to a file on the file system. Such a class, with limited information about the file, has been required due to the Drag & Drop feature of the web browser rendering the UI of DevToys. Files that are dropped in the UI are sandboxed by the web browser for security purposes. The web browser does not expose every information about it, such as the full path to the file. However, it exposes a stream to read the file.

The @"DevToys.Api.SandboxedFileReader" has a similar behavior. It exposes a set of properties and methods that can be used to read the file.
- @"DevToys.Api.SandboxedFileReader.OpenReadFileAsync(System.Threading.CancellationToken)": Creates a stream allowing to read the file.
- @"DevToys.Api.SandboxedFileReader.GetNewAccessToFileContentAsync(System.Threading.CancellationToken)": Creates a stream allowing to read the file.
- @"DevToys.Api.SandboxedFileReader.CopyFileContentToAsync(System.IO.Stream,System.Threading.CancellationToken)": Copies the integrality of the file into the given stream. If the file is large, this method can pressure the memory depending on how the destination stream is designed.

>[!IMPORTANT]
>The file stays in memory until @"DevToys.Api.SandboxedFileReader" is disposed. It is important to dispose it when you do not need it anymore. Disposing an instance of @"DevToys.Api.SandboxedFileReader" also dispose every stream received from @"DevToys.Api.SandboxedFileReader.GetNewAccessToFileContentAsync(System.Threading.CancellationToken)".

##### Creating a sandboxed file reader

Sometimes you need to create an instance of @"DevToys.Api.SandboxedFileReader" yourself. You can use the static method @"DevToys.Api.SandboxedFileReader.FromFileInfo(System.IO.FileInfo)" for this purpose.

### Creating a temporary file

You can create a self-destructing file using @"DevToys.Api.IFileStorage.CreateSelfDestroyingTempFile(System.String)". This method will create a file with a randomly generated name in a DevToys cache folder dedicated to temporary files. The file will be automatically deleted from the hard drive when the DevToys stops, or the next time is starts. You do not need to remove the file yourself, but there's also no harm at doing so.