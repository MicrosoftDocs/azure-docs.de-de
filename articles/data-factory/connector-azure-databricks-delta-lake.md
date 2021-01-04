---
title: Kopieren von Daten in und aus Azure Databricks Delta Lake
description: Es wird beschrieben, wie Sie Daten mit einer Kopieraktivität in einer Azure Data Factory-Pipeline für Azure Databricks Delta Lake kopieren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/24/2020
ms.openlocfilehash: c7e8f96e7917173aaec308b8ae5218684a722483
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507471"
---
# <a name="copy-data-to-and-from-azure-databricks-delta-lake-by-using-azure-data-factory"></a>Kopieren von Daten in und aus Azure Databricks Delta Lake mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten nach und aus Azure Databricks Delta Lake zu kopieren. Er baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Databricks Delta Lake-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit einer Tabelle zur [unterstützten Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Im Allgemeinen bietet Azure Data Factory Unterstützung für Delta Lake mit den folgenden Funktionen, um Ihren verschiedenen Anforderungen zu entsprechen.

- Die Kopieraktivität unterstützt den Azure Databricks Delta Lake-Connector beim Kopieren von Daten aus einem beliebigen unterstützten Quelldatenspeicher in die Azure Databricks Delta Lake-Tabelle und aus der Delta Lake-Tabelle in einen beliebigen unterstützten Senkendatenspeicher. Sie nutzt Ihren Databricks-Cluster zur Durchführung der Datenverschiebung. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites).
- Der [Zuordnungsdatenfluss](concepts-data-flow-overview.md) unterstützt das generische [Delta-Format](format-delta.md) für Azure Storage als Quelle und Senke zum Lesen und Schreiben von Delta-Dateien für ETL-Vorgänge ohne Code und wird unter der verwalteten Azure Integration Runtime ausgeführt.
- [Databricks-Aktivitäten](transform-data-databricks-notebook.md) unterstützen zusätzlich zu Delta Lake die Orchestrierung Ihrer codezentrischen ETL- oder Machine Learning-Workload.

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Azure Databricks Delta Lake-Connector zu verwenden, müssen Sie einen Cluster in Azure Databricks einrichten.

