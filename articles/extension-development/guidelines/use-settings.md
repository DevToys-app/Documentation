# Use settings

DevToys tools, especially those with a GUI, often expose options that persist even after the app is restarted. This feature allows users to maintain the configuration of DevToys and its tools from the last session.

## Settings Provider

@"DevToys.Api.ISettingsProvider" is a MEF service available for reading and writing settings. You can define a setting using @"DevToys.Api.SettingDefinition`1".  This service can be injected into a class using MEF's @"System.ComponentModel.Composition.ImportAttribute" attribute.

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
    private static readonly SettingDefinition<bool> mySetting // Define a setting.
        = new(
            name: $"{nameof(mySetting)}",                     // Unique name for the setting.
            defaultValue: true);                              // Default value for the setting.

    [Import] // Import the settings provider.
    private ISettingsProvider _settingsProvider = null;

    public UIToolView View
        => new UIToolView(
            Stack()
                .Vertical()
                .WithChildren(
                    Button()
                        .Text("Click me")
                        .OnClick(OnButtonClick)));

    public void OnDataReceived(string dataTypeName, object? parsedData)
    {
        // Handle Smart Detection.
    }

    private void OnButtonClick()
    {
        // Toggle the setting value.
        bool settingValue = _settingsProvider.GetSetting(mySetting);
        bool newValue = !settingValue;
        _settingsProvider.SetSetting(mySetting, newValue);
    }
}
```

### UI component for settings

DevToys GUI provides UI elements that can automatically handle settings.  See [Settings UI element](UX/basic-input/setting.md).

## Data types

@"DevToys.Api.SettingDefinition`1" supports values of various types. Some common types that can be easily parsed are already supported:
- @"System.Boolean"
- @"System.DateTimeOffset"
- @"System.Double"
- @"System.Int32"
- @"System.String"
- @"System.Enum"
- @"System.Array" of all the above

For more complex objects, you can save and restore them by providing methods to persist the data as a string and parse it from a string. This can be done using the @"DevToys.Api.SettingDefinition`1.Serialize" and @"DevToys.Api.SettingDefinition`1.Deserialize" properties, which can be set through a constructor.

## Where are settings persisted

### DevToys CLI

Settings are stored in a file named `Cache/settings.ini` in the folder where DevToys CLI is installed. If the app is in a folder that requires admin rights, such as `Program Files` on Windows, the app should be run with administrator privileges to persist settings.

### DevToys GUI

## [**Windows**](#tab/windows)

Settings are stored in the Local AppData, in a file named `%LocalAppData%/DevToys/settings.ini`.

## [**macOS**](#tab/macos)

Settings are stored in the file named `Users/<username>/library/preferences/com.devtoys.plist`.

## [**Linux**](#tab/linux)

Settings are stored in the file named `home/.local/share/devtoys/settings.ini`. The folder `home/.local/share` may changed based on the value of the `XDG_DATA_HOME` environment variable.

***

## Do & Don't

- **DO** take advantage of settings when using a @"DevToys.Api.IUISetting" and @"DevToys.Api.IUISettingGroup". See [Settings UI element](UX/basic-input/setting.md).
- **DO NOT** use settings for user text input such as text box (@"DevToys.Api.IUISingleLineTextInput", @"DevToys.Api.IUIMultiLineTextInput" or @"DevToys.Api.IUIPasswordInput" for example), as settings are stored in clear text on the computer, and user text input may contain sensitive information..
- **DO NOT** persist a setting on multiple lines when serializing the value manually using @"DevToys.Api.SettingDefinition`1.Serialize".