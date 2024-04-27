# Setup

In this Getting Started section, we will walk you through the process of setting up your development environment, creating, debugging, and publishing your first extension.

>[!NOTE]
>This guide assumes that you have already installed DevToys and DevToys CLI on your computer. It is also expected that you have some experience with [C# and .NET](https://learn.microsoft.com/en-us/training/paths/get-started-c-sharp-part-1/) and are familiar with the concept of [dependency injection](https://en.wikipedia.org/wiki/Dependency_injection).

The setup process varies slightly depending on the operating system you are developing on. Below are the instructions for Windows, macOS, and Linux.

## [**Windows**](#tab/windows)

#### Setting the Environment Variable

To debug an extension seamlessly, you need to set a specific environment variable on your system.
Follow these steps:
1. Locate `DevToys.exe` and `DevToys.CLI.exe` on your computer. If you installed the app using an installer (not through the Microsoft Store), it is likely in `C:\Users\[user name]\AppData\Local\Programs\DevToys\DevToys.exe`. Keep this path handy for a later step.
1. Launch a **PowerShell** command prompt using **Windows Terminal** or **Windows PowerShell** app.
1. Enter the following command to define the `DevToysGuiDebugEntryPoint` and `DevToysCliDebugEntryPoint` environment variables.
   ```powershell
   [Environment]::SetEnvironmentVariable("DevToysGuiDebugEntryPoint", "<your_path>\DevToys.exe", "User")
   [Environment]::SetEnvironmentVariable("DevToysCliDebugEntryPoint", "<your_path>\DevToys.CLI.exe", "User")
   ```
   Replace `<your_path>\DevToys.exe` and `<your_path>\DevToys.CLI.exe` with the actual path you located in Step 1.

#### Selecting Your Editor

We recommend you use [**Visual Studio 2022**](https://visualstudio.microsoft.com/#vs-section) for its robust development capabilities for C# and .NET. This IDE also offers a free tier. However, if you are already familiar with [**JetBrains Rider**](https://www.jetbrains.com/rider/), it is an excellent alternative. Finally, [**Visual Studio Code**](https://visualstudio.microsoft.com/#vscode-section) is another option, particularly if you prefer a more lightweight text editor.

- For **Visual Studio** users, please select `.NET desktop development` workload during installation.
- For **Visual Studio Code** users, please install the [**C# DevKit**](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit) and [**ResX Editor**](https://marketplace.visualstudio.com/items?itemName=DominicVonk.vscode-resx-editor) extensions.
- For **JetBrains Rider** users, install .NET 8.0 if prompted.

## [**macOS**](#tab/macos)

#### Setting the Environment Variable

To debug an extension seamlessly, you need to set a specific environment variable on your system.
Follow these steps:
1. Locate `DevToys.app` and `DevToys.CLI.app` on your computer. It is common to find it in `~/Applications/DevToys.app`. Keep this path handy for a later step.
1. Launch the **Terminal** application from your **Applications** folder or using **Spotlight** search.
1. Enter the command `nano ~/.zshrc`.
1. Within the opened `nano` editor, append the following line at the end of the document to define the `DevToysGuiDebugEntryPoint` and `DevToysCliDebugEntryPoint` variables.
   ```bash
   export DevToysGuiDebugEntryPoint="<your_path_to_DevToys.app>/Contents/MacOS/DevToys"
   export DevToysCliDebugEntryPoint="<your_path_to_DevToys.CLI.app>/Contents/MacOS/DevToys.CLI"
   ```
   Replace `<your_path_to_DevToys.app>` with the actual path you located in Step 1.
1. Save and exit Nano (press `Ctrl+X`, then confirm by pressing `Y`, and finally press `Enter`)
1. Enter the command `source ~/.zshrc` in the Terminal to apply the changes you made.
1. Restart macOS.

#### Selecting Your Editor

We recommend you use [**JetBrains Rider**](https://www.jetbrains.com/rider/) as it provides a full IDE experience for C#. [**Visual Studio Code**](https://visualstudio.microsoft.com/#vscode-section) is another option, particularly if you prefer a more lightweight text editor.

- For **Visual Studio Code** users, please install the [**C# DevKit**](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit) and [**ResX Editor**](https://marketplace.visualstudio.com/items?itemName=DominicVonk.vscode-resx-editor) extensions.
- For **JetBrains Rider** users, install .NET 8.0 if prompted.

## [**Linux**](#tab/linux)

#### Setting the Environment Variable

To debug an extension seamlessly, you need to set a specific environment variable on your system.
Follow these steps:
1. Locate `DevToys` and `DevToys.CLI` programs on your computer. It is common to find it in `/usr/bin/devtoys` and `/usr/bin/devtoys.cli`. Keep this path handy for a later step.
1. Launch the Terminal application.
1. Enter the command `nano ~/.bashrc`.
1. Within the opened `nano` editor, append the following line at the end of the document to define the `DevToysGuiDebugEntryPoint` and `DevToysCliDebugEntryPoint` variables.
   ```bash
   export DevToysGuiDebugEntryPoint="<your_path>/DevToys"
   export DevToysCliDebugEntryPoint="<your_path>/DevToys.CLI"
   ```
   Replace `<your_path>` with the actual path you located in Step 1.
1. Save and exit Nano (press `Ctrl+X`, then confirm by pressing `Y`, and finally press `Enter`)
1. Enter the command `source ~/.bashrc` in the Terminal to apply the changes you made.

#### Selecting Your Editor

We recommend you use [**JetBrains Rider**](https://www.jetbrains.com/rider/) as it provides a full IDE experience for C#. [**Visual Studio Code**](https://visualstudio.microsoft.com/#vscode-section) is another option, particularly if you prefer a more lightweight text editor.

- For **Visual Studio Code** users, please install the [**C# DevKit**](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit) and [**ResX Editor**](https://marketplace.visualstudio.com/items?itemName=DominicVonk.vscode-resx-editor) extensions.
- For **JetBrains Rider** users, install .NET 8.0 if prompted.

***