---
title: Kopieren von Daten aus und in IBM Informix mithilfe von Azure Data Factory
description: Hier erfahren Sie, wie Daten mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline aus und in IBM Informix kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: jingwang
ms.openlocfilehash: 95ecb44f154ab84a60a1ee673826d83fc51305b3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383499"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Kopieren von Daten aus und in IBM Informix mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einem bzw. in einen IBM Informix-Datenspeicher zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Informix-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer Informix-Quelle in jeden unterstützten Senkendatenspeicher oder Daten aus jedem unterstützten Quelldatenspeicher in eine Informix-Senke kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden dieses Informix-Connectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.
- Installieren des Informix-ODBC-Treibers für den Datenspeicher auf dem Computer mit der Integration Runtime. Beispielsweise können Sie den Treiber „IBM INFORMIX Informix DRIVER (64-bit)“ verwenden.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Informix-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Informix verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Informix** | Ja |
| connectionString | Die ODBC-Verbindungszeichenfolge, ausgenommen des Teils mit den Anmeldeinformationen. Sie können die Verbindungszeichenfolge angeben oder den System-DSN (Data Source Name) verwenden, den Sie auf dem Computer mit der Integration Runtime eingerichtet haben. (Sie müssen nach wie vor den Teil mit den Anmeldeinformationen im verknüpften Dienst entsprechend angeben.) <br> Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md).| Ja |
| authenticationType | Typ der Authentifizierung für die Verbindung mit dem Informix-Datenspeicher.<br/>Zulässige Werte sind: **Standard** und **Anonym**. | Ja |
| userName | Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. | Nein |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „userName“ angegeben haben. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| Anmeldeinformationen (credential) | Der zum Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge. Er wird in einem treiberspezifischen Format in Eigenschaft und Wert angegeben. Legen Sie für dieses Feld „SecureString“ fest. | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Informix-Dataset unterstützt werden.

Zum Kopieren von Daten aus Informix werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **InformixTable** | Ja |
| tableName | Name der Tabelle in Informix. | Nein bei Quellen (wenn „query“ in der Aktivitätsquelle angegeben ist);<br/>ja bei Senken |

**Beispiel**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Informix-Quelle unterstützt werden.

### <a name="informix-as-source"></a>Informix als Quelle

Wenn Sie Daten aus Informix kopieren möchten, werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **InformixSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix als Senke

Beim Kopieren von Daten in Informix werden die folgenden Eigenschaften im Abschnitt **sink** der Copy-Aktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf Folgendes festgelegt sein: **InformixSink** | Ja |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br/>Zulässige Werte: Zeitraum Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht.<br/>Zulässige Werte: Ganze Zahlen (Anzahl der Zeilen). |Nein (Standard ist 0 – automatisch erkannt) |
| preCopyScript |Geben Sie eine auszuführende SQL-Abfrage für die Kopieraktivität an, ehe Sie bei der jeder Ausführung Daten in Datenspeicher schreiben. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. |Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
