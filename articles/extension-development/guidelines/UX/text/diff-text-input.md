# Diff Text Input

You can compare two texts using the @"DevToys.Api.GUI.DiffTextInput" static method, which produces a @"DevToys.Api.IUIDiffTextInput".

>[!IMPORTANT]
>This editor has no `Copy` or `Save` command bar.

## Sample

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
    private readonly IUIMultiLineTextInput _multiLineTextInput = MultilineTextInput();

    public UIToolView View
        => new UIToolView(
            DiffTextInput()
                .Title("Title")
                .SplitView()
                .OriginalText("Hello world!")
                .ModifiedText("Hello DevToys!"));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }
}
```

The code above produces the following UI:

![DevToys - My Tool - Diff Text Input](assets/diff-text-input.png)