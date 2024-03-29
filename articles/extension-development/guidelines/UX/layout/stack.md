# Stack

You can create a vertical or horizontal stack using the @"DevToys.Api.GUI.Stack" static method, which produces a @"DevToys.Api.IUIStack".

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
    public UIToolView View
        => new UIToolView(
            Stack()
                .Vertical()
                .WithChildren(
                    Stack()
                        .Horizontal()
                        .WithChildren(
                            Button().Text("A"),
                            Button().Text("B"),
                            Button().Text("C")),
                    Stack()
                        .Horizontal()
                        .WithChildren(
                            Button().Text("D"),
                            Button().Text("E"),
                            Button().Text("F"))));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }
}
```

The code above produces the following UI:

![DevToys - My Tool - Stack](assets/stack.png)