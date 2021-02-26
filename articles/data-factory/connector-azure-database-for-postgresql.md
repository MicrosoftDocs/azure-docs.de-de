---
title: Kopieren und Transformieren von Daten in Azure Database for PostgreSQL
description: Erfahren Sie, wie Sie Daten mit Azure Data Factory in Azure Database for PostgreSQL kopieren und transformieren.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/01/2021
ms.openlocfilehash: 32c65a3e1063b29ab6458151aec42e4415a73b62
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381323"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Database for PostgreSQL mit Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Copy-Aktivität in Azure Data Factory aus und in Azure Database for PostgreSQL kopieren sowie Daten mit einem Datenfluss in Azure Database for PostgreSQL transformieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

Dieser Connector ist speziell auf den Dienst [Azure Database for PostgreSQL](../postgresql/overview.md) ausgelegt. Wenn Sie Daten aus einer generischen PostgreSQL-Datenbank in der lokalen Umgebung oder in der Cloud kopieren möchten, verwenden Sie den [PostgreSQL-Connector](connector-postgresql.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Database for PostgreSQL-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten für den Azure Database for PostgreSQL-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den verknüpften Azure Database for PostgreSQL-Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
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

***Speichern des Kennworts in Azure Key Vault***

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

Legen Sie die „type“-Eigenschaft des Datasets auf **AzurePostgreSQLTable** fest, um Daten aus Azure Database for PostgreSQL zu kopieren. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
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

| Eigenschaft | Beschreibung | Erforderlich |
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

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf **AzurePostgreSQLSink** festgelegt sein. | Ja |
| preCopyScript | Geben Sie eine SQL-Abfrage für die Kopieraktivität an, die bei jeder Ausführung ausgeführt wird, bevor Daten in Azure Database for PostgreSQL geschrieben werden. Sie können diese Eigenschaft nutzen, um die vorab geladenen Daten zu bereinigen. | Nein |
| writeMethod | Die Methode zum Schreiben von Daten in Azure Database for PostgreSQL.<br>Zulässige Werte sind: **CopyCommand** (Vorschau mit höherer Leistung), **BulkInsert** (Standard). | Nein |
| writeBatchSize | Die Anzahl von Zeilen, die pro Batch in Azure Database for PostgreSQL geladen werden.<br>Als Wert ist ein Integer zulässig, der die Anzahl der Zeilen angibt. | Nein (Standardwert: 1.000.000) |
| writeBatchTimeout | Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird.<br>Zulässige Werte sind Timespan-Zeichenfolgen. Beispiel: 00:30:00 (30 Minuten). | Nein (Standardwert: 00:30:00) |

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
                "writeMethod": "CopyCommand",
                "writeBatchSize": 1000000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten im Zuordnungsdatenfluss können Sie Tabellen in Azure Database for PostgreSQL lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Zuordnungsdatenflüssen. Sie können ein Azure Database for PostgreSQL-Dataset oder ein [Inlinedataset](data-flow-source.md#inline-datasets) als Quell- und Senkentyp verwenden.

### <a name="source-transformation"></a>Quellentransformation

In der folgenden Tabelle sind die von der Azure Database for PostgreSQL-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabelle | Wenn Sie „Tabelle“ als Eingabe auswählen, ruft der Datenfluss alle Daten aus der im Dataset angegebenen Tabelle ab. | Nein | - |*(nur für Inlinedataset)*<br>tableName |
| Abfrage | Wenn Sie „Abfrage“ als Eingabe auswählen, geben Sie eine SQL-Abfrage zum Abrufen von Daten aus der Quelle an, die Vorrang vor jeder im Dataset angegebenen Tabelle hat. Die Verwendung von Abfragen stellt eine gute Möglichkeit dar, um die Zeilen für Tests oder Suchvorgänge zu verringern.<br><br>Die **Order By**-Klausel wird nicht unterstützt. Sie können aber eine vollständige SELECT FROM-Anweisung festlegen. Sie können auch benutzerdefinierte Tabellenfunktionen verwenden. **select * from udfGetData()** ist eine benutzerdefinierte Funktion in SQL, mit der eine Tabelle zurückgegeben wird, die Sie im Datenfluss verwenden können.<br>Abfragebeispiel: `select * from mytable where customerId > 1000 and customerId < 2000` oder `select * from "MyTable"`. Beachten Sie, dass in PostgreSQL bei einem Entitätsnamen ohne Anführungszeichen die Groß-/Kleinschreibung nicht berücksichtigt wird.| Nein | String | Abfrage |
| Batchgröße | Geben Sie eine Batchgröße an, um große Datenmengen in Batches zu segmentieren. | Nein | Integer | batchSize |
| Isolationsstufe | Wählen Sie eine der folgenden Isolationsstufen aus:<br>– Lesen zugesichert<br>– Lesen nicht zugesichert (Standard)<br>– Wiederholbarer Lesevorgang<br>– Serialisierbar<br>– Keine (Isolationsstufe ignorieren) | Nein | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NONE</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>Azure Database for PostgreSQL: Beispiel für ein Quellskript

Wenn Sie Azure Database for PostgreSQL als Quelltyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>Senkentransformation

In der folgenden Tabelle sind die von der Azure Database for PostgreSQL-Senke unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Senkenoptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Updatemethode | Geben Sie an, welche Vorgänge für das Datenbankziel zulässig sind. Standardmäßig sind lediglich Einfügevorgänge zulässig.<br>Um Aktualisierungs-, Upsert- oder Löschaktionen auf Zeilen anzuwenden, muss eine [Zeilenänderungstransformation](data-flow-alter-row.md) zum Kennzeichnen von Zeilen für diese Aktionen erfolgen. | Ja | `true` oder `false` | deletable <br/>insertable <br/>updateable <br/>upsertable |
| Schlüsselspalten | Für Update-, Upsert- und Löschvorgänge müssen Schlüsselspalten festgelegt werden, um die Zeile zu bestimmen, die geändert werden soll.<br>Der Spaltenname, den Sie als Schlüssel auswählen, wird als Teil der nachfolgenden Update-, Upsert- und Löschvorgänge verwendet. Daher müssen Sie eine Spalte auswählen, die in der Senkenzuordnung vorhanden ist. | Nein | Array | keys |
| Schreiben von Schlüsselspalten überspringen | Wenn Sie den Wert nicht in die Schlüsselspalte schreiben möchten, wählen Sie „Schreiben von Schlüsselspalten überspringen“ aus. | Nein | `true` oder `false` | skipKeyWrites |
| Aktion table |Bestimmt, ob die Zieltabelle vor dem Schreiben neu erstellt werden soll oder alle Zeilen aus der Zieltabelle entfernt werden sollen.<br>- **Keine**: Es wird keine Aktion an der Tabelle vorgenommen.<br>- **Neu erstellen**: Die Tabelle wird gelöscht und neu erstellt. Erforderlich, wenn eine neue Tabelle dynamisch erstellt wird.<br>- **Abschneiden**: Alle Zeilen werden aus der Zieltabelle entfernt. | Nein | `true` oder `false` | Neu erstellen<br/>truncate |
| Batchgröße | Geben Sie an, wie viele Zeilen in die einzelnen Batches geschrieben werden. Durch größere Batches werden zwar Komprimierung und Arbeitsspeicheroptimierung verbessert, beim Zwischenspeichern von Daten besteht aber die Gefahr, dass Ausnahmen wegen unzureichenden Arbeitsspeichers auftreten. | Nein | Integer | batchSize |
| Pre- und Post-SQL-Skripts | Geben Sie mehrzeilige SQL-Skripts an, die ausgeführt werden, bevor Daten in die Senkendatenbank geschrieben werden (Vorverarbeitung) und danach (Nachbearbeitung). | Nein | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>Azure Database for PostgreSQL: Beispiel für ein Senkenskript

Wenn Sie Azure Database for PostgreSQL als Senkentyp verwenden, sieht das zugehörige Datenflussskript wie folgt aus:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
