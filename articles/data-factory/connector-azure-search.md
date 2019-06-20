---
title: Kopieren von Daten in den Search-Index mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten mit der Kopieraktivität in einer Azure Data Factory-Pipeline mithilfe von Push auf einen Azure Search-Index übertragen oder in einen Azure Search-Index kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: jingwang
ms.openlocfilehash: faf0cab55ec0cef034638d218f2172f3676ff39b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245106"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Kopieren von Daten in einen Azure Search-Index mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-azure-search-connector.md)
> * [Aktuelle Version](connector-azure-search.md)

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in einen Azure Search-Index zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus einem beliebigen unterstützten Quelldatenspeicher in einen Azure Search-Index kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Azure Search-Index verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Azure Search verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureSearch** | Ja |
| url | URL für den Azure Search-Dienst. | Ja |
| key | Admin-Schlüssel für den Azure Search-Dienst. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

> [!IMPORTANT]
> Wenn Sie Daten aus einem Clouddatenspeicher in den Azure Search-Index kopieren, dem mit Azure Search verknüpften Dienst, müssen Sie auf eine Azure Integration Runtime mit explizitem Bereich in connectVia verweisen. Legen Sie die Region als Region fest, in der sich Ihre Azure Search-Instanz befindet. Weitere Informationen finden Sie unter [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime).

**Beispiel:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu Datasets. Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Azure Search-Dataset unterstützt werden.

Beim Kopieren von Daten in Azure Search werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **AzureSearchIndex** | Ja |
| indexName | Name eines Azure Search-Index. Data Factory erstellt den Index nicht. Der Index muss in Azure Search vorhanden sein. | Ja |

**Beispiel:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Search-Quelle unterstützt werden.

### <a name="azure-search-as-sink"></a>Azure Search als Senke

Legen Sie zum Kopieren von Daten in Azure Search den Quelltyp in der Kopieraktivität auf **AzureSearchIndexSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AzureSearchIndexSink** | Ja |
| writeBehavior | Gibt an, ob ein Dokument zusammengeführt oder ersetzt werden soll, wenn es bereits im Index vorhanden ist. Siehe [Eigenschaft „WriteBehavior“](#writebehavior-property).<br/><br/>Zulässige Werte sind: **Merge** (Standard) und **Upload**. | Nein |
| writeBatchSize | Lädt Daten in den Azure Search-Index hoch,wenn die Puffergröße „writeBatchSize“ erreicht. Einzelheiten finden Sie unter [Eigenschaft „WriteBatchSize“](#writebatchsize-property).<br/><br/>Zulässige Werte sind ganze Zahlen von 1 bis 1.000 (Standardwert „1.000“). | Nein |

### <a name="writebehavior-property"></a>Eigenschaft „WriteBehavior“

AzureSearchSink fügt Daten ein/aktualisiert beim Schreiben von Daten. Dies bedeutet, dass Azure Search beim Schreiben eines Dokuments das bestehende Dokument aktualisiert, anstatt eine Konfliktausnahme auszulösen, wenn der Dokumentenschlüssel bereits im Azure Search-Index vorhanden ist.

AzureSearchSink bietet die folgenden zwei Verhalten zum Einfügen/Aktualisieren (mithilfe des Azure Search SDK):

- **Merge** (Zusammenführen): kombiniert alle Spalten im neuen Dokument mit dem bestehenden. Bei Spalten mit Null-Wert im neuen Dokument wird der Wert im bestehenden Dokument beibehalten.
- **Upload** (Hochladen): Das neue Dokument ersetzt das bestehende. Bei Spalten, die nicht im neuen Dokument angegeben werden, wird der Wert auf Null gesetzt, unabhängig davon, ob ein Null-Wert im bestehenden Dokument vorhanden ist oder nicht.

Das Standardverhalten ist **Merge**.

### <a name="writebatchsize-property"></a>Eigenschaft „writeBatchSize“

Der Azure Search-Dienst unterstützt das Schreiben von Dokumenten als Batch. Ein Batch kann 1 bis 1.000 Aktionen enthalten. Eine Aktion bearbeitet ein Dokument, um den Vorgang upload/merge auszuführen.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>Unterstützung von Datentypen

In der folgenden Tabelle wird angegeben, ob ein Azure Search-Datentyp unterstützt wird oder nicht.

| Azure Search-Datentyp | In Azure Search-Senke unterstützt |
| ---------------------- | ------------------------------ |
| String | J |
| Int32 | J |
| Int64 | J |
| Double | J |
| Boolean | J |
| DataTimeOffset | J |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
