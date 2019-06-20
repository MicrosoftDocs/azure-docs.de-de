---
title: 'Herstellen einer Verbindung mit verschiedenen Datenquellen aus Azure Databricks '
description: Erfahren Sie, wie Sie eine Verbindung mit verschiedenen Datenquellen aus Azure Databricks herstellen.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: f2b7136ec21416e31c2af658974577023a4494de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240348"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Verbinden mit Datenquellen aus Azure Databricks

Dieser Artikel enthält Links zu den verschiedenen Datenquellen in Azure, die mit Azure Databricks verbunden werden können. Folgen Sie den Beispielen in diesen Links, um Daten aus den Azure-Datenquellen (wie z.B. Azure Blob Storage oder Azure Event Hubs) in einen Azure Databricks-Cluster zu extrahieren und analytische Aufträge für diese auszuführen. 

## <a name="prerequisites"></a>Voraussetzungen

* Sie benötigen einen Azure Databricks-Arbeitsbereich und einen Spark-Cluster. Folgen Sie den Anweisungen unter [Schnellstart: Ausführen eines Spark-Auftrags in Azure Databricks mit dem Azure-Portal](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Datenquellen für Azure Databricks

Die folgende Liste enthält die Datenquellen in Azure, die Sie mit Azure Databricks verwenden können. Eine vollständige Liste der Datenquellen, die mit Azure Databricks verwendet werden können, finden Sie unter [Datenquellen für Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Azure SQL-Datenbank](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Dieser Link bietet die DataFrame-API für die Verbindung mit SQL-Datenbanken über JDBC und Informationen dazu, wie die Parallelität von Lesezugriffen über die JDBC-Schnittstelle gesteuert wird. Dieses Thema enthält detaillierte Beispiele unter Verwendung der Scala-API, mit abgekürzten Python- und Spark-SQL-Beispielen am Ende.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Dieser Link enthält Beispiele für die Verwendung des Azure Active Directory-Dienstprinzipals zur Authentifizierung bei Data Lake Store. Es enthält auch Anweisungen, wie aus Azure Databricks auf die Daten in Data Lake Store zugegriffen werden kann.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Dieser Link enthält Beispiele für den direkten Zugriff aus Azure Databricks auf Azure Blob Storage mithilfe des Zugriffsschlüssels oder des SAS für einen bestimmten Container. Zudem bietet der Link Informationen darüber, wie Sie aus Azure Databricks über die RDD-API auf Azure Blob Storage zugreifen können.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Dieser Link enthält Anweisungen zur Verwendung des [Azure Cosmos DB-Spark-Connectors](https://github.com/Azure/azure-cosmosdb-spark) aus Azure Databricks für den Zugriff auf Daten in Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Dieser Link enthält Anweisungen zur Verwendung des [Azure Event Hubs-Spark-Connectors](https://github.com/Azure/azure-event-hubs-spark) aus Azure Databricks für den Zugriff auf Daten in Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Dieser Link enthält Anweisungen, wie über den Azure SQL Data Warehouse-Connector eine Verbindung aus Azure Databricks hergestellt werden kann.
    

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Quellen, aus denen Sie Daten in Azure Databricks importieren können, finden Sie im Artikel zu [Datenquellen für Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


