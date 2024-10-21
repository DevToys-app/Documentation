# Number Input

You can display a number input with associated command bar using the @"DevToys.Api.GUI.NumberInput" static method, which produces a @"DevToys.Api.IUINumberInput".

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
    private readonly IUIMultiLineTextInput _multiLineTextInput = MultiLineTextInput();

    public UIToolView View
        => new UIToolView(
            NumberInput()
                .Title("Title")
                .Maximum(100)
                .Minimum(0)
                .OnValueChanged(OnValueChanged));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }

    private void OnValueChanged(double value)
    {
        // Handle value changed.
    }
}
```

The code above produces the following UI:

![DevToys - My Tool - Number Input](assets/number-input.png)
