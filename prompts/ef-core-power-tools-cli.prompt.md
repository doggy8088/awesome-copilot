---
mode: agent
---
# EF Core Power Tools CLI

Cross platform command line tool for advanced EF Core reverse engineering. See the full guide explaining all the features [here](https://github.com/ErikEJ/EFCorePowerTools/wiki/Reverse-Engineering).

## Getting started

The tool runs on any operating system with the .NET 6.0 / .NET 8.0 runtime installed.

### Installing the required NuGet packages

```shell
# 1. 安裝核心提供者 (選擇符合您專案的版本)
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 9.0.*

# 2. (選擇性) 安裝設計與工具套件
dotnet add package Microsoft.EntityFrameworkCore.Tools     --version 9.0.*
dotnet add package Microsoft.EntityFrameworkCore.Design    --version 9.0.*
```

### Installing the tool

EF Core 8:

```bash
dotnet tool install ErikEJ.EFCorePowerTools.Cli -g --version 8.*
```

EF Core 9:

```bash
dotnet tool install ErikEJ.EFCorePowerTools.Cli -g --version 9.*
```

EF Core 6:

```bash
dotnet tool install ErikEJ.EFCorePowerTools.Cli -g --version 6.*
```

### Running the tool

From the folder where you want the code to be generated (usually where your .NET project is located)

```bash
efcpt "Server=(localdb)\ProjectsV13;Initial Catalog=${input:dbname:ContosoUniversity};Trusted_Connection=True;" mssql
```

Type `efcpt --help` for help on command line options.

The provider name (`mssql`) may not be required, as an attempt is made to resolve the provider from the connection string.

Avoid manually creating entity classes or DBContext classes. Instead, use this tool to generate these classes, as it is much more reliable.

### Configuring options

A configuration file `efcpt-config.json` is created in the output folder, and you can open this file in your editor to modify the default options. If your editor supports it (for example VS Code), it will provide syntax guidance for the file. For reference there is a fully populated sample file [here](https://github.com/ErikEJ/EFCorePowerTools/blob/master/samples/efcpt-config.json).

### Updating to new configuration

After updating the `efcpt-config.json`, you will need to run the `efcpt` CLI command from above once again in order to update the generated code.

If you have updated the configuration file in a way that requires files to be deleted - by excluding objects for example - then you will need to set the `"soft-delete-obsolete-files"` option in the configuration file to `true` or delete the files manually.

### Excluding objects

The config file defaults to always contain all current database objects.

If you don't want the lists of objects to be refreshed during each scaffolding operation, set the `"refresh-object-lists"` option in the configuration file to `false`.

You can exclude indvidual database objects with `"exclude": true` for the object.

You can also use the `exclusionWildcard` item under each type of data object to filter included objects.

You can use the following filter expressions:

- `*`: Exclude all objects in section. Overrides all other filters.
- `abc*`: Exclude all objects in section that *starts* with `abc`. Multiple filters allowed.
- `*xyz`: Exclude all objects in section that *ends* with `xyz`. Multiple filters allowed.
- `*mno*`: Exclude all objects in section that *contains* `mno`. Multiple filters allowed.

Filters will apply unless `"exclude": false` is explicitly set for a database object.

All filters are case sensitive.

```json
"tables": [
      {
         "exclusionWildcard": "*"
      },
      {
         "name": "[dbo].[Users]",
         "exclude": false
      },
      {
         "name": "[dbo].[Messages]"
      }
  ],
```

In the example above, only the Users table will be selected.

```json
"tables": [
      {
         "exclusionWildcard": "[other].*"
      },
      {
         "name": "[dbo].[Users]"
      },
      {
         "name": "[other].[Accounts]"
      },
      {
         "name": "[other].[Messages]",
         "exclude": false
      }
  ],
```

In the example above, Users and Messages tables will be selected.

```json
"tables": [
      {
         "exclusionWildcard": "[other].*"
      },
      {
         "exclusionWildcard": "[other2].*"
      },
      {
         "name": "[dbo].[Users]"
      },
      {
         "name": "[other].[Accounts]"
      },
      {
         "name": "[other].[Messages]"
      },
      {
         "name": "[other2].[Actions]"
      }
  ],
```

In the example above, Users table will be selected.


### Generate a Mermaid ER diagram

The tool can generate a [Mermaid ER diagram](https://mermaid.js.org/syntax/entityRelationshipDiagram.html) during exectution, just set the `code-generation` option `generate-mermaid-diagram` to `true` and a `dbdiagram.md` file will be created in the output folder.

### Updating the tool

```bash
dotnet tool update ErikEJ.EFCorePowerTools.Cli -g --version 8.*
```

[Release notes](https://github.com/ErikEJ/EFCorePowerTools/wiki/Release-notes) - notice the `+CLI` label.
