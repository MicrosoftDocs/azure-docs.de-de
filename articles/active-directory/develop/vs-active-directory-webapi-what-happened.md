---
title: Änderungen an einem WebAPI-Projekt, die vorgenommen werden, wenn Sie eine Verbindung mit Azure AD herstellen
description: Beschreibt, was mit dem WebAPI-Projekt geschieht, wenn Sie mithilfe von Visual Studio eine Verbindung mit Azure AD herstellen.
services: active-directory
author: ghogen
manager: douge
ms.assetid: 57630aee-26a2-4326-9dbb-ea2a66daa8b0
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f4946251cf72d7869ec5fc2f0fd844b9c06ac34
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353277"
---
# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Was ist mit dem WebAPI-Projekt passiert (verbundener Visual Studio-Dienst für Azure Active Directory)?

> [!div class="op_single_selector"]
> - [Erste Schritte](vs-active-directory-webapi-getting-started.md)
> - [Was ist passiert?](vs-active-directory-webapi-what-happened.md)

In diesem Artikel werden die genauen Änderungen beschrieben, die an ASP.NET-WebAPI-, ASP.NET-Single-Page-Webanwendungs- und ASP.NET-Azure-API-Projekten vorgenommen werden, wenn Sie den [verbundenen Azure Active Directory-Dienst mithilfe von Visual Studio](vs-active-directory-add-connected-service.md) hinzufügen. Die Informationen in diesem Artikel gelten auch für ASP.NET-Azure Mobile Service-Projekte in Visual Studio 2015.

Informationen zur Verwendung des verbundenen Diensts finden Sie unter [Erste Schritte](vs-active-directory-webapi-getting-started.md).

## <a name="added-references"></a>Hinzugefügte Verweise

Betrifft die Projektdatei (\*.NET-Verweise) und `packages.config` (NuGet-Verweise).

| Type | Verweis |
| --- | --- |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.ActiveDirectory |
| .NET; NuGet | Microsoft.Owin.Security.Jwt |
| .NET; NuGet | Microsoft.Owin.Security.OAuth |
| .NET; NuGet | Owin |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |

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
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms<br>(nur Visual Studio 2015) |
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
        <add key="ida:Tenant" value="<your selected Azure domain>" />
        <add key="ida:Audience" value="<your selected domain + / + project name>" />
    </appSettings>
    ```

- Nur Visual Studio 2017: Der folgende Eintrag wird zudem unter `<appSettings>` hinzugefügt.

    ```xml
    <add key="ida:MetadataAddress" value="<domain URL + /federationmetadata/2007-06/federationmetadata.xml>" />
    ```

- `<dependentAssembly>`-Elemente werden unter dem Knoten `<runtime><assemblyBinding>` für `System.IdentityModel.Tokens.Jwt` hinzugefügt.

- Wenn Sie die Option **Verzeichnisdaten lesen** ausgewählt haben, wird der folgende Konfigurationseintrag unter `<appSettings>` hinzugefügt:

    ```xml
    <add key="ida:Password" value="<Your Azure AD app's new password>" />
    ```

## <a name="code-changes-and-additions"></a>Änderungen und Ergänzungen des Codes

- Das Attribut `[Authorize]` wird `Controllers/ValueController.cs` und allen anderen vorhandenen Controllern hinzugefügt.

- Ein Authentifizierungsstartklasse `App_Start/Startup.Auth.cs`, die Startlogik für die Azure AD-Authentifizierung enthält, wird hinzugefügt oder entsprechend geändert. Wenn Sie die Option **Verzeichnisdaten lesen** ausgewählt haben, enthält diese Datei auch Code, mit dem ein OAuth-Code empfangen und gegen ein Zugriffstoken ausgetauscht wird.

- (Nur Visual Studio 2015 mit ASP.NET 4-App) `App_Start/IdentityConfig.cs` wird entfernt, und `Controllers/AccountController.cs`, `Models/IdentityModel.cs` und `Providers/ApplicationAuthProvider.cs` werden hinzugefügt.

- Eine Datei `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) oder `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015) wird hinzugefügt. Diese Datei enthält Informationen, mit denen Visual Studio die Hinzufügung des verbundenen Diensts nachverfolgt.

### <a name="file-backup-visual-studio-2015"></a>Dateisicherung (Visual Studio 2015)

Wenn Sie den verbundenen Dienst hinzufügen, sichert Visual Studio 2015 geänderte und entfernte Dateien. Alle betroffenen Dateien werden im Ordner `Backup/AzureAD` gespeichert. Visual Studio 2017 erstellt keine Sicherungen.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ApplicationOAuthProvider.cs`

## <a name="changes-on-azure"></a>Änderungen in Azure

- Eine Azure AD-Anwendung wurde in der Domäne erstellt, die Sie beim Hinzufügen des verbundenen Diensts ausgewählt haben.
- Die App wurde aktualisiert, damit sie die Berechtigung **Verzeichnisdaten lesen** enthält, sofern diese Option ausgewählt wurde.

[Weitere Informationen zu Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierungsszenarien für Azure Active Directory](authentication-scenarios.md)
- [Hinzufügen von „Mit Microsoft anmelden“ zu einer ASP.NET-Web-App](quickstart-v1-aspnet-webapp.md)
