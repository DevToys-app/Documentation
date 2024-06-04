# Extension Management

DevToys 2.0 allows to develop and install third-party extension, increasing the capabilities of the app by making the amount of tools theorically infinite.

>[!IMPORTANT]
>Third-party extensions can modify the functionality of existing tools and access your computer without your permission. This may expose your data to the internet or install unwanted software on your device. We strongly recommend that you exercise caution and only install extensions that you trust. DevToys authors are not responsible for any damages or losses caused by extensions. To monitor DevToys or any extension's network activity, tools like [Wireshark](https://www.wireshark.org/), [Little Snitch](https://www.obdev.at/products/littlesnitch/index.html), or [Fiddler](https://www.telerik.com/fiddler) can be utilized to track incoming and outgoing connections.

Extensions are packed as a NuGet package (`nupkg`), which is in fact a ZIP archive. When installing an extension, the whole archive is uncompressed in a folder.

## [**Windows**](#tab/windows)

On Windows, extensions are installed in `%LocalAppData%/DevToys/Plugins/`.

## [**macOS**](#tab/macos)

On macOS, extensions are installed in `~/Library/com.devtoys/Plugins/`.

## [**Linux**](#tab/linux)

On Linux, extensions are installed in `home/.local/share/devtoys/Plugins/`. The folder `home/.local/share` may changed based on the value of the `XDG_DATA_HOME` environment variable.

***

There is currently no way to completely prevent someone from installing an extension manually.