- Zum Kopieren von Daten nach Delta Lake ruft die Kopieraktivität den Azure Databricks-Cluster auf, um Daten aus einem Azure Storage zu lesen, bei dem es sich entweder um Ihre ursprüngliche Quelle oder einen Stagingbereich handelt, in den Data Factory die Quelldaten zunächst über eine integrierte gestaffelte Kopie schreibt. Weitere Informationen finden Sie unter [Delta Lake als Quelle](#delta-lake-as-source).
- Ähnlich verhält es sich mit dem Kopieren von Daten aus Delta Lake, wobei die Kopieraktivität den Azure Databricks-Cluster aufruft, um Daten in einen Azure Storage zu schreiben, bei dem es sich entweder um Ihre ursprüngliche Senke oder einen Stagingbereich handelt, von dem aus Data Factory weiterhin Daten über eine integrierte gestaffelte Kopie in die endgültige Senke schreibt. Weitere Informationen finden Sie unter [Delta Lake als Senke](#delta-lake-as-sink).

Der Databricks-Cluster muss Zugriff auf das Azure Blob- oder Azure Data Lake Storage Gen2-Konto haben, sowohl auf den Speichercontainer/das Dateisystem, der/das für Quelle/Senke/Staging verwendet wird, als auch auf den Container/das Dateisystem, in den/das Sie die Delta Lake-Tabellen schreiben möchten.

- Zur Verwendung von **Azure Data Lake Storage Gen2** können Sie einen **Dienstprinzipal** oder **Zugriffsschlüssel für das Speicherkonto** auf dem Databricks-Cluster im Rahmen der Apache Spark-Konfiguration konfigurieren. Befolgen Sie die Schritte in [Direkter Zugriff mit Dienstprinzipal](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-with-service-principal-and-oauth-20) oder [Direkter Zugriff mithilfe des Zugriffsschlüssels für das Speicherkonto](/azure/databricks/data/data-sources/azure/azure-datalake-gen2#--access-directly-using-the-storage-account-access-key).

- Zur Verwendung von **Azure-Blobspeicher** können Sie einen **Zugriffsschlüssel für das Speicherkonto** oder ein **SAS-Token** auf dem Databricks-Cluster im Rahmen der Apache Spark-Konfiguration konfigurieren. Befolgen Sie die Schritte in [Zugreifen auf Azure-Blobspeicher mithilfe der RDD-API](/azure/databricks/data/data-sources/azure/azure-storage#access-azure-blob-storage-using-the-rdd-api).

Wenn der von Ihnen konfigurierte Cluster während der Ausführung der Kopieraktivität beendet wurde, startet Data Factory ihn automatisch. Wenn Sie eine Pipeline mithilfe der Data Factory-Erstellungsbenutzeroberfläche erstellen, benötigen Sie für Vorgänge wie die Datenvorschau einen aktiven Cluster. Data Factory startet den Cluster nicht in Ihrem Auftrag.

#### <a name="specify-the-cluster-configuration"></a>Angeben der Clusterkonfiguration

1. Wählen Sie in der Dropdownliste **Clustermodus** die Option **Standard** aus.

2. Wählen Sie in der Dropdownliste **Databricks-Runtimeversion** eine Databricks-Runtimeversion aus.

3. Aktivieren Sie [Automatisches Optimieren](/azure/databricks/delta/optimizations/auto-optimize), indem Sie die folgenden Eigenschaften zu Ihrer [Spark-Konfiguration](/azure/databricks/clusters/configure#spark-config) hinzufügen:

   ```
   spark.databricks.delta.optimizeWrite.enabled true
   spark.databricks.delta.autoCompact.enabled true
   ```

4. Konfigurieren Sie Ihren Cluster je nach Ihren Integrations- und Skalierungsanforderungen.

Weitere Informationen zur Clusterkonfiguration finden Sie unter [Konfigurieren von Clustern](/azure/databricks/clusters/configure).

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für einen Azure Databricks Delta Lake-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgenden Eigenschaften werden für einen mit Azure Databricks Delta Lake verknüpften Dienst unterstützt.

| Eigenschaft    | Beschreibung                                                  | Erforderlich |
| :---------- | :----------------------------------------------------------- | :------- |
| type        | Die type-Eigenschaft muss auf **AzureDatabricksDeltaLake** festgelegt werden. | Ja      |
| Domäne      | Geben Sie die Azure Databricks-Arbeitsbereichs-URL an, z. B. `https://adb-xxxxxxxxx.xx.azuredatabricks.net`. |          |
| clusterId   | Geben Sie die Cluster-ID eines vorhandenen Clusters an. Dabei sollte es sich um einen bereits erstellten interaktiven Cluster handeln. <br>Sie finden die Cluster-ID eines interaktiven Clusters unter: Databricks-Arbeitsbereich -> Cluster -> Name des interaktiven Clusters -> Konfiguration -> Tags. [Weitere Informationen](/azure/databricks/clusters/configure#cluster-tags). |          |
| accessToken | Für die Authentifizierung bei Azure Databricks ist ein Zugriffstoken erforderlich. Das Zugriffstoken muss im Databricks-Arbeitsbereich generiert werden. Ausführlichere Informationen zum Auffinden des Zugriffstokens finden Sie [hier](/azure/databricks/dev-tools/api/latest/authentication#generate-token). |          |
| connectVia  | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet wird. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (wenn sich der Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein       |

**Beispiel:**

```json
{
    "name": "AzureDatabricksDeltaLakeLinkedService",
    "properties": {
        "type": "AzureDatabricksDeltaLake",
        "typeProperties": {
            "domain": "https://adb-xxxxxxxxx.xx.azuredatabricks.net",
            "clusterId": "<cluster id>",
            "accessToken": {
                "type": "SecureString", 
                "value": "<access token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

Folgende Eigenschaften werden für das Azure Databricks Delta Lake-Dataset unterstützt.

| Eigenschaft  | Beschreibung                                                  | Erforderlich                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | Die „type“-Eigenschaft des Datasets muss auf **AzureDatabricksDeltaLakeDataset** festgelegt werden. | Ja                         |
| database | Der Name der Datenbank. |Quelle: Nein, Senke: Ja  |
| table | Der Name der Delta-Tabelle. |Quelle: Nein, Senke: Ja  |

**Beispiel:**

```json
{
    "name": "AzureDatabricksDeltaLakeDataset",
    "properties": {
        "type": "AzureDatabricksDeltaLakeDataset",
        "typeProperties": {
            "database": "<database name>",
            "table": "<delta table name>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Databricks Delta Lake-Quelle und -Senke unterstützt werden.

### <a name="delta-lake-as-source"></a>Delta Lake als Quelle

Beim Kopieren von Daten aus Azure Databricks Delta Lake werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt.

| Eigenschaft                     | Beschreibung                                                  | Erforderlich |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureDatabricksDeltaLakeSource** festgelegt werden. | Ja      |
| Abfrage          | Geben Sie die SQL-Abfrage an, um Daten zu lesen. Für die Zeitreisesteuerung folgen Sie dem folgenden Muster:<br>- `SELECT * FROM events TIMESTAMP AS OF timestamp_expression`<br>- `SELECT * FROM events VERSION AS OF version` | Nein       |
| exportSettings | Erweiterte Einstellungen, die zum Abrufen von Daten aus der Delta-Tabelle verwendet werden. | Nein       |
| ***Unter `exportSettings`:** _ |  |  |
| type | Der Typ des Exportbefehls, festgelegt auf _*AzureDatabricksDeltaLakeExportCommand**. | Ja |
| dateFormat | Formatieren Sie den Datumstyp in eine Zeichenfolge mit einem Datumsformat. Benutzerdefinierte Datumsformate folgen den Formaten unter [datetime-Mustern](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Wenn keine Angabe erfolgt, wird der Standardwert `yyyy-MM-dd` verwendet. | Nein |
| timestampFormat | Formatieren Sie den Zeitstempeltyp in eine Zeichenfolge mit einem Zeitstempelformat. Benutzerdefinierte Datumsformate folgen den Formaten unter [datetime-Mustern](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Wenn keine Angabe erfolgt, wird der Standardwert `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` verwendet. | Nein |

#### <a name="direct-copy-from-delta-lake"></a>Direkte Kopie aus Delta Lake

Wenn der Senkendatenspeicher und das Format die in diesem Abschnitt beschriebenen Kriterien erfüllen, können Sie mit der Kopieraktivität Kopiervorgänge direkt aus der Azure Databricks Delta-Tabelle in die Senke durchführen. Data Factory überprüft die Einstellungen und gibt bei der Ausführung der Kopieraktivität einen Fehler aus, wenn die folgenden Kriterien nicht erfüllt werden:

- Der **verknüpfte Senkendienst** ist [Azure-Blobspeicher](connector-azure-blob-storage.md) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Die Anmeldeinformation für das Konto sollten in der Clusterkonfiguration von Azure Databricks vorkonfiguriert sein. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

- Das **Senkendatenformat** lautet **Parquet**, **Durch Trennzeichen getrennter Text** oder **Avro** mit den folgenden Konfigurationen und verweist auf einen Ordner anstatt auf eine Datei.

    - Beim Format **Parquet** wird einer der Komprimierungscodecs **None**, **Snappy** oder **GZIP** verwendet.
    - Beim Format **Durch Trennzeichen getrennter Text**:
        - `rowDelimiter` ist ein beliebiges einzelnes Zeichen.
        - `compression` kann **None**, **BZIP2**, **GZIP** sein.
        - `encodingName` UTF-7 wird nicht unterstützt.
    - Beim Format **Avro** wird einer der Komprimierungscodecs **None**, **Deflate** oder **Snappy** verwendet.

- In der Quelle der Kopieraktivität ist `additionalColumns` nicht angegeben.
- Wenn Daten in durch Trennzeichen getrennter Text kopiert werden, muss `fileExtension` in der Kopieraktivitätssenke „.csv“ sein.
- In der Zuordnung der Kopieraktivität ist die Typkonvertierung nicht aktiviert.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-delta-lake"></a>Gestaffelte Kopie aus Delta Lake

Wenn Ihr Senkendatenspeicher oder das Format nicht den Kriterien für die direkte Kopie entspricht, wie im letzten Abschnitt erwähnt, aktivieren Sie die integrierte gestaffelte Kopie mithilfe einer Azure Storage-Zwischeninstanz. Das gestaffelte Kopieren bietet auch einen höheren Durchsatz. Data Factory exportiert Daten aus Azure Databricks Delta Lake in den Stagingspeicher, kopiert dann die Daten in die Senke und bereinigt schließlich die temporären Daten aus dem Stagingspeicher. Ausführliche Informationen zum Kopieren von Daten mithilfe von Staging finden Sie unter [Gestaffeltes Kopieren](copy-activity-performance-features.md#staged-copy).

Um dieses Feature verwenden zu können, erstellen Sie einen [mit Azure Blob Storage verknüpften Dienst](connector-azure-blob-storage.md#linked-service-properties) oder einen [mit Azure Data Lake Storage Gen2 verknüpften Dienst](connector-azure-data-lake-storage.md#linked-service-properties), der auf das Speicherkonto als Zwischenbereich verweist. Geben Sie dann die Eigenschaften `enableStaging` und `stagingSettings` in der Kopieraktivität an.

>[!NOTE]
>Die Anmeldeinformation für das Konto des Stagingspeichers sollten in der Clusterkonfiguration von Azure Databricks vorkonfiguriert sein. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delta lake input dataset name>",
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
                "type": "AzureDatabricksDeltaLakeSource",
                "sqlReaderQuery": "SELECT * FROM events TIMESTAMP AS OF timestamp_expression"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="delta-lake-as-sink"></a>Delta Lake als Senke

Beim Kopieren von Daten in Azure Databricks Delta Lake werden die folgenden Eigenschaften im Abschnitt **sink** der Kopieraktivität unterstützt.

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| :------------ | :----------------------------------------------------------- | :------- |
| type          | Die type-Eigenschaft der Senke der Kopieraktivität ist auf **AzureDatabricksDeltaLakeSink** festgelegt. | Ja      |
| preCopyScript | Geben Sie eine SQL-Abfrage an, die bei jeder Ausführung von der Kopieraktivität ausgeführt werden soll, bevor Daten in die Databricks-Delta-Tabelle geschrieben werden. Sie können diese Eigenschaft verwenden, um die vorinstallierten Daten zu bereinigen oder eine „truncate table“- oder „Vacuum“-Anweisung hinzuzufügen. | Nein       |
| importSettings | Erweiterte Einstellungen, die zum Schreiben von Daten in eine Delta-Tabelle verwendet werden. | Nein |
| **_Unter `importSettings`:_* _ |                                                              |  |
| type | Der Typ des Importbefehls, festgelegt auf _*AzureDatabricksDeltaLakeImportCommand**. | Ja |
| dateFormat | Formatieren Sie die Zeichenfolge in einen Datumstyp mit einem Datumsformat. Benutzerdefinierte Datumsformate folgen den Formaten unter [datetime-Mustern](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Wenn keine Angabe erfolgt, wird der Standardwert `yyyy-MM-dd` verwendet. | Nein |
| timestampFormat | Formatieren Sie die Zeichenfolge in einen Zeitstempeltyp mit einem Zeitstempelformat. Benutzerdefinierte Datumsformate folgen den Formaten unter [datetime-Mustern](https://spark.apache.org/docs/latest/sql-ref-datetime-pattern.html). Wenn keine Angabe erfolgt, wird der Standardwert `yyyy-MM-dd'T'HH:mm:ss[.SSS][XXX]` verwendet. | Nein |

#### <a name="direct-copy-to-delta-lake"></a>Direktes Kopieren nach Delta Lake

Wenn der Quelldatenspeicher und das Format die in diesem Abschnitt beschriebenen Kriterien erfüllen, können Sie mit der Kopieraktivität Kopiervorgänge direkt aus der Quelle in Azure Databricks Delta Lake durchführen. Azure Data Factory überprüft die Einstellungen und gibt bei der Ausführung der Kopieraktivität einen Fehler aus, wenn die folgenden Kriterien nicht erfüllt werden:

- Der **verknüpfte Quelldienst** ist [Azure-Blobspeicher](connector-azure-blob-storage.md) oder [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md). Die Anmeldeinformation für das Konto sollten in der Clusterkonfiguration von Azure Databricks vorkonfiguriert sein. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

- Das **Quelldatenformat** lautet **Parquet**, **Durch Trennzeichen getrennter Text** oder **Avro** mit den folgenden Konfigurationen und verweist auf einen Ordner anstatt auf eine Datei.

    - Beim Format **Parquet** wird einer der Komprimierungscodecs **None**, **Snappy** oder **GZIP** verwendet.
    - Beim Format **Durch Trennzeichen getrennter Text**:
        - `rowDelimiter` ist „default“ oder ein beliebiges einzelnes Zeichen.
        - `compression` kann **None**, **BZIP2**, **GZIP** sein.
        - `encodingName` UTF-7 wird nicht unterstützt.
    - Beim Format **Avro** wird einer der Komprimierungscodecs **None**, **Deflate** oder **Snappy** verwendet.

- In der Quelle der Kopieraktivität: 

    - `wildcardFileName` enthält nur das Platzhalterzeichen `*`, aber nicht `?`, und `wildcardFolderName` ist nicht angegeben.
    - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` und `enablePartitionDiscovery` sind nicht angegeben.
    - `additionalColumns` ist nicht angegeben.

- In der Zuordnung der Kopieraktivität ist die Typkonvertierung nicht aktiviert.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            }
        }
    }
]
```

#### <a name="staged-copy-to-delta-lake"></a>Gestaffeltes Kopieren nach Delta Lake

Wenn Ihr Quelldatenspeicher oder das Format nicht den Kriterien für die direkte Kopie entspricht, wie im letzten Abschnitt erwähnt, aktivieren Sie die integrierte gestaffelte Kopie mithilfe einer Azure Storage-Zwischeninstanz. Das gestaffelte Kopieren bietet auch einen höheren Durchsatz. Data Factory konvertiert die Daten automatisch in einen Stagingspeicher, um die Anforderungen an das Datenformat zu erfüllen, und lädt die Daten von dort aus in Delta Lake. Abschließend werden Sie die temporären Daten im Speicher bereinigt. Ausführliche Informationen zum Kopieren von Daten mithilfe von Staging finden Sie unter [Gestaffeltes Kopieren](copy-activity-performance-features.md#staged-copy).

Um dieses Feature verwenden zu können, erstellen Sie einen [mit Azure Blob Storage verknüpften Dienst](connector-azure-blob-storage.md#linked-service-properties) oder einen [mit Azure Data Lake Storage Gen2 verknüpften Dienst](connector-azure-data-lake-storage.md#linked-service-properties), der auf das Speicherkonto als Zwischenbereich verweist. Geben Sie dann die Eigenschaften `enableStaging` und `stagingSettings` in der Kopieraktivität an.

>[!NOTE]
>Die Anmeldeinformation für das Konto des Stagingspeichers sollten in der Clusterkonfiguration von Azure Databricks vorkonfiguriert sein. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToDeltaLake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Delta lake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDatabricksDeltaLakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="monitoring"></a>Überwachung

Azure Data Factory bietet die gleiche [Erfahrung bei der Überwachung von Kopieraktivitäten](copy-activity-monitoring.md) wie andere Connectors. Da das Laden von Daten von/nach Delta Lake auf Ihrem Azure Databricks-Cluster ausgeführt wird, können Sie darüber hinaus [detaillierte Clusterprotokolle](/azure/databricks/clusters/clusters-manage#--view-cluster-logs) anzeigen und die [Leistung überwachen](/azure/databricks/clusters/clusters-manage#--monitor-performance).

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).