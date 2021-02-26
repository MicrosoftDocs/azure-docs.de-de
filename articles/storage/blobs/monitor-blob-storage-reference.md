---
title: Überwachen von Daten in Azure-Blobspeicher – Referenz | Microsoft-Dokumentation
description: Protokoll- und Metrikenreferenz für die Überwachung von Daten in Azure-Blobspeicher
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: 6dd53358b67eef481948d3349afc07086ea26fcd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584905"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Überwachen von Daten in Azure-Blobspeicher – Referenz

Ausführliche Informationen über das Erfassen und Analysieren von Überwachungsdaten für Azure Storage finden Sie unter [Überwachen von Azure Storage](monitor-blob-storage.md).

## <a name="metrics"></a>Metriken

In den nachstehenden Tabellen sind die für Azure Storage erfassten Plattformmetriken aufgelistet. 

### <a name="capacity-metrics"></a>Kapazitätsmetriken

Kapazitätsmetriken werden täglich aktualisiert (bis zu 24 Stunden). Das Aggregationsintervall definiert das Zeitintervall, für das Metrikwerte dargestellt werden. Das unterstützte Aggregationsintervall für alle Kapazitätsmetriken beträgt eine Stunde (PT1H).

Azure Storage stellt in Azure Monitor folgende Kapazitätsmetriken bereit:

#### <a name="account-level"></a>Kontoebene

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

Diese Tabelle enthält [Blob Storage-Metriken](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Metrik | BESCHREIBUNG |
| ------------------- | ----------------- |
| BlobCapacity | Der gesamte im Speicherkonto beanspruchte Blobspeicher. <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 <br/> Abmessungen: **BlobType** und **BlobTier** ([Definition](#metrics-dimensions)) |
| BlobCount    | Die Anzahl von im Speicherkonto gespeicherten Blobs. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 <br/> Abmessungen: **BlobType** und **BlobTier** ([Definition](#metrics-dimensions)) |
| BlobProvisionedSize | Die Speichermenge, die im Speicherkonto bereitgestellt wird. Diese Metrik gilt nur für Premium-Speicherkonten. <br/><br/> Einheit: Bytes <br/> Aggregationstyp: Average |
| ContainerCount    | Die Anzahl von Containern im Speicherkonto. <br/><br/> Einheit: Anzahl <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |
| IndexCapacity     | Die vom hierarchischen ADLS Gen2-Index verwendete Speichermenge <br/><br/> Einheit: Byte <br/> Aggregationstyp: Average <br/> Beispielwert: 1024 |

### <a name="transaction-metrics"></a>Transaktionsmetriken

Transaktionsmetriken werden für jede Anforderung an ein Speicherkonto aus Azure Storage an Azure Monitor ausgegeben. Falls in Ihrem Speicherkonto keine Aktivität stattfindet, gibt es in diesem Zeitraum keine Daten zu Transaktionsmetriken. Alle Transaktionsmetriken stehen sowohl auf der Konto- als auch auf der Dienstebene von Blobspeicher zur Verfügung. Das Aggregationsintervall definiert das Zeitintervall für die dargestellten Metrikwerte. Für alle Transaktionsmetriken werden die Aggregationsintervalle PT1H und PT1M unterstützt.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikdimensionen

Azure Storage unterstützt folgende Dimensionen für Metriken in Azure Monitor:

### <a name="dimensions-available-to-all-storage-services"></a>Für alle Speicherdienste verfügbare Dimensionen

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Spezifische Dimensionen für Blobspeicher

| Dimensionsname | BESCHREIBUNG |
| ------------------- | ----------------- |
| **BlobType** | Die Art des Blobs (nur für Blobmetriken). Die unterstützten Werte sind **BlockBlob**, **PageBlob**, und **Azure Data Lake Storage**. Anfügeblobs sind in **BlockBlob** enthalten. |
| **BlobTier** | Azure Storage bietet unterschiedliche Zugriffsebenen, die Ihnen das Speichern von Blobobjektdaten auf die kostengünstigste Art ermöglichen. Weitere Informationen finden Sie in [Azure Storage-Blobtarif](../blobs/storage-blob-storage-tiers.md). Die unterstützten Werte umfassen: <br/> <li>**Hot**: Heiße Zugriffsebene</li> <li>**Cool**: Kalte Zugriffsebene</li> <li>**Archive**: Archivzugriffsebene</li> <li>**Premium**: Premium-Tarif für Blockblob</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: Zugriffsebenentypen für Premium-Seitenblob</li> <li>**Standard**: Zugriffsebenentypen für Standard-Seitenblob</li> <li>**Kein Tarif**: Zugriffsebenentyp für Speicherkonto des Typs „Allgemein v1“</li> |

Bei Metriken mit Dimensionsunterstützung muss der Dimensionswert angegeben werden, um die entsprechenden Metrikwerte anzeigen zu können. Wenn Sie sich also beispielsweise erfolgreiche Antworten für **Transaktionen** ansehen möchten, müssen Sie die Dimension **ResponseType** nach **Success** filtern. Wenn Sie sich **BlobCount** für „Blockblob“ ansehen möchten, müssen Sie die Dimension **BlobType** nach **BlockBlob** filtern.

## <a name="resource-logs-preview"></a>Ressourcenprotokolle (Vorschau)

> [!NOTE]
> Azure Storage-Protokolle in Azure Monitor befinden sich in der öffentlichen Vorschauphase und stehen in allen öffentlichen Cloudregionen für Vorschautests zur Verfügung. Diese Vorschauversion ermöglicht Protokolle für Blobs (einschließlich Azure Data Lake Storage Gen2), Dateien, Warteschlangen, Tabellen, Universell V1-Premium-Speicherkonten und Universell V2-Speicherkonten. Klassische Speicherkonten werden nicht unterstützt.

In der nachstehenden Tabelle sind die Eigenschaften für Azure Storage-Ressourcenprotokolle aufgelistet, wenn diese in Azure Monitor-Protokollen oder Azure Storage gesammelt werden. Diese Eigenschaften beschreiben den Vorgang, den Dienst und den Autorisierungstyp, der zum Ausführen des Vorgangs verwendet wurde.

### <a name="fields-that-describe-the-operation"></a>Den Vorgang beschreibende Felder

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Felder, die beschreiben, wie der Vorgang authentifiziert wurde

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Den Dienst beschreibende Felder

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Weitere Informationen

- Eine Beschreibung der Überwachung von Azure Storage finden Sie unter [Überwachen von Azure Storage](monitor-blob-storage.md).
- Ausführliche Informationen zur Überwachung von Azure-Ressourcen finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).
