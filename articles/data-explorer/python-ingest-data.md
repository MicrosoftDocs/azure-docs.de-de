---
title: Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python
description: In diesem Artikel erfahren Sie, wie Sie Daten mit Python in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: da23ec91891776e9a459b04c5718147427843991
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496919"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Der Azure-Daten-Explorer bietet zwei Clientbibliotheken für Python: eine [Erfassungsbibliothek](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) und eine [Datenbibliothek](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Mit diesen Bibliotheken können Sie über Ihren Code Daten in einem Cluster erfassen (laden) und Daten abfragen. In diesem Artikel erstellen Sie zunächst eine Tabelle und eine Datenzuordnung in einem Cluster. Anschließend stellen Sie die Erfassung im Cluster in eine Warteschlange und überprüfen die Ergebnisse.

Dieser Artikel ist auch als [Azure-Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

* [Ein Cluster und eine Datenbank](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) auf dem Entwicklungscomputer

## <a name="install-the-data-and-ingest-libraries"></a>Installieren der Daten- und der Erfassungsbibliothek

Installieren Sie *azure-kusto-data* und *azure-kusto-ingest*.

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Hinzufügen von Importanweisungen und Konstanten

Importieren Sie Klassen aus „azure-kusto-data“.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Für die Authentifizierung von Anwendungen verwendet der Azure-Daten-Explorer Ihre AAD-Mandanten-ID. Um Ihre Mandanten-ID zu suchen, verwenden Sie die folgende URL, und ersetzen Sie dabei *YourDomain* durch Ihre Domäne.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicken Sie auf diese URL, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Die Mandanten-ID ist in diesem Fall `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Legen Sie die Werte für AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI und KUSTO_DATABASE fest, bevor Sie diesen Code ausführen.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

Erstellen Sie nun die Verbindungszeichenfolge. In diesem Beispiel wird die Geräteauthentifizierung zum Zugreifen auf den Cluster verwendet. Sie können auch das [AAD-Anwendungszertifikat](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24), den [AAD-Anwendungsschlüssel](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20) und den [AAD-Benutzer mit dem zugehörigen Kennwort](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34) verwenden.

Sie erstellen die Zieltabelle und die Zuordnung in einem späteren Schritt.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Festlegen der Informationen zur Quelldatei

Importieren Sie zusätzliche Klassen, und legen Sie Konstanten für die Datenquellendatei fest. In diesem Beispiel wird eine Beispieldatei verwendet, die in Azure Blob Storage gehostet wird. Das **StormEvents**-Beispieldataset enthält wetterbezogene Daten der [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>Erstellen einer Tabelle im Cluster

Erstellen Sie eine Tabelle, die dem Schema der Daten in der Datei „StormEvents.csv“ entspricht. Wenn dieser Code ausgeführt wird, wird eine Meldung wie die folgende zurückgegeben: *Verwenden Sie zur Anmeldung einen Webbrowser, um die Seite https://microsoft.com/devicelogin zu öffnen. Geben Sie dann zur Authentifizierung den Code F3W4VWZDM ein*. Befolgen Sie die Schritte für die Anmeldung, und kehren Sie dann zurück, um den Codeblock auszuführen. Für nachfolgende Codeblöcke, die eine Verbindung herstellen, müssen Sie sich wieder anmelden.

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>Definieren der Erfassungszuordnung

Ordnen Sie eingehende JSON-Daten den beim Erstellen der Tabelle verwendeten Spaltennamen und Datentypen zu. Dadurch werden Quelldatenfelder zu Zieltabellenspalten zugeordnet.

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>Stellen einer Nachricht in eine Warteschlange für die Erfassung

Stellen Sie eine Nachricht zum Abrufen von Daten aus Blob Storage in eine Warteschlange, und erfassen Sie diese Daten im Azure-Daten-Explorer.

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference = DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)  # FILE_SIZE is the raw size of the data in bytes
INGESTION_CLIENT.ingest_from_blob(BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')

```

## <a name="query-data-that-was-ingested-into-the-table"></a>Abfragen von Daten, die in der Tabelle erfasst wurden

Warten Sie fünf bis zehn Minuten, bis die Warteschlange die Erfassung geplant und die Daten in den Azure-Daten-Explorer geladen hat. Führen Sie dann den folgenden Code aus, um die Anzahl der Datensätze in der Tabelle „StormEvents“ zu erhalten.

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>Ausführen von Abfragen zur Problembehandlung

Melden Sie sich bei [https://dataexplorer.azure.com](https://dataexplorer.azure.com) an, und stellen Sie eine Verbindung mit Ihrem Cluster her. Führen Sie den folgenden Befehl in Ihrer Datenbank aus, um festzustellen, ob in den letzten vier Stunden Erfassungsfehler aufgetreten sind. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Führen Sie den folgenden Befehl aus, um den Status aller Erfassungsvorgänge in den letzten vier Stunden anzuzeigen. Ersetzen Sie den Namen der Datenbank vor dem Ausführen.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei. Wenn dies nicht der Fall ist, führen Sie den folgenden Befehl in der Datenbank aus, um die Tabelle „StormEvents“ zu bereinigen.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

* [Abfragen von Daten mithilfe von Python](python-query-data.md)
