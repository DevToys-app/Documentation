# Logs

DevToys collects some minimal anonymized data locally for troubleshooting purposes. It does not upload logs to the internet.

>[!IMPORTANT]
>While by default, DevToys logs anonymized data and doesn't upload them to the internet, it is possible that a third-party extension do otherwise. DevToys and its author currently have no control on what extensions do with the user data. It is encouraged to practice caution when installing extensions. To monitor DevToys or any extension's network activity, tools like [Wireshark](https://www.wireshark.org/), [Little Snitch](https://www.obdev.at/products/littlesnitch/index.html), or [Fiddler](https://www.telerik.com/fiddler) can be utilized to track incoming and outgoing connections.

Logs are stored on the local machine in files with a name formatted as `log-yyymmddThhmmss.txt`. For example, `log-20240603T075343.txt`. A log file is created every time DevToys starts. On DevToys startup, logs that are older than 2 days are permanently deleted from the computer. This is to avoid filling your computer with a large amount of useless files.

## [**Windows**](#tab/windows)

On Windows, logs are stored in `%LocalAppData%/DevToys/Logs/`.

## [**macOS**](#tab/macos)

On macOS, logs are stored in `~/Library/Caches/com.devtoys/Logs/`.

## [**Linux**](#tab/linux)

On Linux, logs are stored in `home/.local/share/devtoys/Logs/`. The folder `home/.local/share` may changed based on the value of the `XDG_DATA_HOME` environment variable.

***