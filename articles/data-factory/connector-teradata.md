---
title: Kopieren von Daten aus Teradata Vantage mithilfe von Azure Data Factory
description: Mit dem Teradata-Connector des Data Factory-Diensts können Sie Daten aus einer Teradata  Vantage-Instanz in Datenspeicher kopieren, die von Data Factory als Senken unterstützt werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: jingwang
ms.openlocfilehash: c65b295dca16a3453b2ee5472b9a3ee5e8d62df7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381765"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Kopieren von Daten aus Teradata Vantage mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
>
> * [Version 1](v1/data-factory-onprem-teradata-connector.md)
> * [Aktuelle Version](connector-teradata.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel erfahren Sie, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Teradata Vantage zu kopieren. Der Artikel baut auf der [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Teradata-Connector wird für folgende Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Teradata Vantage in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Der Teradata-Connector unterstützt insbesondere Folgendes:

- Teradata, **Version 14.10, 15.0, 15.10, 16.0, 16.10 und 16.20**.
- Kopieren von Daten unter Verwendung der **Standard**-, **Windows**- oder **LDAP**-Authentifizierung.
- Paralleles Kopieren aus einer Teradata-Quelle. Weitere Informationen finden Sie im Abschnitt [Paralleles Kopieren aus Teradata](#parallel-copy-from-teradata).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Wenn Sie die selbstgehostete Integration Runtime verwenden, beachten Sie, dass ein integrierter Teradata-Treiber ab Version 3.18 enthalten ist. Es ist keine manuelle Treiberinstallation erforderlich. Für den Treiber muss „Visual C++ Redistributable 2012 Update 4“ auf dem Computer mit der selbstgehosteten Integration Runtime vorhanden sein. Sollte diese Komponente noch nicht installiert sein, können Sie sie [hier](https://www.microsoft.com/en-sg/download/details.aspx?id=30679) herunterladen.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren spezifischer Data Factory-Entitäten für den Teradata-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der verknüpfte Teradata-Dienst unterstützt folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ muss auf **Teradata** festgelegt sein. | Ja |
| connectionString | Gibt die erforderlichen Informationen für die Verbindungsherstellung mit der Teradata-Instanz an. Sehen Sie sich die folgenden Beispiele an.<br/>Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). | Ja |
| username | Geben Sie einen Benutzernamen für die Verbindungsherstellung mit Teradata an. Gilt bei Verwendung der Windows-Authentifizierung. | Nein |
| password | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. Sie können auch [auf ein in Azure Key Vault gespeichertes Geheimnis verweisen](store-credentials-in-key-vault.md). <br>Gilt, wenn Sie die Windows-Authentifizierung verwenden oder für die Standardauthentifizierung auf ein Kennwort in Key Vault verweisen. | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

Weitere Verbindungseigenschaften, die Sie abhängig von Ihrem Anwendungsfall in der Verbindungszeichenfolge festlegen können:

| Eigenschaft | BESCHREIBUNG | Standardwert |
|:--- |:--- |:--- |
| TdmstPortNumber | Die Nummer des Ports für den Zugriff auf die Teradata-Datenbank.<br>Ändern Sie diesen Wert nur, wenn Sie vom technischen Support dazu aufgefordert werden. | 1025 |
| UseDataEncryption | Mit dieser Eigenschaft wird angegeben, ob die gesamte Kommunikation mit der Teradata-Datenbank verschlüsselt werden soll. Zulässige Werte sind „0“ und „1“.<br><br/>- **0 (deaktiviert, Standardwert):** Nur Authentifizierungsinformationen werden verschlüsselt.<br/>- **1 (aktiviert):** Alle zwischen dem Treiber und der Datenbank übermittelten Daten werden verschlüsselt. | `0` |
| CharacterSet | Der Zeichensatz, der für die Sitzung verwendet werden soll. Beispiel: `CharacterSet=UTF16`.<br><br/>Bei diesem Wert kann es sich um einen benutzerdefinierten Zeichensatz oder um einen der folgenden vordefinierten Zeichensätze handeln: <br/>- ASCII<br/>- UTF8<br/>- UTF16<br/>- LATIN1252_0A<br/>- LATIN9_0A<br/>- LATIN1_0A<br/>- Shift-JIS (Windows, DOS-kompatibel, KANJISJIS_0S)<br/>- EUC (Unix-kompatibel, KANJIEC_0U)<br/>- IBM Mainframe (KANJIEBCDIC5035_0I)<br/>- KANJI932_1S0<br/>- BIG5 (TCHBIG5_1R0)<br/>- GB (SCHGB2312_1T0)<br/>- SCHINESE936_6R0<br/>- TCHINESE950_8R0<br/>- NetworkKorean (HANGULKSC5601_2R4)<br/>- HANGUL949_7R0<br/>- ARABIC1256_6A0<br/>- CYRILLIC1251_2A0<br/>- HEBREW1255_5A0<br/>- LATIN1250_1A0<br/>- LATIN1254_7A0<br/>- LATIN1258_8A0<br/>- THAI874_4A0 | `ASCII` |
| MaxRespSize |Die maximale Größe des Antwortpuffers für SQL-Anforderungen in Kilobytes (KB). Beispiel: `MaxRespSize=‭10485760‬`.<br/><br/>Ab der Teradata Database-Version 16.00 beträgt der Höchstwert „7361536“. Bei Verbindungen mit älteren Versionen beträgt der Höchstwert „1048576“. | `65536` |
| MechanismName | Um das LDAP-Protokoll zum Authentifizieren der Verbindung zu verwenden, geben Sie `MechanismName=LDAP` an. | – |

**Beispiel mit Standardauthentifizierung**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel mit Windows-Authentifizierung**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel mit LDAP-Authentifizierung**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;MechanismName=LDAP;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> Die folgende Nutzlast wird weiterhin unterstützt. In Zukunft sollte jedoch die neue verwendet werden.

**Vorherige Nutzlast:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Teradata-Dataset unterstützt werden. Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md).

Beim Kopieren von Daten aus Teradata werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ des Datasets muss auf `TeradataTable` festgelegt werden. | Ja |
| database | Der Name der Teradata-Instanz. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |
| table | Der Name der Tabelle in der Teradata-Instanz. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> Datasets vom Typ `RelationalTable` werden weiterhin unterstützt. Es wird jedoch empfohlen, das neue Dataset zu verwenden.

**Vorherige Nutzlast:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Quelle „Teradata“ unterstützt werden. Eine vollständige Liste der verfügbaren Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="teradata-as-source"></a>Teradata als Quelle

>[!TIP]
>Weitere Informationen zum effizienten Laden von Daten aus Teradata mithilfe der Datenpartitionierung finden Sie unter [Parallele Kopie von Teradata](#parallel-copy-from-teradata).

Beim Kopieren von Daten aus Teradata werden die folgenden Eigenschaften im Abschnitt **source** der Copy-Aktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft „type“ der Quelle der Kopieraktivität muss auf `TeradataSource` festgelegt werden. | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. z. B. `"SELECT * FROM MyTable"`.<br>Wenn Sie partitioniertes Laden aktivieren, müssen Sie die entsprechenden integrierten Partitionsparameter in Ihre Abfrage integrieren. Beispiele finden Sie im Abschnitt [Paralleles Kopieren aus Teradata](#parallel-copy-from-teradata). | Nein (wenn „table“ im Dataset angegeben ist) |
| partitionOptions | Gibt die Datenpartitionierungsoptionen an, mit denen Daten aus Teradata geladen werden. <br>Zulässige Werte sind: **None** (Standardwert), **Hash** und **DynamicRange**.<br>Wenn eine Partitionsoption aktiviert ist (nicht `None`), wird der Grad an Parallelität zum gleichzeitigen Laden von Daten aus Teradata durch die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) für die Kopieraktivität gesteuert. | Nein |
| partitionSettings | Geben Sie die Gruppe der Einstellungen für die Datenpartitionierung an. <br>Verwenden Sie diese Option, wenn die Partitionsoption nicht `None` lautet. | Nein |
| partitionColumnName | Geben Sie den Namen der Quellspalte an, die von der Bereichspartitionierung oder Hashpartitionierung für den parallelen Kopiervorgang verwendet wird. Wenn kein Wert angegeben ist, wird der primäre Index der Tabelle automatisch erkannt und als Partitionsspalte verwendet. <br>Verwenden Sie diese Option, wenn die Partitionsoption `Hash` oder `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfHashPartitionCondition` oder `?AdfRangePartitionColumnName` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Parallele Kopie von Teradata](#parallel-copy-from-teradata). | Nein |
| partitionUpperBound | Der Höchstwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie ihn, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie Quelldaten per Abfrage abrufen, integrieren Sie `?AdfRangePartitionUpbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Teradata](#parallel-copy-from-teradata). | Nein |
| partitionLowerBound | Der Mindestwert der Partitionsspalte zum Herauskopieren von Daten. <br>Verwenden Sie diese Option, wenn die Partitionsoption `DynamicRange` lautet. Wenn Sie die Quelldaten mithilfe einer Abfrage abrufen, integrieren Sie `?AdfRangePartitionLowbound` in die WHERE-Klausel. Ein Beispiel finden Sie im Abschnitt [Paralleles Kopieren aus Teradata](#parallel-copy-from-teradata). | Nein |

> [!NOTE]
>
> Kopierquellen vom Typ `RelationalSource` werden zwar weiterhin unterstützt, das neue integrierte parallele Laden aus Teradata (Partitionsoptionen) wird allerdings nicht unterstützt. Es wird jedoch empfohlen, das neue Dataset zu verwenden.

**Beispiel: Kopieren von Daten mit einer einfachen Abfrage ohne Partition**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
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
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Parallele Kopie von Teradata

Der Data Factory-Teradata-Connector verfügt über eine integrierte Datenpartitionierung zum parallelen Kopieren von Daten aus Teradata. Die Datenpartitionierungsoptionen befinden sich auf der Registerkarte **Quelle** der Kopieraktivität.

![Screenshot der Partitionierungsoptionen](./media/connector-teradata/connector-teradata-partition-options.png)

Wenn Sie die partitioniertes Kopieren aktivieren, führt Data Factory parallele Abfragen für Ihre Teradata-Quelle aus, um Daten anhand von Partitionen zu laden. Der Parallelitätsgrad wird über die Einstellung [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) der Kopieraktivität gesteuert. Wenn Sie zum Beispiel `parallelCopies` auf „4“ festlegen, werden von Data Factory gleichzeitig vier Abfragen generiert und ausgeführt. Diese Abfragen basieren auf den von Ihnen angegebenen Partitionsoptionen und -einstellungen, und jede Abfrage ruft einen Teil der Daten aus Ihrer Teradata-Instanz ab.

Es wird empfohlen, das parallele Kopieren mit Datenpartitionierung zu aktivieren. Das gilt insbesondere, wenn Sie große Datenmengen aus Ihrer Teradata-Instanz laden. Im Anschluss finden Sie empfohlene Konfigurationen für verschiedene Szenarien. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird empfohlen, mehrere Dateien in einen Ordner zu schreiben. (Geben Sie nur den Ordnernamen an.) In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

| Szenario                                                     | Empfohlene Einstellungen                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Vollständiges Laden aus großer Tabelle                                   | **Partitionsoption:** Hash <br><br/>Während der Ausführung erkennt Data Factory automatisch die Primärindexspalte, wendet einen Hash darauf an und kopiert Daten anhand von Partitionen. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage                 | **Partitionsoption:** Hash<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Hashpartitionierung an. Ohne Angabe erkennt Data Factory automatisch die PK-Spalte der Tabelle, die Sie im Teradata-Dataset angegeben haben.<br><br>Während der Ausführung ersetzt Data Factory `?AdfHashPartitionCondition` durch die Hashpartitionierungslogik und sendet die Daten an Teradata. |
| Laden einer großen Datenmenge unter Verwendung einer benutzerdefinierten Abfrage, wenn eine Integerspalte mit gleichmäßig verteilten Werten für die Bereichspartitionierung vorhanden ist | **Partitionsoptionen:** Dynamische Bereichspartitionierung<br>**Abfrage**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**Partitionsspalte:** Geben Sie die Spalte für die Datenpartitionierung an. Die Partitionierung kann auf der Grundlage der Spalte mit dem Datentyp „Integer“ erfolgen.<br>**Obergrenze der Partition** und **Untergrenze der Partition**: Geben Sie an, ob Sie anhand der Partitionsspalte filtern möchten, um nur Daten zwischen der Ober- und der Untergrenze zu erhalten.<br><br>Data Factory ersetzt während der Ausführung `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` und `?AdfRangePartitionLowbound` durch den tatsächlichen Spaltennamen und die Wertebereiche für die jeweilige Partition und sendet die Daten dann an Teradata. <br>Wenn Ihre Partitionsspalte „ID“ also beispielsweise mit der Untergrenze „1“ und der Obergrenze „80“ konfiguriert und paralleles Kopieren auf „4“ festgelegt ist, ruft Data Factory Daten anhand von vier Partitionen ab. Die ID-Bereiche sehen dann wie folgt aus: [1–20], [21–40], [41–60] und [61–80]. |

**Beispiel: Abfrage mit Hashpartition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**Beispiel: Abfrage mit dynamischer Bereichspartition**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Datentypzuordnung für Teradata

Beim Kopieren von Daten aus Teradata gelten folgende Zuordnungen. Weitere Informationen dazu, wie die Kopieraktivität das Quellschema und den Datentyp zur Senke zuordnet, finden Sie unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md).

| Teradata-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |String |
| Clob |String |
| Date |Datetime |
| Decimal |Decimal |
| Double |Double |
| Graphic |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Integer |Int32 |
| Interval Day |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Day To Hour |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Day To Minute |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Day To Second |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Hour |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Hour To Minute |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Hour To Second |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Minute |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Minute To Second |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Month |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Second |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Year |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Interval Year To Month |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Number |Double |
| Zeitraum (Datum) |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Zeitraum (Zeit) |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Zeitraum (Zeit mit Zeitzone) |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Zeitraum (Zeitstempel) |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Zeitraum (Zeitstempel mit Zeitzone) |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| SmallInt |Int16 |
| Time |TimeSpan |
| Time With Time Zone |TimeSpan |
| Timestamp |Datetime |
| Timestamp With Time Zone |Datetime |
| VarByte |Byte[] |
| VarChar |String |
| VarGraphic |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |
| Xml |Wird nicht unterstützt. Wenden Sie explizite Umwandlungen in der Quellabfrage an. |


## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
