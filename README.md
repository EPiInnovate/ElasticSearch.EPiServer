# Elasticsearch Plugin for Optimizely/EPiServer .NET Integration


## Introduction

EPiInnovate ElasticSearch EPiServer is a advanced plugin designed for .NET Core that extends the search capabilities of the EPiServer (now Optimizely) CMS and Commerce platforms by integrating with ElasticSearch. The plugin integrates ElasticSearch, which, similar to EpiServer Find, is built on the Lucene engine. This integration results in a more powerful and swift search capability within the EPiServer platform. For more information, visit www.epiinnovate.com.

## Requirements

* .NET 5.0 or higher (including .NET 6.0, .NET 7.0+)
* EPiServer CMS version 12.2.0 or higher
* Elasticsearch version 5.6 or higher
* Ingest Attachment Processor Plugin for Elasticsearch

## Installation

Install EPiInnovate Elasticsearch EPiServer CMS in Visual Studio or at the command line with the NuGet Package Manager. In Visual Studio, navigate to the console with:

   **Tools** ->
   **NuGet Package Manager** ->
   **Package Manager Console**

```csharp
 PM > Install-Package EPiInnovate.ElasticSearch.EPiServer.CMS
```
...or

   **Tools** ->
   **NuGet Package Manager** ->
   **Manage NuGet Packages for Solutions** ->
   **Search: EPiInnovate.ElasticSearch.EPiServer.CMS**

![Tools](assets/EpiElasticSearch-Nuget.png?raw)

## Configure ElasticSearch

To set up Elasticsearch correctly, please adhere to the following guidance:

* Install the latest version of Elasticsearch (minimum supported version 5.6).
* Configure settings to match your environment's needs.
* Define user roles and configure heap size when running as a service.
* Install the Ingest Attachment Processor Plugin for file indexing.
* Refer to the official documentation for detailed setup instructions.

## Configure Your Project for .NET Core

Install the following Nuget packages in your project from Nuget.org:

* EPiInnovate.ElasticSearch.EPiServer.Core
* EPiInnovate.ElasticSearch.EPiServer.CMS
* EPiInnovate.ElasticSearch.EPiServer.Commerce (currently under development)

To integrate EPiInnovate.ElasticSearch.EPiServer with your .NET Core application, configure the necessary services within the Startup.cs file. This process involves registering the service so that it is available for your application to use. Below is a step-by-step guide for adding this configuration to your Startup.cs file.

1. Within the `ConfigureServices` method, you must register the ElasticSearch services using the `AddEpiElasticSearch()` extension method.
2. In the `Configure` method, you must invoke the `UseEpiElasticSearch()` to initialize the ElasticSearch service.

Add the following C# code snippets to the respective methods in the `Startup.cs` file of your .NET Core application:

```csharp
using EPiInnovate.ElasticSearch.EPiServer.CMS.Extensions;

public void ConfigureServices(IServiceCollection services)
{
    // Register EpiElasticSearch services
    services.AddEpiElasticSearch();
}

public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    // Initialize EpiElasticSearch
    app.UseEpiElasticSearch();
}
```

## Configure .NET Core appsettings.json

```json
    "EpiElasticSearch": {
      "License": "" 
      "Host": "http://localhost:9200",
      "BulkSize": 1000,
      "ProviderMaxResults": 100,
      "CloseIndexDelay": 500,
      "IgnoreXhtmlStringContentFragments": false,
      "ClientTimeoutSeconds": 100,
      "Username": "",
      "Password": "",
      "Shards": 5,
      "Replicas": 1,
      "TrackingConnectionStringName": "EPiServerDB",
      "Indices": [
        {
          "Name": "my-index-name",
          "DisplayName": "My Index",
          "SynonymsFile": "",
          "Default": true
        }
      ],
      "Files": {
        "MaxSize": "50MB",
        "Enabled": true,
        "Extensions": [
          "doc",
          "txt"
        ]
      }
    }
```

The only essential configuration parameter is `Host`; the others are optional and pre-populated with default values as specified:

Configuration Parameters:

