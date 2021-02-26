---
title: Kopieren von Daten aus/in SAP Cloud for Customer
description: Erfahren Sie, wie Sie mithilfe von Data Factory Daten aus SAP Cloud for Customer in unterstützte Senkendatenspeicher (oder) aus unterstützten Quelldatenspeichern in SAP Cloud for Customer kopieren können.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: 4590129b8d0d7a63ef4291a023ea4d980c89368c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386644"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Kopieren von Daten aus SAP Cloud for Customer (C4C) mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus und in SAP Cloud for Customer (C4C) kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios durch ADF finden Sie im [Whitepaper zur SAP-Datenintegration mit Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dort finden Sie auch eine detaillierte Einführung in jeden SAP-Connector, einen Vergleich sowie Anleitungen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser SAP Cloud for Customer-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus SAP Cloud for Customer in jeden unterstützten Senkendatenspeicher bzw. Daten aus jedem unterstützten Quelldatenspeicher in SAP Cloud for Customer kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Connector ermöglicht insbesondere, dass Azure Data Factory Daten aus/in SAP Cloud for Customer kopieren kann, einschließlich der Lösungen SAP Cloud for Sales, SAP Cloud for Service und SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP Cloud for Customer-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP Cloud for Customer verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapCloudForCustomer**. | Ja |
| url | Die URL des SAP C4C OData-Diensts. | Ja |
| username | Geben Sie den Benutzernamen für die Verbindung mit dem SAP C4C-Server an. | Ja |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „username“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

**Beispiel:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP Cloud for Customer-Dataset unterstützt werden.

Um Daten aus SAP Cloud for Customer zu kopieren, legen Sie die „type“-Eigenschaft des Datasets auf **SapCloudForCustomerResource** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **SapCloudForCustomerResource** |Ja |
| path | Geben Sie den Pfad der Entität „SAP C4C OData“ an. |Ja |

**Beispiel:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP Cloud for Customer-Quelle unterstützt werden.

### <a name="sap-c4c-as-source"></a>SAP C4C als Quelle

Legen Sie zum Kopieren von Daten aus SAP Cloud for Customer den Quelltyp in der Kopieraktivität auf **SapCloudForCustomerSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapCloudForCustomerSource**  | Ja |
| Abfrage | Geben Sie die benutzerdefinierte OData-Abfrage zum Lesen von Daten an. | Nein |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Wenn Sie hier nichts angeben, lautet der Standardwert **00:30:00** (30 Minuten). | Nein |

Beispielabfrage zum Abrufen von Daten für einen bestimmten Tag: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C als Senke

Legen Sie zum Kopieren von Daten in SAP Cloud for Customer den Senkentyp in der Kopieraktivität auf **SapCloudForCustomerSink** fest. Folgende Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapCloudForCustomerSink**  | Ja |
| writeBehavior | Das Schreibverhalten des Vorgangs. Optionen: „Insert“, „Update“. | Nein. Standard: „Insert“. |
| writeBatchSize | Die Batchgröße des Schreibvorgangs. Die Batchgröße zum Erzielen der besten Leistung kann für verschiedene Tabellen oder Server unterschiedlich sein. | Nein. Standardwert: 10 |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Datentypzuordnung für SAP Cloud for Customer

Beim Kopieren von Daten aus SAP Cloud for Customer werden die folgenden Zuordnungen von SAP Cloud for Customer-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP C4C OData-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
