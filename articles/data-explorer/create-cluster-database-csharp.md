---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit C#
description: Hier erfahren Sie, wie Sie mit C# einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e51551d4ce8061122fce52b05e68e102b71c27a8
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494610"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank mit C#.

## <a name="prerequisites"></a>Voraussetzungen

* Falls Sie Visual Studio 2019 nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="install-c-nuget"></a>Installieren eines C#-NuGet-Pakets

1. Installieren Sie das [Azure Data Explorer-NuGet-Paket (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Installieren Sie das [NuGet-Paket „Microsoft.IdentityModel.Clients.ActiveDirectory“](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) für die Authentifizierung.

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Code:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | clusterName | *mykustocluster* | Der gewünschte Name Ihres Clusters.|
   | sku | *D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | resourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    Sie können auch noch weitere optionale Parameter verwenden, etwa die Kapazität des Clusters.

1. Legen Sie [Ihre Anmeldeinformationen](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet) fest.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Wenn das Ergebnis `ProvisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Code:

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | clusterName | *mykustocluster* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | databaseName | *mykustodatabase* | Der Name Ihrer Datenbank.|
   | resourceGroupName | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | softDeletePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten für Abfragen verfügbar sein sollen. |
   | hotCachePeriod | *3650:00:00:00* | Der Zeitraum, für den Daten im Cache verfügbar sein sollen. |

2. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)](net-standard-ingest-data.md)
