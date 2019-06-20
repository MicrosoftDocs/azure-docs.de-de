---
title: Änderungen an einem MVC-Projekt, die vorgenommen werden, wenn Sie eine Verbindung mit Azure AD herstellen
description: Beschreibt, was mit dem MVC-Projekt geschieht, wenn Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit Azure AD herstellen
services: active-directory
author: ghogen
manager: douge
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e40a1fd72adf353d5c4d856544482cb7bd68f43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413986"
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>Was ist mit dem MVC-Projekt passiert (verbundene Visual Studio-Dienste für Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Erste Schritte](vs-active-directory-dotnet-getting-started.md)
> - [Was ist passiert?](vs-active-directory-dotnet-what-happened.md)

In diesem Artikel werden die genauen Änderungen identifiziert, die an einem ASP.NET MVC-Projekt vorgenommen werden, wenn der [verbundene Azure Active Directory-Dienst mit Visual Studio](vs-active-directory-add-connected-service.md) hinzugefügt wird.

Informationen zur Verwendung des verbundenen Diensts finden Sie unter [Erste Schritte](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Hinzugefügte Verweise

Betrifft die Projektdatei (\*.NET-Verweise) und `packages.config` (NuGet-Verweise).

| Type | Verweis |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.Cookies |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Zusätzliche Verweise bei Auswahl der Option **Verzeichnisdaten lesen**:

| Type | Verweis |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (nur Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (nur Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

Die folgenden Verweise werden entfernt (nur ASP.NET 4-Projekte, wie in Visual Studio 2015):

| Type | Verweis |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Änderungen an der Projektdatei

- Die `IISExpressSSLPort`-Eigenschaft wird auf eine eindeutige Zahl festgelegt.
- Die `WebProject_DirectoryAccessLevelKey`-Eigenschaft wird auf 0 festgelegt, oder auf 1, wenn Sie die Option **Verzeichnisdaten lesen** ausgewählt haben.
- Die `IISUrl`-Eigenschaft wird auf `https://localhost:<port>/` festgelegt (dabei entspricht `<port>` dem Wert `IISExpressSSLPort`).

## <a name="webconfig-or-appconfig-changes"></a>Änderungen an „web.config“ oder „app.config“

- Die folgenden Konfigurationseinträge wurden hinzugefügt:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- `<dependentAssembly>`-Elemente wurden unter dem Knoten `<runtime><assemblyBinding>` für `System.IdentityModel.Tokens.Jwt` und `Microsoft.IdentityModel.Protocol.Extensions` hinzugefügt.

Zusätzliche Änderungen bei Auswahl der Option **Verzeichnisdaten lesen**:

- Der folgende Konfigurationseintrag unter `<appSettings>` wurde hinzugefügt:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Die folgenden Elemente unter `<configuration>` wurden hinzugefügt. Die Werte für „project-mdf-file“ und „project-catalog-id“ variieren:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit https://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>

    <entityFramework>
      <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
        <parameters>
          <parameter value="mssqllocaldb" />
        </parameters>
      </defaultConnectionFactory>
      <providers>
        <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
      </providers>
    </entityFramework>
    ```

- `<dependentAssembly>`-Elemente wurden unter dem Knoten `<runtime><assemblyBinding>` für `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm` und `Microsoft.Data.OData` hinzugefügt.

## <a name="code-changes-and-additions"></a>Änderungen und Ergänzungen des Codes

- Das Attribut `[Authorize]` wurde `Controllers/HomeController.cs` und allen anderen vorhandenen Controllern hinzugefügt.

- Eine Authentifizierungsstartklasse `App_Start/Startup.Auth.cs`, die Startlogik für die Azure AD-Authentifizierung enthält, wurde hinzugefügt. Wenn Sie die Option **Verzeichnisdaten lesen** ausgewählt haben, enthält diese Datei auch Code, mit dem ein OAuth-Code empfangen und gegen ein Zugriffstoken ausgetauscht wird.

- Die Controllerklasse `Controllers/AccountController.cs` mit den Methoden `SignIn` und `SignOut` wurde hinzugefügt.

- Die Teilansicht `Views/Shared/_LoginPartial.cshtml` mit einem Aktionslink für `SignIn` und `SignOut` wurde hinzugefügt.

- Die Teilansicht `Views/Account/SignoutCallback.cshtml` mit HTML-Code für die Abmeldebenutzeroberfläche wurde hinzugefügt.

- Die `Startup.Configuration`-Methode wurde aktualisiert, um einen Aufruf von `ConfigureAuth(app)` einzubinden, wenn die Klasse bereits vorhanden ist. Andernfalls wurde die `Startup`-Klasse mit einem Methodenaufruf hinzugefügt.

- Die Datei `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) oder `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) wurde hinzugefügt. Diese Datei enthält Informationen, mit denen Visual Studio die Hinzufügung des verbundenen Diensts nachverfolgt.

- Wenn Sie die Option **Verzeichnisdaten lesen** gewählt haben, wurde `Models/ADALTokenCache.cs` und `Models/ApplicationDbContext.cs` zur Unterstützung der Tokenzwischenspeicherung hinzugefügt. Außerdem wurden ein zusätzlicher Controller und eine Ansicht hinzugefügt, um den Zugriff auf Benutzerprofilinformationen mithilfe von Azure Graph-APIs zu veranschaulichen: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml` und `Views/UserProfile/Relogin.cshtml`.

### <a name="file-backup-visual-studio-2015"></a>Dateisicherung (Visual Studio 2015)

Wenn Sie den verbundenen Dienst hinzufügen, sichert Visual Studio 2015 geänderte und entfernte Dateien. Alle betroffenen Dateien werden im Ordner `Backup/AzureAD` gespeichert. Visual Studio 2017 und höher erstellt keine Sicherungen.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Änderungen in Azure

- Eine Azure AD-Anwendung wurde in der Domäne erstellt, die Sie beim Hinzufügen des verbundenen Diensts ausgewählt haben.
- Die App wurde aktualisiert, damit sie die Berechtigung **Verzeichnisdaten lesen** enthält, sofern diese Option ausgewählt wurde.

[Weitere Informationen zu Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierungsszenarien für Azure Active Directory](authentication-scenarios.md)
- [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](quickstart-v1-aspnet-webapp.md)
