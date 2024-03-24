# Create a custom icon

In DevToys, the icon of each tool and group is a glyph coming from a font. Each tool must have an icon to help the user quickly identifying them thanks to a visual identity.

## Create a custom font

DevToys comes with the font [FluentSystemIcons](https://github.com/microsoft/fluentui-system-icons) and can be used by every tools. If you would like to create a custom icon font instead of using a glyph from `FluentSystemIcons`, we recommend you to use the following tools:
- [Figma](https://www.figma.com/)
- [Inkscape](https://inkscape.org/)
- [FontForge](https://fontforge.org/)

>[!WARNING]
>Only TTF and OTF font format are supported. WOFF and WOFF2 aren't supported at the moment.

## Embed a font in an extension

Now we need to indicate to DevToys how to access this custom font.

An extension for DevToys can provide a font that exist on the hard drive. However, we recommend to embed your custom font in your project. The benefit is that there's no need to worry about the font file missing on the hard drive or not being found due to a path changing from an operating system to another.

To embed a font in a .NET project, open your project file (`*.csproj`) with a text editor and add the following tag:
```xml
<Project Sdk="Microsoft.NET.Sdk">
    <ItemGroup>
        <EmbeddedResource Include="Assets\MyFont.ttf" />
    </ItemGroup>
</Project>
```

## Implement IResourceAssemblyIdentifier

To provide a custom font, you can implement @"DevToys.Api.IResourceAssemblyIdentifier" and expose it to [MEF](https://learn.microsoft.com/en-us/dotnet/framework/mef/) using @"System.ComponentModel.Composition.ExportAttribute" attribute.

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;

namespace MyProject;

[Export(typeof(IResourceAssemblyIdentifier))]
[Name(nameof(MyResourceAssemblyIdentifier))]
internal sealed class MyResourceAssemblyIdentifier : IResourceAssemblyIdentifier
{
    public ValueTask<FontDefinition[]> GetFontDefinitionsAsync()
    {
        throw new NotImplementedException();
    }
}
```

## Expose the font to DevToys

To expose your font to DevToys, you can implement the @"DevToys.Api.IResourceAssemblyIdentifier.GetFontDefinitionsAsync" method, get a @"System.IO.Stream" from the embedded font, and return a @"DevToys.Api.FontDefinition".

Defined fonts are shared across every tools and extensions. Make sure that the name of the font provided in @"DevToys.Api.FontDefinition" is unique.

```csharp
using DevToys.Api;
using System.ComponentModel.Composition;

namespace MyProject;

[Export(typeof(IResourceAssemblyIdentifier))]
[Name(nameof(MyResourceAssemblyIdentifier))]
internal sealed class MyResourceAssemblyIdentifier : IResourceAssemblyIdentifier
{
    public ValueTask<FontDefinition[]> GetFontDefinitionsAsync()
    {
        var assembly = Assembly.GetExecutingAssembly();
        string resourceName = "MyProject.Assets.MyFont.ttf";

        Stream stream = assembly.GetManifestResourceStream(resourceName)!;

        return new ValueTask<FontDefinition[]>(new[]
        {
            new FontDefinition("MyFont", stream)
        });
    }
}
```