- `License`: Replace with your demo or purchased license key from www.epiinnovate.com
- `Host`: The address and port for the Elasticsearch server.
- `BulkSize`: Specifies the batch size for bulk document updates.
- `ProviderMaxResults`: Determines the number of search results displayed in the UI.
- `CloseIndexDelay`: Time in milliseconds to wait between index open/close actions. Slower servers may require a longer delay.
- `IgnoreXhtmlStringContentFragments`: Indicates whether to disregard fragments in XhtmlStrings.
- `ClientTimeoutSeconds`: The HttpClient timeout duration in seconds.
- `Username`: For basic authentication, the username.
- `Password`: For basic authentication, the password.
- `Shards`: The default shard count for new indices.
- `Replicas`: The default replica count for new indices.
- `TrackingConnectionStringName`: The identifier for the SQL connection string used for activity tracking.

In the `Indices` array, define your indices, typically one is sufficient. If multiple are used, the primary index for IContent should be identified by setting `Default` to `true`.

Example:

```json
    {
    "Name": "my-index-name",
    "DisplayName": "My Index",
    "SynonymsFile": "synonyms/mysynonyms.txt"
    }
```

- `Name`: The actual name of the index, which will be appended with a language code for each supported language.
- `DisplayName`: The friendly name of the index, which appears in user-facing scenarios.
- `SynonymsFile`: An optional setting for specifying a path to a pre-existing synonyms file relative to the server's config-folder. This file should be available upon index creation and replicated across all cluster nodes, named with the language code as a prefix. For example, if Norwegian and Swedish are active languages, `SynonymsFile` set to `synonyms/mysynonyms.txt` would require `no_mysynonyms.txt` and `se_mysynonyms.txt` to be in the config-folder.

Example:

```json
"Files": {
  "Enabled": true,
  "MaxSize": "Size limit in bytes or with suffix 'KB', 'MB', or 'GB'",
  "Extensions": ["Array of file extensions to be indexed"]
}
```

- `Enabled`: A boolean indicating whether file indexing is active.
- `MaxSize`: The maximum file size allowed for indexing, specified in bytes or with a size suffix.
- `Extensions`: A list of file extensions that should be indexed.

## Create the "ElasticsearchAdmins" Group

You must create a group named `ElasticsearchAdmins` in Optimizely/Episerver which will be used for [explain purpose of the group briefly].

To create the group, follow these steps:

1. Log in to your Optimizely/Episerver CMS.
2. Navigate to the admin panel.
3. Go to the "Groups" section.
4. Click on "Create new group".
5. Name the group `ElasticsearchAdmins` and save it.

Add Users to the Group

Once the group is created, you'll need to add users to it. Any user that is supposed to work with EPiInnovate.ElasticSearch.EPiServer should be a member of this group.

Here's how you add users to the `ElasticsearchAdmins` group:

1. Still in the admin panel, find the `ElasticsearchAdmins` group you just created.
2. Click on the group to open its details.
3. Navigate to the "Users" tab within the group details.
4. Add the relevant users by selecting them and confirming the action.

After you have added the users to the group, it is important to ensure the changes are properly applied to your user account. To do this, log out of the system and then log back in. This action will register the new group memberships to your user claims, updating your access rights accordingly.
Once logged back in, you should be able to see a new menu titled “Search Engine” on your dashboard or main navigation panel. Clicking on this menu will take you to the corresponding page where you can manage settings or perform tasks related to the Elasticsearch functionality.

![Tools](assets/EPiElasticSearch-CMS.png?raw)

## License Key

Before using this package, you will need to acquire a license key. There are two types of keys available:

1. **Demo License Key**: You can obtain a demo license key by visiting [www.epiinnovate.com](https://www.epiinnovate.com). This key allows you to activate and use all the features of the package for a trial period. It's a convenient way to explore the functionalities and assess whether the package meets your requirements before making any commitment.
2. **Purchase License Key**: If the package suits your needs and you decide to use it for an extended period or for commercial purposes, you must purchase a full license key. The purchased license will ensure uninterrupted access to the package features and support.

\
\
\
For additional information and assistance, consult our documentation at [www.epiinnovate.com/docs](https://www.epiinnovate.com/docs). For support or other inquiries, contact us at [support@epiinnovate.com](mailto:support@epiinnovate.com). We are committed to providing a professional and seamless experience.
