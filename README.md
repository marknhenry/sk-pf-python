# DotNetStarter

[![Mark Henry](https://img.shields.io/static/v1?label=Author&message=Mark%20Henry&color=success)](https://www.linkedin.com/in/marknhenry/) 
[![License](https://img.shields.io/static/v1?label=License&message=MIT&color=blue)](https://www.linkedin.com/in/marknhenry/)

## To Setup A New Project
```
dotnet new console -o ProjectName

dotnet add package Microsoft.SemanticKernel --version 1.7.0
dotnet add package Microsoft.Extensions.Configuration --version 8
dotnet add package Microsoft.Extensions.Configuration.Json
dotnet add package Microsoft.SemanticKernel.Plugins.Core --version 1.7.0-alpha
```

Add this line at the end of ```.gitignore```
```
.mono
```

In the Project Directory, add a file called ```appsettings.json``` with the following: 

``` 
{
    "AzureOAIEndpoint": "https://eastus.api.cognitive.microsoft.com/",
    "AzureOAIKey": "",
    "AzureOAIResourceName": "mh-gpt-35-turbo-16k", 
    "AzureOAIDeploymentName": "gpt-35-turbo-16k"
}
```

In the ```.csproj``` file created with the dotnet app, add the following exception for debugging:

```
  <ItemGroup>
    <None Update="appsettings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```

In ```Program.cs```, add the following code: 

```
using System.Text;
using System.Text.Json;
using Microsoft.SemanticKernel;
using Microsoft.SemanticKernel.Plugins.Core;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.Json;
using Azure;
using Microsoft.SemanticKernel.ChatCompletion;

Console.WriteLine("Hello, World!");

// Build a config object and retrieve user settings.
IConfiguration config = new ConfigurationBuilder()
    // .SetBasePath(System.AppContext.BaseDirectory)
    .AddJsonFile("appsettings.json")
    .Build();

string? oaiEndpoint = config["AzureOAIEndpoint"];
string? oaiKey = config["AzureOAIKey"];
string? oaiResourceName = config["AzureOAIResourceName"];
string? oaiDeploymentName = config["AzureOAIDeploymentName"];

var builder = Kernel.CreateBuilder();

builder.Services.AddAzureOpenAIChatCompletion(
    oaiResourceName,
    oaiEndpoint,
    oaiKey,
    oaiDeploymentName);

var kernel = builder.Build();

// Call Method to test OpenAI
await testOpenAI(kernel);

async Task testOpenAI(Kernel kernel)
{
    var result = await kernel.InvokePromptAsync("Who are you?");

    Console.WriteLine(result);
}
```
