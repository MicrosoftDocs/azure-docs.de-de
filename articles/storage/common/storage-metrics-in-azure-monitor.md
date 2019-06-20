---
title: Azure Storage-Metriken in Azure Monitor | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu den neuen Metriken von Azure Monitor.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 8b091ecce98a626f18fe6547445d898b6710e1a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510549"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Storage-Metriken in Azure Monitor

Anhand von Azure Storage-Metriken können Sie Verwendungstrends analysieren, Anforderungen nachverfolgen und Probleme mit Ihrem Speicherkonto diagnostizieren.

Azure Monitor bietet einheitliche Benutzeroberflächen für die übergreifende Überwachung verschiedener Azure-Dienste. Weitere Informationen finden Sie im [Überblick über die Überwachung in Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview.md). Zur Integration von Azure Monitor sendet Azure Storage Metrikdaten an die Azure Monitor-Plattform.

## <a name="access-metrics"></a>Zugreifen auf Metriken

Azure Monitor bietet mehrere Möglichkeiten für den Metrikzugriff. Sie können über das [Azure-Portal](https://portal.azure.com), die Azure Monitor-APIs (REST und .NET) sowie Analyselösungen wie Event Hubs auf Metriken zugreifen. Weitere Informationen finden Sie im [Überblick über Metriken in Microsoft Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Metriken sind standardmäßig aktiviert, und es stehen Daten für die letzten 93 Tage zur Verfügung. Zur längeren Aufbewahrung können Sie Metrikdaten in einem Azure-Speicherkonto archivieren. Dies wird in den [Diagnoseeinstellungen](../../azure-monitor/platform/diagnostic-logs-overview.md) in Azure Monitor konfiguriert.

### <a name="access-metrics-in-the-azure-portal"></a>Zugreifen auf Metriken über das Azure-Portal

Im Azure-Portal können Sie Metriken im Zeitverlauf überwachen. Das folgende Beispiel zeigt, wie Sie **Transaktionen** auf der Kontoebene anzeigen.

![Screenshot für den Zugriff auf Metriken über das Azure-Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Bei Metriken mit Dimensionsunterstützung können Sie die Metrik nach dem gewünschten Dimensionswert filtern. Das folgende Beispiel zeigt, wie Sie **Transaktionen** auf der Kontoebene für einen bestimmten Vorgang anzeigen, indem Sie Werte für die Dimension **API-Name** auswählen.

![Screenshot für den Zugriff auf Metriken mit Dimension über das Azure-Portal](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Zugreifen auf Metriken über die REST-API

Azure Monitor verfügt über [REST-APIs](/rest/api/monitor/) zum Lesen von Metrikdefinition und -werten. In diesem Abschnitt erfahren Sie, wie Sie die Speichermetriken lesen. Bei allen REST-APIs wird die Ressourcen-ID verwendet. Weitere Informationen finden Sie unter „Grundlegendes zur Ressourcen-ID für Dienste in Azure Storage“.

Das folgende Beispiel zeigt, wie Sie [ArmClient](https://github.com/projectkudu/ARMClient) in der Befehlszeile verwenden, um das Testen mit der REST-API zu vereinfachen.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Auflisten der Metrikdefinition auf der Kontoebene über die REST-API

Das folgende Beispiel zeigt, wie Sie die Metrikdefinition auf der Kontoebene auflisten:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Wenn Sie die Metrikdefinitionen für Blob, Table, File oder Queue auflisten möchten, müssen Sie mit der API verschiedene Ressourcen-IDs für die einzelnen Dienste angeben.

Die Antwort enthält die Metrikdefinition im JSON-Format:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Lesen der Metrikwerte auf der Kontoebene über die REST-API

Das folgende Beispiel zeigt, wie Sie Metrikdaten auf der Kontoebene lesen:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Wenn Sie im obigen Beispiel Metrikwerte für Blob, Table, File oder Queue lesen möchten, müssen Sie mit der API verschiedene Ressourcen-IDs für die einzelnen Dienste angeben.

Die folgende Antwort enthält Metrikwerte im JSON-Format:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Zugreifen auf Metriken über das .NET SDK

Azure Monitor bietet das [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) zum Lesen von Metrikdefinition und -werten. Die [Beispielcode](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) zeigt, wie das SDK mit unterschiedlichen Parametern verwendet wird. Sie benötigen `0.18.0-preview` oder eine höhere Version für Speichermetriken. Im .NET SDK wird die Ressourcen-ID verwendet. Weitere Informationen finden Sie unter „Grundlegendes zur Ressourcen-ID für Dienste in Azure Storage“.

Das folgende Beispiel zeigt, wie Speichermetriken mithilfe des .NET SDK für Azure Monitor gelesen werden.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Auflisten der Metrikdefinition auf Kontoebene über das .NET SDK

Das folgende Beispiel zeigt, wie Sie die Metrikdefinition auf der Kontoebene auflisten:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Wenn Sie die Metrikdefinitionen für Blob, Table, File oder Queue auflisten möchten, müssen Sie mit der API verschiedene Ressourcen-IDs für die einzelnen Dienste angeben.

#### <a name="read-metric-values-with-the-net-sdk"></a>Lesen von Metrikwerten mit dem .NET SDK

Das folgende Beispiel zeigt, wie Sie `UsedCapacity`-Daten auf der Kontoebene lesen:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Wenn Sie im obigen Beispiel Metrikwerte für Blob, Table, File oder Queue lesen möchten, müssen Sie mit der API verschiedene Ressourcen-IDs für die einzelnen Dienste angeben.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Lesen von Werten mehrdimensionaler Metriken mit dem .NET SDK

Für mehrdimensionale Metriken müssen Sie Metadatenfilter definieren, wenn Metrikdaten für bestimmte Dimensionswerte gelesen werden sollen.

Im folgenden Beispiel wird gezeigt, wie Metrikdaten bei Metriken mit Unterstützung für mehrere Dimensionen gelesen werden:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Grundlegendes zur Ressourcen-ID für Dienste in Azure Storage

Die Ressourcen-ID ist ein eindeutiger Bezeichner einer Ressource in Azure. Wenn Sie Metrikdefinitionen oder -werte mithilfe der REST-API von Azure Monitor lesen möchten, müssen Sie die Ressourcen-ID für die gewünschte Ressource verwenden. Die Ressourcen-ID-Vorlage hat folgendes Format:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Mit Azure Monitor bietet Storage Metriken auf der Speicherkontoebene und auf der Dienstebene. So haben Sie beispielsweise die Möglichkeit, nur Metriken für Blob Storage abzurufen. Jede Ebene hat ihre eigene Ressourcen-ID, mit der Metriken für genau diese Ebene abgerufen werden können.

### <a name="resource-id-for-a-storage-account"></a>Ressourcen-ID für ein Speicherkonto

Das Format zum Angeben der Ressourcen-ID für ein Speicherkonto sieht wie folgt aus:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>Ressourcen-ID für die Speicherdienste

Das Format zum Angeben der Ressourcen-ID für die einzelnen Speicherdienste sieht wie folgt aus:

* Ressourcen-ID für den Blobdienst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Ressourcen-ID für den Tabellenspeicherdienst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* Ressourcen-ID für den Warteschlangendienst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* Ressourcen-ID für den Dateidienst
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Ressourcen-ID in der REST-API von Azure Monitor

Das Muster zum Aufrufen der REST-API von Azure Monitor sieht wie folgt aus:

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Kapazitätsmetriken
Kapazitätsmetrikwerte werden stündlich an Azure Monitor gesendet. Die Werte werden täglich aktualisiert. Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Aggregationsintervall für alle Kapazitätsmetriken beträgt eine Stunde (PT1H).

Azure Storage stellt in Azure Monitor folgende Kapazitätsmetriken bereit:

### <a name="account-level"></a>Kontoebene

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| UsedCapacity | Die vom Speicherkonto beanspruchte Speichermenge. Bei Standardspeicherkonten ist das die Summe der von Blob, Table, File und Queue beanspruchten Kapazität. Bei Storage Premium- und Blob Storage-Konten ist es der gleiche Wert wie für „BlobCapacity“. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |

### <a name="blob-storage"></a>Blob Storage

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| BlobCapacity | Der gesamte im Speicherkonto beanspruchte Blobspeicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 <br/> Abmessungen: **BlobType** und **BlobTier** ([Definition](#metrics-dimensions)) |
| BlobCount    | Die Anzahl von im Speicherkonto gespeicherten Blobs. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 <br/> Abmessungen: **BlobType** und **BlobTier** ([Definition](#metrics-dimensions)) |
| ContainerCount    | Die Anzahl von Containern im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| IndexCapacity     | Die vom hierarchischen ADLS Gen2-Index verwendete Speichermenge <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |

### <a name="table-storage"></a>Table Storage

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| TableCapacity | Der vom Speicherkonto beanspruchte Table-Speicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| TableCount   | Die Anzahl von Tabellen im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| TableEntityCount | Die Anzahl von Tabellenentitäten im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |

### <a name="queue-storage"></a>Queue Storage

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| QueueCapacity | Der vom Speicherkonto beanspruchte Queue-Speicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| QueueCount   | Die Anzahl von Warteschlangen im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| QueueMessageCount | Die Anzahl nicht abgelaufener Warteschlangennachrichten im Speicherkonto. <br/><br/>Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |

### <a name="file-storage"></a>File Storage

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| FileCapacity | Der vom Speicherkonto beanspruchte File-Speicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| FileCount   | Die Anzahl von Dateien im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |
| FileShareCount | Die Anzahl von Dateifreigaben im Speicherkonto. <br/><br/> Einheit: Count <br/> Aggregationstyp: Durchschnitt <br/> Beispielwert: 1024 |

## <a name="transaction-metrics"></a>Transaktionsmetriken

Transaktionsmetriken werden für jede Anforderung an ein Speicherkonto aus Azure Storage an Azure Monitor ausgegeben. Falls in Ihrem Speicherkonto keine Aktivität stattfindet, gibt es in diesem Zeitraum keine Daten zu Transaktionsmetriken. Alle Transaktionsmetriken stehen sowohl auf der Konto- als auch auf der Dienstebene (Blob Storage, Table Storage, Azure Files und Queue Storage) zur Verfügung. Das Aggregationsintervall definiert das Zeitintervall für die dargestellten Metrikwerte. Für alle Transaktionsmetriken werden die Aggregationsintervalle PT1H und PT1M unterstützt.

Azure Storage stellt in Azure Monitor folgende Transaktionsmetriken bereit:

| Metrikname | BESCHREIBUNG |
| ------------------- | ----------------- |
| Transaktionen | Die Anzahl von Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Diese Anzahl umfasst erfolgreiche und nicht erfolgreiche Anforderungen sowie Anforderungen, die zu Fehlern geführt haben. <br/><br/> Einheit: Count <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: ResponseType, GeoType, ApiName, Authentication ([Definition](#metrics-dimensions))<br/> Beispielwert: 1024 |
| Eingehende Daten | Die eingehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete eingehende Daten von einem externen Client sowie eingehende Daten innerhalb von Azure. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| Ausgehende Daten | Die ausgehende Datenmenge. Dieser Wert umfasst an Azure Storage gerichtete ausgehende Daten von einem externen Client sowie ausgehende Daten innerhalb von Azure. Der Wert stellt somit keine gebührenpflichtigen ausgehenden Daten dar. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Gesamt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| SuccessServerLatency | Die durchschnittliche Verarbeitungszeit einer erfolgreichen Anforderung durch Azure Storage. Dieser Wert beinhaltet nicht die in „SuccessE2ELatency“ angegebene Netzwerklatenz. <br/><br/> Einheit: Millisekunden <br/> Aggregationstyp: Durchschnitt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| SuccessE2ELatency | Die durchschnittliche End-to-End-Wartezeit (in Millisekunden) bei erfolgreichen Anforderungen, die an einen Speicherdienst oder an den angegebenen API-Vorgang gerichtet wurden. Dieser Wert umfasst die erforderliche Verarbeitungszeit in Azure Storage für das Lesen der Anforderung, das Senden der Antwort und das Empfangen der Antwortbestätigung. <br/><br/> Einheit: Millisekunden <br/> Aggregationstyp: Durchschnitt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 1024 |
| Verfügbarkeit | Die Verfügbarkeit (in Prozent) für den Speicherdienst oder den angegebenen API-Vorgang. Zur Berechnung der Verfügbarkeit wird der Wert der gesamten gebührenpflichtigen Anforderungen durch die Anzahl entsprechender Anforderungen geteilt. Dabei werden auch Anforderungen einbezogen, die zu unerwarteten Fehlern geführt haben. Alle unerwarteten Fehler verringern die Verfügbarkeit für den Speicherdienst oder den angegebenen API-Vorgang. <br/><br/> Einheit: Percent <br/> Aggregationstyp: Durchschnitt <br/> Verfügbare Dimensionen: GeoType, ApiName, Authentication ([Definition](#metrics-dimensions)) <br/> Beispielwert: 99,99 |

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Storage unterstützt folgende Dimensionen für Metriken in Azure Monitor:

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **BlobType** | Die Art des Blobs (nur für Blobmetriken). Die unterstützten Werte sind **BlockBlob**, **PageBlob**, und **Azure Data Lake Storage**. Anfügeblob ist in „BlockBlob“ enthalten. |
| **BlobTier** | Azure Storage bietet unterschiedliche Zugriffsebenen, die Ihnen das Speichern von Blobobjektdaten auf die kostengünstigste Art ermöglichen. Weitere Informationen finden Sie in [Azure Storage-Blobtarif](../blobs/storage-blob-storage-tiers.md). Die unterstützten Werte umfassen: <br/> <li>**Hot**: Heiße Zugriffsebene</li> <li>**Cool**: Kalte Zugriffsebene</li> <li>**Archive**: Archivzugriffsebene</li> <li>**Premium**: Premium-Tarif für Blockblob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Zugriffsebenentypen für Premium-Seitenblob</li> <li>**Standard**: Zugriffsebenentypen für Standard-Seitenblob</li> <li>**Kein Tarif**: Zugriffsebenentyp für Speicherkonto des Typs „Allgemein v1“</li> |
| **GeoType** | Transaktion aus dem primären oder sekundären Cluster. Die verfügbaren Werte sind **Primary** und **Secondary**. Gilt für georedundanten Speicher mit Lesezugriff (Read-Access Geographically Redundant Storage, RA-GRS) beim Lesen von Objekten aus dem sekundären Mandanten. |
| **ResponseType** | Transaktionsantworttyp. Verfügbaren Werte: <br/><br/> <li>**ServerOtherError**: Alle weiteren serverseitigen Fehler (mit Ausnahme beschriebener Fehler). </li> <li>**ServerBusyError**: Authentifizierte Anforderung, die den HTTP-Statuscode 503 zurückgegeben hat. </li> <li>**ServerTimeoutError**: Authentifizierte Anforderung mit Timeoutfehler, die den HTTP-Statuscode 500 zurückgegeben hat. Das Timeout ist auf einen Serverfehler zurückzuführen. </li> <li>**AuthorizationError**: Authentifizierte Anforderung, die aufgrund eines nicht autorisierten Datenzugriffs oder aufgrund eines Autorisierungsfehlers nicht erfolgreich war. </li> <li>**NetworkError**: Authentifizierte Anforderung, die aufgrund von Netzwerkfehlern nicht erfolgreich war. Tritt üblicherweise auf, wenn ein Client vor Ablauf des Timeouts vorzeitig eine Verbindung trennt. </li> <li>**ClientThrottlingError**: Clientseitiger Drosselungsfehler. </li> <li>**ClientTimeoutError**: Authentifizierte Anforderung mit Timeoutfehler, die den HTTP-Statuscode 500 zurückgegeben hat. Wenn das Netzwerktimeout des Clients oder das Anforderungstimeout auf einen niedrigeren Wert festgelegt ist als vom Speicherdienst erwartet, handelt es sich um ein erwartetes Timeout. Andernfalls wird „ServerTimeoutError“ gemeldet. </li> <li>**ClientOtherError**: Alle weiteren clientseitigen Fehler (mit Ausnahme beschriebener Fehler). </li> <li>**Erfolg**: Erfolgreiche Anforderung.</li> <li> **SuccessWithThrottling**: Erfolgreiche Anforderung, wenn ein SMB-Client bei den ersten Versuchen gedrosselt wird, der Vorgang aber letztendlich erfolgreich ist.</li> |
| **ApiName** | Der Name des Vorgangs. Beispiel: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Eine Liste mit allen Vorgangsnamen finden Sie [hier](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Authentifizierung** | Der in Transaktionen verwendete Authentifizierungstyp. Verfügbaren Werte: <br/> <li>**AccountKey**: Die Transaktion wird mit dem Speicherkontoschlüssel authentifiziert.</li> <li>**SAS**: Die Transaktion wird mit Shared Access Signatures authentifiziert.</li> <li>**OAuth**: Die Transaktion wird mit OAuth-Zugriffstoken authentifiziert.</li> <li>**Anonymous**: Die Transaktion wird anonym angefordert. Sie enthält keine Preflight-Anforderungen.</li> <li>**AnonymousPreflight**: Die Transaktion ist eine Preflightanforderung.</li> |

Bei Metriken mit Dimensionsunterstützung muss der Dimensionswert angegeben werden, um die entsprechenden Metrikwerte anzeigen zu können. Wenn Sie sich also beispielsweise erfolgreiche Antworten für **Transaktionen** ansehen möchten, müssen Sie die Dimension **ResponseType** nach **Success** filtern. Anderes Beispiel: Wenn Sie sich **BlobCount** für „Blockblob“ ansehen möchten, müssen Sie die Dimension **BlobType** nach **BlockBlob** filtern.

## <a name="service-continuity-of-legacy-metrics"></a>Dienstkontinuität von Legacymetriken

Legacymetriken stehen parallel zu den von Azure Monitor verwalteten Metriken zur Verfügung. Die Unterstützung bleibt unverändert, bis Azure Storage den Dienst für Legacymetriken einstellt.

## <a name="faq"></a>Häufig gestellte Fragen

**Unterstützen die neuen Metriken das klassische Speicherkonto?**

Nein, die neuen Metriken in Azure Monitor unterstützen nur Azure Resource Manager-Speicherkonten. Wenn Sie Metriken für Speicherkonten verwenden möchten, müssen Sie zu Azure Resource Manager-Speicherkonten migrieren. Weitere Informationen finden Sie unter [Migrieren zu Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview).

**Unterstützt Azure Storage Metriken für verwaltete oder nicht verwaltete Datenträger?**

Nein. Die Metriken für Datenträger werden von Azure Compute unterstützt. Weitere Informationen finden Sie in [diesem Artikel](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

**Zuordnen und Migrieren zwischen klassischen und neuen Metriken**

Die ausführliche Zuordnung zwischen klassischen und neuen Metriken finden Sie unter [Migration von Azure Storage-Metriken](./storage-metrics-migration.md).

## <a name="next-steps"></a>Nächste Schritte

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
