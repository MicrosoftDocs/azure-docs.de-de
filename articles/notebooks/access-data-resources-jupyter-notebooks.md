---
title: Zugriff auf Datenressourcen aus Jupyter-Notebooks auf Azure
description: Zugriff auf Dateien, REST-APIs, Datenbanken und verschiedene Ressourcen von Azure Storage aus einem Jupyter-Notebook.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 14a4191612a5d42836ae4be3ff902ca47a6b06d4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60634341"
---
# <a name="access-cloud-data-in-a-notebook"></a>Zugreifen auf Clouddaten in einem Notebook

Für interessante Arbeiten in einem Jupyter-Notebook benötigen Sie Daten. Daten bilden wirklich die Lebensader von Notebooks.

Natürlich können Sie [Datendateien in ein Projekt importieren](work-with-project-data-files.md), selbst mit Befehlen wie `curl`, entweder aus einem Notebook oder durch direktes Herunterladen. Es ist aber wahrscheinlich, dass Sie mit sehr viel umfangreicheren Daten arbeiten müssen, die aus nicht dateibasierten Quellen stammen, wie etwa REST-APIs, relationalen Datenbanken und Cloudspeicher wie Azure-Tabellen.

Dieser Artikel beschreibt kurz diese verschiedenen Optionen. Da sich Datenzugriff besonders gut in der Praxis verstehen lässt, finden Sie in den [Azure Notebooks Samples - Access your data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb) (Beispiele für Azure Notebooks – Zugriff auf Ihre Daten) ausführbaren Code.

## <a name="rest-apis"></a>REST-APIs

Im Allgemeinen erfolgt der Zugriff auf die riesigen Datenmengen im Internet nicht in Form von Dateien, sondern mithilfe von REST-APIs. Da eine Notebookzelle jeden beliebigen Code enthalten kann, können Sie glücklicherweise Code verwenden, um Anforderungen zu senden und JSON-Daten zu empfangen. Anschließend können Sie dieses JSON in jedes gewünschte Format konvertieren, wie etwa in Pandas Dataframe.

Um auf Daten mithilfe einer REST-API zuzugreifen, verwenden Sie den gleichen Code, den Sie in anderen Anwendungen verwenden, in den Codezellen eines Notebooks. Unter Verwendung der Anforderungsbibliothek ist dies die allgemeine Struktur:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL-Datenbanken

Mithilfe der Bibliotheken pyodbc oder pymssql können Sie auf SQL Server-Datenbanken zugreifen.

In [Abfragen einer Azure SQL-Datenbank mithilfe von Python](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) finden Sie Anweisungen zum Erstellen einer Datenbank, die AdventureWorks-Daten enthält, und zum Abfragen dieser Daten. Der gleiche Code wird im Beispielnotebook für diesen Artikel verwendet.

## <a name="azure-storage"></a>Azure Storage

Azure Storage bietet verschiedene Arten von nicht relationalem Speicher, abhängig von der Art Ihrer Daten und des gewünschten Zugriffs darauf:

- Table Storage: bietet kostengünstigen Speicher mit großem Volumen für Tabellendaten, wie etwa gesammelte Sensorprotokolle, Diagnoseprotokolle usw.
- Blob-Speicher: bietet dateiähnliche Speicherung beliebiger Datentypen.

Das Beispielnotebook veranschaulicht das Arbeiten mit beiden, Tabellen und Blobs, einschließlich der Verwendung einer Signatur für freigegebenen Zugriff, um den schreibgeschützten Zugriff auf Blobs zu ermöglichen.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB bietet einen vollständig indizierten NoSQL-Speicher für JSON-Dokumente). In den folgenden Artikeln lernen Sie eine Reihe verschiedener Weisen für das Arbeiten mit Cosmos DB in Python kennen:

- [Build a SQL API app with Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python) (Erstellen einer SQL API-App mit Python)
- [Erstellen einer Flask-App mit der Azure Cosmos DB-API für MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Create a graph database using Python and the Gremlin API](https://docs.microsoft.com/azure/cosmos-db/create-graph-python) (Erstellen einer Graphdatenbank mit Python und der Gremlin-API)
- [Build a Cassandra app with Python and Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python) (Erstellen einer Cassandra-App mit Python und Azure Cosmos DB)
- [Build a Table API app with Python and Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python) (Erstellen einer Tabellen-API-App mit Python und Azure Cosmos DB)

Bei der Arbeit mit Cosmos DB können Sie die [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/)-Bibliothek verwenden.

## <a name="other-azure-databases"></a>Andere Azure-Datenbanken

Azure bietet eine Reihe weiterer Datenbanktypen, die Sie verwenden können. Die Artikel unten bieten Hilfestellung beim Zugriff auf diese Datenbanken aus Python:

- [Azure Database for PostgreSQL: Use Python to connect and query data](https://docs.microsoft.com/azure/postgresql/connect-python) (Verwenden von Python zum Herstellen einer Verbindung und Abfragen von Daten)
- [Schnellstart: Verwenden von Azure Redis Cache mit Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: Use Python to connect and query data](https://docs.microsoft.com/azure/mysql/connect-python) (Verwenden von Python zum Herstellen einer Verbindung und Abfragen von Daten)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory – Kopier-Assistent](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Arbeiten mit Projektdatendateien](work-with-project-data-files.md)
