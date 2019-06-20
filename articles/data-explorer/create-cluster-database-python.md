---
title: Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit Python
description: Hier erfahren Sie, wie Sie mit Python einen Azure Data Explorer-Cluster und eine Datenbank erstellen.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: caac53aec08e234f320ee4278a5a58bbc62bff68
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494577"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Erstellen eines Azure Data Explorer-Clusters und einer Datenbank mit Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Datenmengen, die von Anwendungen, Websites, IoT-Geräten usw. gestreamt werden. Um den Azure Data Explorer zu verwenden, erstellen Sie zuerst einen Cluster und anschließend eine oder mehrere Datenbanken in diesem Cluster. Anschließend erfassen (laden) Sie Daten in eine Datenbank, damit Sie diese abfragen können. In diesem Artikel erstellen Sie einen Cluster und eine Datenbank mit Python.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="install-python-package"></a>Installieren des Python-Pakets

Um das Python-Paket für Azure Data Explorer (Kusto) zu installieren, öffnen Sie eine Eingabeaufforderung, deren Pfad Python enthält: Führen Sie den folgenden Befehl aus:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Erstellen des Azure Data Explorer-Clusters

1. Erstellen Sie Ihren Cluster mit dem folgenden Befehl:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Der gewünschte Name Ihres Clusters.|
   | sku | *D13_v2* | Die SKU, die für Ihren Cluster verwendet wird. |
   | resource_group_name | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |

    Sie können auch noch weitere optionale Parameter verwenden, etwa die Kapazität des Clusters.
    
1. Legen Sie [*Ihre Anmeldeinformationen*](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) fest.

1. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr Cluster erfolgreich erstellt wurde:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Wenn das Ergebnis `provisioningState` mit dem Wert `Succeeded` enthält, wurde der Cluster erfolgreich erstellt.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Erstellen der Datenbank im Azure Data Explorer-Cluster

1. Erstellen Sie Ihre Datenbank mit dem folgenden Befehl:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Einstellung** | **Empfohlener Wert** | **Feldbeschreibung**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Der Name Ihres Clusters, in dem die Datenbank erstellt werden soll.|
   | database_name | *mykustodatabase* | Der Name Ihrer Datenbank.|
   | resource_group_name | *testrg* | Der Name der Ressourcengruppe, in der der Cluster erstellt werden soll |
   | soft_delete_period | *3650 days, 0:00:00* | Der Zeitraum, für den Daten für Abfragen verfügbar sein sollen. |
   | hot_cache_period | *3650 days, 0:00:00* | Der Zeitraum, für den Daten im Cache verfügbar sein sollen. |

1. Führen Sie den folgenden Befehl aus, um die erstellte Datenbank anzuzeigen:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Jetzt verfügen Sie über einen Cluster und eine Datenbank.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

* Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei.
* Löschen Sie den Cluster, um Ressourcen zu bereinigen. Wenn Sie einen Cluster löschen, werden auch alle darin enthaltenen Datenbanken gelöscht. Verwenden Sie den folgenden Befehl, um Ihren Cluster zu löschen:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)
