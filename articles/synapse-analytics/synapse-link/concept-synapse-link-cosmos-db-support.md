---
title: Von Azure Synapse Link (Vorschau) unterstützte Features für Azure Cosmos DB
description: Aktuelle Liste der Aktionen, die von Azure Synapse Link für Azure Cosmos DB unterstützt werden
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 3d12e60e2aeee667b89d56589870d53366772014
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459037"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Von Azure Synapse Link unterstützte Features für Azure Cosmos DB

In diesem Artikel werden die Funktionen beschrieben, die derzeit in Azure Synapse Link für Azure Cosmos DB unterstützt werden.

## <a name="azure-synapse-support"></a>Azure Synapse-Unterstützung

In Azure Cosmos DB gibt es zwei Arten von Containern:
* HTAP-Container: ein Container mit Synapse Link-Aktivierung. Dieser Container verfügt sowohl über Transaktionsspeicher als auch über Analysespeicher. 
* OLTP-Container: Ein Container mit nicht aktiviertem Synaspe Link. Dieser Container verfügt nur über Transaktionsspeicher und nicht über Analysespeicher.

> [!IMPORTANT]
> Azure Synapse Link für Azure Cosmos DB wird derzeit für Synapse-Arbeitsbereiche unterstützt, in denen kein verwaltetes virtuelles Netzwerk aktiviert ist. 

Sie können keine Verbindung mit einem Azure Cosmos DB-Container herstellen, ohne Synapse Link zu aktivieren. In diesem Szenario können Sie nur den Transaktionsspeicher lesen bzw. darin schreiben. Nachfolgend sehen Sie eine Liste der derzeit unterstützten Features in Synapse Link für Azure Cosmos DB. 

| Kategorie              | BESCHREIBUNG |[Apache Spark-Pool](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Serverloser SQL-Pool](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Laufzeitunterstützung** |Unterstützte Azure Synapse-Runtime für den Zugriff auf Azure Cosmos DB| ✓ | Vorschau |
| **Azure Cosmos DB-API-Unterstützung** | Unterstützte Azure Cosmos DB-API-Art | SQL/MongoDB | SQL/MongoDB |
| **Object**  |Objekte wie eine Tabelle, die erstellt werden können und direkt auf den Azure Cosmos DB-Container verweisen| Datenrahmen, Ansicht, Tabelle | Sicht |
| **Lesen**    | Typ des Azure Cosmos DB-Containers, der gelesen werden kann | OLTP/HTAP | HTAP  |
| **Schreiben**   | Kann die Azure Synapse-Runtime verwendet werden, um Daten in einen Azure Cosmos DB-Container zu schreiben? | Ja | Nein |

* Wenn Sie Daten aus Spark in einen Azure Cosmos DB-Container schreiben, erfolgt dieser Vorgang über den Transaktionsspeicher von Azure Cosmos DB. Dieser Vorgang wirkt sich durch den Verbrauch von Anforderungseinheiten auf die Transaktionsleistung von Azure Cosmos DB aus.
* Die Integration mit dedizierten SQL-Pools über externe Tabellen wird derzeit nicht unterstützt.
 
## <a name="supported-code-generated-actions-for-spark"></a>Unterstützte, durch Code generierte Aktionen für Spark

| Geste              | BESCHREIBUNG |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **In Datenrahmen laden** |Laden und Lesen von Daten in einen Spark-Datenrahmen |✓| ✓ |
| **Spark-Tabelle erstellen** |Erstellen einer Tabelle, die auf einen Azure Cosmos DB-Container verweist|✓| ✓ |
| **Datenrahmen in Container schreiben** |Schreiben von Dateien in einen Container|✓| ✓ |
| **Streamingdatenrahmen aus Container laden** |Streamen von Daten mithilfe des Azure Cosmos DB-Änderungsfeeds|✓| ✓ |
| **Streamingdatenrahmen in Container schreiben** |Streamen von Daten mithilfe des Azure Cosmos DB-Änderungsfeeds|✓| ✓ |


## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Unterstützte durch Code generierte Aktionen für serverlosen SQL-Pool

| Geste              | BESCHREIBUNG |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Durchsuchen von Daten** |Durchsuchen von Daten aus einem Container mit vertrauter T-SQL-Syntax und automatischem Schemarückschluss|X| ✓ |
| **Erstellen von Sichten und Erstellen von BI-Berichten** |Erstellen einer SQL-Sicht für den direkten Zugriff auf einen Container für BI über serverlosen SQL-Pool |X| ✓ |
| **Verknüpfen von unterschiedlichen Datenquellen mit Cosmos DB-Daten** | Speichern von Abfrageergebnissen beim Lesen von Daten aus Cosmos DB-Containern zusammen mit Daten in Azure Blob Storage oder Azure Data Lake Storage mithilfe von CETAS |X| ✓ |

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zum Herstellen einer Verbindung mit Synapse Link für Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Informationen zum Abfragen des Analysespeichers von Cosmos DB mit Spark](how-to-query-analytical-store-spark.md)
