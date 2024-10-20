This is the documentation website of DevToys

# Setup this repository

Install [.NET SDK 8.0](https://dotnet.microsoft.com/en-us/download) or higher, then open a command line prompt and enter the following command:
```powershell
> git submodule update --init --recursive
> git submodule update --remote --merge
> dotnet tool update -g docfx
```

# How to build

```powershell
> docfx
```

# How to run locally

```powershell
> docfx --serve
```

Now you can preview the website on [http://localhost:8080/](http://localhost:8080/)

## Hot reload

Instead of running the command above, use NodeJS:

1. Install the dev dependencies by running:
   ```powershell
   > npm install -D
   ```
2. Run:
   ```powershell
   > npm run watch
   ```

> Note!
>
> If you are running the local server on Linux, replace the `watch` script with this:
> `npm-watch build & npm run browser-livereload`