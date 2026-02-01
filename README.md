# az400-github-pkgconsumer

- Repo hosting the lab "Stage-5-19"
   which consumes the package created in lab Stage-5-18
   hosted on upstream repo "rkj-devops-labs-1/az400-github-repo"

- The commands that created the dotnet solution

```bash
# Create a New Project (Consumer)
dotnet new console -n Az400.Consumer.App
cd Az400.Consumer.App
```

-  Add nuget.config

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
   <packageSources>
      <add key="github" value="https://nuget.pkg.github.com/<OWNER>/index.json" />
      <add key="nuget" value="https://api.nuget.org/v3/index.json" />
   </packageSources>
</configuration>
```

> .
> **✏️[!NOTE]**
> Replace <OWNER> with:
> - Your GitHub username or
> - Organization name
> '

- Authentication Options

   - use `GITHUB_TOKEN` for GitHub Actions workflow

   - use PAT for Local Development
      - pat_for_stage-5-19_lab_on_package_consumer
      - `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`

```bash
# Never commit PATs
# Replace <GITHUB_USERNAME>, <OWNER> and <PAT>

dotnet nuget add source \
   --username <YOUR_GITHUB_USERNAME> \
   --password <YOUR_PAT_WITH_read:packages> \
   --store-password-in-clear-text \
   --name github \
   https://nuget.pkg.github.com/<OWNER>/index.json

# eg, https://nuget.pkg.github.com/rkj-devops-labs-1/index.json
# Runs once; stores credentials locally.
# Ensures dotnet add package works for Az400.Demo.Library .
```

- Add NuGet Package

   `dotnet add package Az400.Demo.Library --version 1.0.1`

- Reference the Library in Program.cs

```cs
using Az400.Demo.Library;
Console.WriteLine("Using GitHub Packages!");
```

- Add Restore + Build Workflow

```yaml
# .github/workflows/ci-consumer.yml
name: Consumer CI

on:
  push:
    branches:
      - main

permissions:
  contents: read
  packages: read

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.0.x'

      - name: Restore
        run: dotnet restore

      - name: Build
        run: dotnet build --no-restore
```

- Push to GitHub

```bash
git add .
git commit -m "Consume GitHub NuGet package"
git push origin main
```
