---
title: Verwaltungsbibliotheken – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu der Bibliothek, die Sie zum Verwalten von Azure Event Hubs-Namespaces und -Entitäten über .NET verwenden können.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 431fe04461f422274697d1e91c4b56e914ce2d4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60746657"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-Verwaltungsbibliotheken

Mit den Event Hubs-Verwaltungsbibliotheken können Sie dynamisch Event Hubs-Namespaces und -Entitäten bereitstellen. Diese Dynamik ermöglicht komplexe Bereitstellungen und Messagingszenarien sowie eine programmgesteuerte Bestimmung der bereitzustellenden Entitäten. Die Bibliotheken sind derzeit für .NET verfügbar.

## <a name="supported-functionality"></a>Unterstützte Funktionen

* Erstellen, Aktualisieren und Löschen von Namespaces
* Erstellen, Aktualisieren und Löschen von Event Hubs
* Erstellen, Aktualisieren und Löschen von Consumergruppen

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung von Event Hubs-Verwaltungsbibliotheken müssen Sie zunächst eine Authentifizierung mit Azure Active Directory (AAD) durchführen. AAD erfordert, dass Sie sich als Dienstprinzipal authentifizieren. Dadurch erhalten Sie Zugriff auf Ihre Azure-Ressourcen. Informationen zum Erstellen eines Dienstprinzipals finden Sie in einem der folgenden Artikel:  

* [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md)

In diesen Tutorials erhalten Sie Werte für `AppId` (Client-ID), `TenantId` und `ClientSecret` (Authentifizierungsschlüssel). Diese werden von den Verwaltungsbibliotheken für die Authentifizierung verwendet. Für die Ressourcengruppe, für die die Ausführung erfolgen soll, müssen Sie über **Besitzer**-Berechtigungen verfügen.

## <a name="programming-pattern"></a>Muster für die Programmierung

Das Muster zum Bearbeiten einer beliebigen Event Hubs-Ressource folgt einem gemeinsamen Protokoll:

1. Abrufen eines Tokens aus AAD mithilfe der `Microsoft.IdentityModel.Clients.ActiveDirectory`-Bibliothek.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Erstellen Sie das Objekt `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Legen Sie die `CreateOrUpdate`-Parameter auf Ihre spezifischen Werte fest.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Führen Sie den Aufruf aus.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nächste Schritte
* [.NET-Verwaltungsbeispiel](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub-Referenz](/dotnet/api/Microsoft.Azure.Management.EventHub) 
