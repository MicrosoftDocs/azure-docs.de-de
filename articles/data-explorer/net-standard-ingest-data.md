---
title: Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)
description: In diesem Artikel erfahren Sie, wie Sie mit dem .NET Standard SDK Daten in Azure Data Explorer erfassen (laden).
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 53cf055a0900a25923fe67b961755c1f4367e1fb
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496890"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)

Azure Data Explorer (ADX) ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Protokoll- und Telemetriedaten. Azure Data Explorer bietet zwei Clientbibliotheken für .NET Standard: eine [Erfassungsbibliothek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) und eine [Datenbibliothek](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Mit diesen Bibliotheken können Sie über Ihren Code Daten in einem Cluster erfassen (laden) und Daten abfragen. In diesem Artikel erstellen Sie zunächst eine Tabelle und eine Datenzuordnung in einem Testcluster. Anschließend stellen Sie die Erfassung im Cluster in eine Warteschlange und überprüfen die Ergebnisse.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

* [Einen Testcluster und eine Testdatenbank](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Installieren der Erfassungsbibliothek

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Für die Authentifizierung von Anwendungen verwendet der Azure-Daten-Explorer Ihre AAD-Mandanten-ID. Um Ihre Mandanten-ID zu suchen, verwenden Sie die folgende URL, und ersetzen Sie dabei *YourDomain* durch Ihre Domäne.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Klicken Sie auf diese URL, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Die Mandanten-ID ist in diesem Fall `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

In diesem Beispiel werden ein AAD-Benutzer (Azure Active Directory) und das zugehörige Kennwort zur Authentifizierung verwendet, um auf den Cluster zuzugreifen. Sie können auch ein AAD-Anwendungszertifikat und einen AAD-Anwendungsschlüssel verwenden. Legen Sie vor dem Ausführen dieses Codes Ihre Werte für `tenantId`, `user` und `password` fest.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Erstellen der Verbindungszeichenfolge
Erstellen Sie nun die Verbindungszeichenfolge. Sie erstellen die Zieltabelle und die Zuordnung in einem späteren Schritt.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Festlegen der Informationen zur Quelldatei

Legen Sie den Pfad für die Quelldatei fest. In diesem Beispiel wird eine Beispieldatei verwendet, die in Azure Blob Storage gehostet wird. Das **StormEvents**-Beispieldataset enthält wetterbezogene Daten der [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Erstellen einer Tabelle im Testcluster
Erstellen Sie eine Tabelle mit dem Namen `StormEvents`, die dem Schema der Daten in der Datei `StormEvents.csv` entspricht.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definieren der Erfassungszuordnung

Ordnen Sie die eingehenden CSV-Daten den beim Erstellen der Tabelle verwendeten Spaltennamen zu.
Bereitstellen eines [Objekts für die CSV-Spaltenzuordnung](/azure/kusto/management/tables#create-ingestion-mapping) in dieser Tabelle

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Stellen einer Nachricht in eine Warteschlange für die Erfassung

Stellen Sie eine Nachricht zum Pullen von Daten aus Blob Storage in eine Warteschlange, und erfassen Sie diese Daten in Azure Data Explorer.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Überprüfen, ob die Daten in der Tabelle erfasst wurden

Warten Sie fünf bis zehn Minuten, bis die Warteschlange die Erfassung geplant und die Daten in Azure Data Explorer geladen hat. Führen Sie dann den folgenden Code aus, um die Anzahl von Datensätzen in der Tabelle `StormEvents` zu erhalten.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
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

Wenn Sie unsere anderen Artikel durcharbeiten möchten, behalten Sie die erstellten Ressourcen bei. Wenn dies nicht der Fall ist, führen Sie den folgenden Befehl in der Datenbank aus, um die Tabelle `StormEvents` zu bereinigen.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Nächste Schritte

* [Write queries](write-queries.md) (Schreiben von Abfragen)
