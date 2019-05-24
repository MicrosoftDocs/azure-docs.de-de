---
title: 'Schnellstart: Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Node'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mit Node.js Daten in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: 42a4d2e34eab3679f98d5a62099ab57e5924fcc3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800418"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Schnellstart: Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Node

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Data Explorer bietet zwei Clientbibliotheken für Node: eine [Erfassungsbibliothek](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) und eine [Datenbibliothek](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Mit diesen Bibliotheken können Sie über Ihren Code Daten in einem Cluster erfassen (laden) und Daten abfragen. In diesem Schnellstart erstellen Sie zunächst eine Tabelle und eine Datenzuordnung in einem Testcluster. Anschließend stellen Sie die Erfassung im Cluster in eine Warteschlange und überprüfen die Ergebnisse.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zusätzlich zu einem Azure-Abonnement benötigen Sie zum Abschließen dieses Schnellstarts Folgendes:

* [Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) auf dem Entwicklungscomputer

## <a name="install-the-data-and-ingest-libraries"></a>Installieren der Daten- und der Erfassungsbibliothek

Installieren Sie *azure-kusto-ingest* und *azure-kusto-data*.

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Hinzufügen von Importanweisungen und Konstanten

Importieren Sie Klassen aus den Bibliotheken.

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Für die Authentifizierung von Anwendungen verwendet Azure Data Explorer Ihre Azure Active Directory-Mandanten-ID. Informationen zum Ermitteln Ihrer Mandanten-ID finden Sie unter [Find your Office 365 tenant ID](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id) (Ermitteln Ihrer Office 365-Mandanten-ID).

Legen Sie vor dem Ausführen dieses Codes die Werte für `authorityId`, `kustoUri`, `kustoIngestUri` und `kustoDatabase` fest.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Erstellen Sie nun die Verbindungszeichenfolge. In diesem Beispiel wird die Geräteauthentifizierung zum Zugreifen auf den Cluster verwendet. Sie können auch das Azure Active Directory-Anwendungszertifikat, den Anwendungsschlüssel und den Benutzer mit dem zugehörigen Kennwort verwenden.

Sie erstellen die Zieltabelle und die Zuordnung in einem späteren Schritt.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Festlegen der Informationen zur Quelldatei

Importieren Sie zusätzliche Klassen, und legen Sie Konstanten für die Datenquellendatei fest. In diesem Beispiel wird eine Beispieldatei verwendet, die in Azure Blob Storage gehostet wird. Das **StormEvents**-Beispieldataset enthält wetterbezogene Daten der [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Erstellen einer Tabelle im Testcluster

Erstellen Sie eine Tabelle, die dem Schema der Daten in der Datei `StormEvents.csv` entspricht. Wenn dieser Code ausgeführt wird, wird eine Meldung wie die folgende zurückgegeben: *Verwenden Sie zur Anmeldung einen Webbrowser, um die Seite https://microsoft.com/devicelogin zu öffnen. Geben Sie dann zur Authentifizierung den Code XXXXXXXXX ein*. Befolgen Sie die Schritte für die Anmeldung, und kehren Sie dann zurück, um den Codeblock auszuführen. Für nachfolgende Codeblöcke, die eine Verbindung herstellen, müssen Sie sich wieder anmelden.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Definieren der Erfassungszuordnung

Ordnen Sie eingehende JSON-Daten den beim Erstellen der Tabelle verwendeten Spaltennamen und Datentypen zu.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Stellen einer Nachricht in eine Warteschlange für die Erfassung

Stellen Sie eine Nachricht zum Abrufen von Daten aus Blob Storage in eine Warteschlange, und erfassen Sie diese Daten im Azure-Daten-Explorer.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Überprüfen, ob die Tabelle Daten enthält

Überprüfen Sie, ob die Daten in der Tabelle erfasst wurden. Warten Sie fünf bis zehn Minuten, bis die Warteschlange die Erfassung geplant und die Daten in den Azure-Daten-Explorer geladen hat. Führen Sie dann den folgenden Code aus, um die Anzahl von Datensätzen in der Tabelle `StormEvents` zu erhalten.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
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
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie unsere anderen Schnellstarts und Tutorials durchgehen möchten, behalten Sie die erstellten Ressourcen bei. Wenn dies nicht der Fall ist, führen Sie den folgenden Befehl in der Datenbank aus, um die Tabelle `StormEvents` zu bereinigen.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Write queries](write-queries.md) (Schreiben von Abfragen)
