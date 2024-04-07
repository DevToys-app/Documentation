# Do & Don't

Below are some general recommendation of good practices to consider when developing a tool for DevToys.

## Reading string

- **DO** assume that user may open large text document in @"DevToys.Api.IUIMultiLineTextInput" or in a CLI tool (hundred megabytes at least) and consider this scenario when treating strings.
- **DO** use [Memory and Spans](https://learn.microsoft.com/en-us/dotnet/standard/memory-and-spans/) to parse a string in a allocation-friendly manner.
- **DO NOT** use string manipulation methods such as @"System.String.Split(System.Char,System.Int32,System.StringSplitOptions)" or @"System.String.Substring(System.Int32)" to parse a string as each call results in a new string allocated. Try using Memory and Spans instead.

## Reading files

- **DO** assume that user may open large files.
- **DO** consider reading streams using a buffer.

## Logging

- **DO** report errors.
- **DO** report information about performance of some tasks, if relevant.
- **DO** report some system information, but only if it can truly be helpful when investigating performance or compatibility issues.
- **DO NOT** report what the user input in the app as it might contains user personal information.