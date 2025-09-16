# PdfTemplar Client SDK

[![Build and Publish NuGet Package](https://github.com/simplify9/SW-PdfTemplar-Client/actions/workflows/nuget-publish.yml/badge.svg)](https://github.com/simplify9/SW-PdfTemplar-Client/actions/workflows/nuget-publish.yml)
[![NuGet](https://img.shields.io/nuget/v/SimplyWorks.PdfTemplarClient.svg)](https://www.nuget.org/packages/SimplyWorks.PdfTemplarClient/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A .NET client SDK for PdfTemplar - a service that generates PDF documents from templates and data objects.

## Features

- Generate PDF documents by sending templates and data objects to PdfTemplar service
- Configurable JSON naming strategies (SnakeCase, CamelCase, CapitalCase)
- Comprehensive error handling with specific error codes
- Built-in HTTP client integration with `IHttpClientFactory`
- .NET 8.0 target framework

## Installation

Install via NuGet Package Manager:

```bash
dotnet add package SimplyWorks.PdfTemplarClient
```

Or via Package Manager Console:

```
Install-Package SimplyWorks.PdfTemplarClient
```

## Usage

### Basic Setup

```csharp
using Microsoft.Extensions.DependencyInjection;
using SW.PdfTemplar.Client;

// Configure services
services.AddHttpClient();

// Configure PdfTemplar settings
var settings = new PdfTemplarClientSettings
{
    ServiceUri = "https://pdftemplar.sf9.io/", // Default service URI
    NamingStrategy = NamingStrategy.CamelCase   // JSON naming strategy
};

// Create client instance
var pdfTemplar = new PdfTemplar(settings, httpClientFactory);
```

### Generate PDF Document

```csharp
// Define your data model
var model = new
{
    CustomerName = "John Doe",
    OrderDate = DateTime.Now,
    Items = new[]
    {
        new { Product = "Widget A", Quantity = 2, Price = 10.00 },
        new { Product = "Widget B", Quantity = 1, Price = 15.00 }
    }
};

try
{
    // Generate PDF document
    FileData pdfFile = await pdfTemplar.GetDocument("invoice-template", model);
    
    // Use the generated PDF
    string fileName = pdfFile.FileName;        // "generatedPDF.pdf"
    string mimeType = pdfFile.MimeType;        // "application/pdf"
    byte[] pdfContent = pdfFile.InlineData;    // PDF file bytes
}
catch (PdfTemplarException ex)
{
    // Handle specific PDF generation errors
    switch (ex.Error)
    {
        case PdfTemplarError.TemplateInvalid:
            // Handle invalid template
            break;
        case PdfTemplarError.ServerError:
            // Handle server error
            break;
        case PdfTemplarError.Timeout:
            // Handle timeout
            break;
        case PdfTemplarError.GatewayError:
            // Handle gateway error
            break;
    }
}
```

## Configuration

### PdfTemplarClientSettings

| Property | Type | Default | Description |
|----------|------|---------|-------------|
| `ServiceUri` | `string` | `"https://pdftemplar.sf9.io/"` | The base URI of the PdfTemplar service |
| `NamingStrategy` | `NamingStrategy` | `CapitalCase` | JSON serialization naming strategy |

### Naming Strategies

- **`CapitalCase`** - PascalCase property names (default)
- **`CamelCase`** - camelCase property names  
- **`SnakeCase`** - snake_case property names

## Error Handling

The SDK provides specific error codes through `PdfTemplarException`:

| Error Code | Description |
|------------|-------------|
| `TemplateInvalid` | The provided template is invalid or not found |
| `ServerError` | Internal server error occurred |
| `Timeout` | Request timed out |
| `GatewayError` | Gateway error (service may be restarting) |

## Models

### FileData
Represents the generated PDF document:
```csharp
public class FileData
{
    public string MimeType { get; set; }    // "application/pdf"
    public byte[] InlineData { get; set; }  // PDF file content
    public string FileName { get; set; }    // File name
}
```

## Dependencies

- **Microsoft.Extensions.Http** (8.0.1) - HTTP client factory
- **Newtonsoft.Json** (13.0.4) - JSON serialization
- **System.Configuration.ConfigurationManager** (8.0.1) - Configuration management
- **System.Text.Encoding** (4.3.0) - Text encoding support

## Requirements

- .NET 8.0 or later
- Access to PdfTemplar service

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

This is an open source project by [Simplify9](https://github.com/simplify9). Contributions are welcome!

## Support

For issues and questions, please use the [GitHub Issues](https://github.com/simplify9/SW-PdfTemplar-Client/issues) page.