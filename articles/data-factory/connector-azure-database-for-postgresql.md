---
title: Kopieren von Daten in und aus Azure Database for PostgreSQL
description: Hier erfahren Sie, wie Sie Daten mit einer Kopieraktivität in einer Azure Data Factory-Pipeline für Azure Database for PostgreSQL kopieren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296504"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopieren von Daten in und aus Azure Database for PostgreSQL mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Azure Database for PostgreSQL zu kopieren. Er baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

Dieser Connector ist speziell auf den Dienst [Azure Database for PostgreSQL](../postgresql/overview.md) ausgelegt. Wenn Sie Daten aus einer generischen PostgreSQL-Datenbank in der lokalen Umgebung oder in der Cloud kopieren möchten, verwenden Sie den [PostgreSQL-Connector](connector-postgresql.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Database for PostgreSQL-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Azure Database for PostgreSQL in beliebige unterstützte Senkendatenspeicher kopieren. Alternativ können Sie Daten auch aus jedem beliebigen unterstützten Quelldatenspeicher in Azure Database for PostgreSQL kopieren. Die Datenspeicher, die für die Kopieraktivität als Quellen und Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory bietet zum Herstellen von Konnektivität einen integrierten Treiber. Aus diesem Grund müssen Sie keinen Treiber für die Verwendung dieses Connectors manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten für den Azure Database for PostgreSQL-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den verknüpften Azure Database for PostgreSQL-Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzurePostgreSql**. | Ja |
| connectionString | Eine ODBC-Verbindungszeichenfolge zum Herstellen einer Verbindung mit Azure Database for PostgreSQL.<br/>Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Weitere Informationen finden Sie in den folgenden Beispielen sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Diese Eigenschaft gibt die [Integration Runtime](concepts-integration-runtime.md) an, die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet wird. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Eine typische Verbindungszeichenfolge ist `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`. Weitere Eigenschaften, die Sie für Ihren Fall festlegen können:

| Eigenschaft | BESCHREIBUNG | Tastatur | Erforderlich |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| Diese Methode wird vom Treiber verwendet, um Daten zu verschlüsseln, die zwischen dem Treiber und dem Datenbankserver gesendet werden. Beispiel: `EncryptionMethod=<0/1/6>;`| 0 (keine Verschlüsselung) **(Standard)** / 1 (SSL) / 6 (RequestSSL) | Nein |
| ValidateServerCertificate (VSC) | Bestimmt, ob der Treiber das Zertifikat überprüft, das vom Datenbankserver gesendet wird, wenn die SSL-Verschlüsselung aktiviert ist (Encryption Method=1). Beispiel: `ValidateServerCertificate=<0/1>;`| 0 (Deaktiviert) **(Standard)** / 1 (Aktiviert) | Nein |

**Beispiel:**

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**Beispiel:**

**_Speichern des Kennworts in Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets in Azure Data Factory](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die Azure Database for PostgreSQL in Datasets unterstützt.

Legen Sie die „type“-Eigenschaft des Datasets auf _*AzurePostgreSqlTable** fest, um Daten aus Azure Database for PostgreSQL zu kopieren. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf **AzurePostgreSqlTable** festgelegt werden. | Ja |
| tableName | Name der Tabelle | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel:**

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die in einer Azure Database for PostgreSQL-Quelle unterstützt werden.

### <a name="azure-database-for-postgresql-as-source"></a>Azure-Datenbank für PostgreSQL als Quelle

Legen Sie zum Kopieren von Daten aus Azure Database for PostgreSQL den Quelltyp in der Kopieraktivität auf **AzureMySqlSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **AzurePostgreSqlSource** festgelegt werden. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `SELECT * FROM mytable` oder `SELECT * FROM "MyTable"`. Beachten Sie, dass in PostgreSQL bei einem Entitätsnamen ohne Anführungszeichen die Groß-/Kleinschreibung nicht berücksichtigt wird. | Nein (wenn die tableName-Eigenschaft im Dataset angegeben ist) |

**Beispiel**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>Azure Database for PostgreSQL als Senke

Beim Kopieren von Daten nach Azure Database for PostgreSQL werden im Abschnitt **sink** der Kopieraktivität folgende Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **AzurePostgreSQLSink** festgelegt sein. | Ja |
| preCopyScript | Geben Sie eine SQL-Abfrage für die Kopieraktivität an, die bei jeder Ausführung ausgeführt wird, bevor Daten in Azure Database for PostgreSQL geschrieben werden. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. | Nein |
| writeBatchSize | Fügt Daten in die Azure Database for PostgreSQL-Tabelle ein, wenn die Puffergröße „writeBatchSize“ erreicht.<br>Als Wert ist ein Integer zulässig, der die Anzahl der Zeilen angibt. | Nein (Standardwert ist 10.000) |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br>Zulässige Werte sind Timespan-Zeichenfolgen. Beispiel: 00:30:00 (30 Minuten). | Nein (Standardwert ist 00:00:30) |

**Beispiel**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
