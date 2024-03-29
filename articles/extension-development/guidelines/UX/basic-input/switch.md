# Toggle Switch

You can create a toggle switch using the @"DevToys.Api.GUI.Switch" static method, which produces a @"DevToys.Api.IUISwitch".

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
                    Switch()
                        .On(),
                    Switch()
                        .OnText("Enabled")
                        .OffText("Disabled")
                        .OnToggle(OnToggleChanged)));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }

    private void OnToggleChanged(bool isOn)
    {
    }
}
```

The code above produces the following UI:

![DevToys - My Tool - Toggle Switch](assets/switch.png)