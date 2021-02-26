---
title: Kopieren von Daten aus Amazon Redshift
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten aus Amazon Redshift in unterstützte Senkendatenspeicher kopieren.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 9441885766dad97dfc237ab81a59710245bf13ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364255"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Kopieren von Daten aus Amazon Redshift mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-amazon-redshift-connector.md)
> * [Aktuelle Version](connector-amazon-redshift.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Amazon Redshift zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon Redshift-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten von Amazon Redshift in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Insbesondere unterstützt dieser Amazon Redshift-Connector das Abrufen von Daten aus Redshift mittels Abfrage oder integrierter Redshift-UNLOAD-Unterstützung.

> [!TIP]
> Um die optimale Leistung beim Kopieren großer Datenmengen aus Redshift zu erzielen, empfiehlt sich die Verwendung des integrierten Redshift-Befehls UNLOAD durch Amazon S3. Ausführliche Informationen finden Sie im Abschnitt [Verwenden von UNLOAD zum Kopieren von Daten aus Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie Daten mit [selbstgehosteter Integration Runtime](create-self-hosted-integration-runtime.md) in einen lokalen Datenspeicher kopieren, gewähren Sie der Integration Runtime (IP-Adresse des Computers verwenden) den Zugriff auf den Amazon Redshift-Cluster. Anweisungen finden Sie unter [Authorize access to the cluster](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autorisieren des Zugriffs auf den Cluster).
* Wenn Sie Daten in einen Azure-Datenspeicher kopieren, nutzen Sie die Auflistung der Compute-IP-Adressbereiche unter [Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653), um die von den Azure-Rechenzentren verwendeten Compute-IP-Adressen und SQL-Bereiche anzuzeigen.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten verwendet werden, die für den Amazon Redshift-Connector spezifisch sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Amazon Redshift verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AmazonRedshift** | Ja |
| server |IP-Adresse oder Hostname des Amazon Redshift-Servers. |Ja |
| port |Die Nummer des TCP-Ports, den der Amazon Redshift-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein, Standard = 5439 |
| database |Der Name der Amazon Redshift-Datenbank. |Ja |
| username |Der Name des Benutzers, der Zugriff auf die Datenbank hat. |Ja |
| password |Kennwort für das Benutzerkonto. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Amazon Redshift-Dataset unterstützt werden.

Zum Kopieren von Daten aus Amazon Redshift werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **AmazonRedshiftTable** | Ja |
| schema | Name des Schemas. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| table | Der Name der Tabelle. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| tableName | Name der Tabelle mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie `schema` und `table` für eine neue Workload. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "AmazonRedshiftTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Wenn Sie das Datenset vom Typ `RelationalTable` verwenden, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig die neue Version zu verwenden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Amazon Redshift-Quelle unterstützt werden.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift als Quelle

Legen Sie zum Kopieren von Daten aus Amazon Redshift den Quelltyp in der Kopieraktivität auf **AmazonRedshiftSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **AmazonRedshiftSource** | Ja |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. Beispiel: select * from MyTable. |Nein (wenn „tableName“ im Dataset angegeben ist) |
| redshiftUnloadSettings | Eigenschaftengruppe bei Verwendung von Amazon Redshift UNLOAD. | Nein |
| s3LinkedServiceName | Bezieht sich auf eine Amazon S3-Instanz, die als Zwischenspeicher verwendet wird, indem der Name eines verknüpften Diensts des Typs „AmazonS3“ angegeben wird. | Ja, wenn UNLOAD verwendet wird |
| bucketName | Gibt den S3-Bucket zum Speichern der vorläufigen Daten an. Bei fehlender Angabe wird der Data Factory-Dienst automatisch generiert.  | Ja, wenn UNLOAD verwendet wird |

**Beispiel: Amazon Redshift-Quelle in der Kopieraktivität mit UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Erfahren Sie mehr darüber, wie Sie mithilfe von UNLOAD effizient Daten aus einem Amazon Redshift aus dem nächsten Abschnitt kopieren.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Verwenden von UNLOAD zum Kopieren von Daten aus Amazon Redshift

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) ist ein von Amazon Redshift bereitgestellter Mechanismus, mit dem die Ergebnisse einer Abfrage in eine oder mehrere Dateien in Amazon Simple Storage Service (Amazon S3) entladen werden können. Dieser Mechanismus wird von Amazon zum Kopieren großer DataSets aus Redshift empfohlen.

**Beispiel: Kopieren von Daten aus Amazon Redshift in Azure Synapse Analytics mithilfe von UNLOAD, gestaffeltem Kopieren und PolyBase**

In diesem Beispiel für einen Anwendungsfall werden bei der Kopieraktivität Daten entsprechend der Konfiguration in „redshiftUnloadSettings“ aus Amazon Redshift in Amazon S3 entladen, dann entsprechend der Angabe in „stagingSettings“ Daten aus Amazon S3 in Azure Blob kopiert und schließlich Daten mithilfe von PolyBase in Azure Synapse Analytics geladen. Die gesamte vorläufige Formatierung wird von der Kopieraktivität ordnungsgemäß verarbeitet.

![Redshift zu Azure Synapse Analytics-Workflow zum Kopieren](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Datentypzuordnung für Amazon Redshift

Beim Kopieren von Daten aus Amazon Redshift werden die folgenden Zuordnungen von Amazon Redshift-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| Amazon Redshift-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| bigint |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATE |Datetime |
| DECIMAL |Decimal |
| DOUBLE PRECISION |Double |
| INTEGER |Int32 |
| real |Single |
| SMALLINT |Int16 |
| TEXT |String |
| timestamp |Datetime |
| VARCHAR |String |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
