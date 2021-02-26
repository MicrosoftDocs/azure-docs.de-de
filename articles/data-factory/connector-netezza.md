---
title: Kopieren von Daten aus Netezza mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus Netezza mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jingwang
ms.openlocfilehash: 4b12c1f24f389634004de3d487a693bc588a7241
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374353"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Kopieren von Daten aus Netezza mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Netezza zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Ein Szenario für die Datenmigration von Netezza zu Azure finden Sie unter [Verwenden von Azure Data Factory zum Migrieren von Daten von einem lokalen Netezza-Server zu Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Netezza-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)


Sie können Daten aus Netezza in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Der Netezza-Connector unterstützt das parallele Kopieren aus einer Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza).

Azure Data Factory bietet zum Herstellen von Konnektivität einen integrierten Treiber. Sie müssen Treiber für die Verwendung dieses Connectors nicht manuell installieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

Sie können mit dem .NET SDK, Python SDK, Azure PowerShell, der REST-API oder einer Azure Resource Manager-Vorlage eine Pipeline erstellen, die eine Kopieraktivität verwendet. Im [Tutorial zur Kopieraktivität](quickstart-create-data-factory-dot-net.md) finden Sie eine schrittweise Anleitung, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die spezifisch für den Netezza-Connector sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Netezza verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **Netezza** festgelegt werden. | Ja |
| connectionString | Eine ODBC-Verbindungszeichenfolge zum Herstellen einer Verbindung mit Netezza. <br/>Sie können auch das Kennwort in Azure Key Vault speichern und die `pwd`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Eine typische Verbindungszeichenfolge ist `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Die folgende Tabelle beschreibt weitere Eigenschaften, die Sie festlegen können:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| SecurityLevel | Der Sicherheitsgrad, den der Treiber für die Verbindung mit dem Datenspeicher verwendet. Der Treiber unterstützt SSL-Verbindungen mit unidirektionaler Authentifizierung unter Verwendung der SSL-Version 3. <br>Beispiel: `SecurityLevel=preferredSecured`. Diese Werte werden unterstützt:<br/>- **Nur ungesichert** (**onlyUnSecured**): Der Treiber verwendet SSL nicht.<br/>- **Bevorzugt ungesichert (preferredUnSecured) (Standard)** : Wenn der Server die Wahl zulässt, verwendet der Treiber SSL nicht. <br/>- **Bevorzugt gesichert (preferredSecured)** : Wenn der Server die Wahl zulässt, verwendet der Treiber SSL. <br/>- **Nur gesichert (onlySecured)** : Der Treiber stellt nur dann eine Verbindung her, wenn eine SSL-Verbindung verfügbar ist. | Nein |
| CaCertFile | Der vollständige Pfad zum vom Server verwendeten SSL-Zertifikat. Beispiel: `CaCertFile=<cert path>;`| Ja, wenn SSL aktiviert ist |

**Beispiel**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kennworts in Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die das Netezza-Dataset unterstützt.

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets](concepts-datasets-linked-services.md).

Legen Sie zum Kopieren von Daten aus Netezza die **type**-Eigenschaft des Datasets auf **NetezzaTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **NetezzaTable** | Ja |
| schema | Name des Schemas. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| table | Der Name der Tabelle. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| tableName | Name der Tabelle mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie `schema` und `table` für eine neue Workload. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die die Netezza-Quelle unterstützt.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Netezza mithilfe der Datenpartitionierung finden Sie unter [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza).

Legen Sie zum Kopieren von Daten aus Netezza den **Quelltyp** in der Kopieraktivität auf **NetezzaSource** fest. Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **NetezzaSource** festgelegt werden. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"` | Nein (wenn „tableName“ im Dataset angegeben ist) |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten aus Netezza geladen werden. <br>Zulässige Werte sind: **None** (Standardwert), **DataSlice** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (d.h. nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten aus einer Netezza-Datenbank durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| partitionColumnName | Geben Sie den Namen der Quellspalte als **Integer** an, der von der Bereichspartitionierung für den parallelen Kopiervorgang verwendet wird. Ohne Angabe wird der Primärschlüssel der Tabelle automatisch erkannt und als Partitionsspalte verwendet. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionColumnName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |
| partitionUpperBound | Der Höchstwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie ihn, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie Quelldaten per Abfrage abrufen, integrieren Sie `?AdfRangePartitionUpbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |
| partitionLowerBound | Der Mindestwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionLowbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Netezza](#parallel-copy-from-netezza). | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Paralleles Kopieren aus Netezza

Der Data Factory-Netezza-Connector verfügt über eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten aus Netezza. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

![Screenshot der Partitionierungsoptionen](./media/connector-netezza/connector-netezza-partition-options.png)

Wenn Sie partitioniertes Kopieren aktivieren, führt Data Factory parallele Abfragen für Ihre Netezza-Quelle aus, um Daten anhand von Partitionen zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie zum Beispiel `parallelCopies` auf vier festlegen, werden von Data Factory vier Abfragen gleichzeitig generiert und ausgeführt. Diese Abfragen basieren auf den von Ihnen angegebenen Partitionsoptionen und -einstellungen, und jede Abfrage ruft einen Teil der Daten aus Ihrer Netezza-Datenbank ab.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren, vor allem, wenn Sie große Datenmengen aus Ihrer Netezza-Datenbank laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben. (Geben Sie nur den Ordnernamen an.) In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus großer Tabelle                                   | **Partitionsoption:** Datenslice <br><br/>Während der Ausführung partitioniert Data Factory automatisch die Daten basierend auf den [integrierten Datenslices von Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html) und kopiert Daten nach Partitionen. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage                 | **Partitionsoption:** Datenslice<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`<br>Data Factory ersetzt während der Ausführung `?AdfPartitionCount` (bei festgelegter Anzahl paralleler Kopien für Kopieraktivität) und `?AdfDataSliceCondition` durch die Datenslice-Partitionierungslogik und sendet die Daten dann an Netezza. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage, wenn eine Integerspalte mit gleichmäßig verteilten Werten für die Bereichspartitionierung vorhanden ist | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Die Partitionierung kann auf der Grundlage der Spalte mit dem Datentyp „Integer“ erfolgen.<br>**Obergrenze der Partition** und **Untergrenze der Partition**: Geben Sie an, ob Sie anhand der Partitionsspalte filtern möchten, um nur Daten zwischen der Ober- und der Untergrenze zu erhalten.<br><br>Data Factory ersetzt während der Ausführung `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` und `?AdfRangePartitionLowbound` durch den tatsächlichen Spaltennamen und die Wertebereiche für die jeweilige Partition und sendet die Daten dann an Netezza. <br>Wenn Ihre Partitionsspalte „ID“ also beispielsweise mit der Untergrenze „1“ und der Obergrenze „80“ konfiguriert und paralleles Kopieren auf „4“ festgelegt ist, ruft Data Factory Daten anhand von vier Partitionen ab. Die ID-Bereiche sehen dann wie folgt aus: [1–20], [21–40], [41–60] und [61–80]. |

**Beispiel: Abfrage mit Datenslicepartition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
