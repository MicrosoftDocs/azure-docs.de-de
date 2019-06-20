---
title: Schemazuordnung in Kopieraktivität | Microsoft-Dokumentation
description: Erfahren Sie, wie Kopieraktivität in Azure Data Factory beim Kopieren von Daten Schemas und Datentypen aus Quelldaten Senkendaten zuordnet.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64875803"
---
# <a name="schema-mapping-in-copy-activity"></a>Schemazuordnung in Kopieraktivität

In diesem Artikel wird beschrieben, wie Kopieraktivität in Azure Data Factory Schemazuordnung und Datentypzuordnung von Quelldaten zu Daten der Empfangsquelle (Senkendaten) beim Kopieren der Daten ausführt.

## <a name="schema-mapping"></a>Schemazuordnung

Die Spaltenzuordnung wird angewendet, wenn Daten aus der Quelle in die Senke kopiert werden. Kopieren Sie standardmäßig die Aktivität **Quelldaten nach Spaltennamen zur Senke zuordnen**. Sie können [explizite Zuordnung](#explicit-mapping) angeben, um die Spaltenzuordnung an Ihre Anforderungen anzupassen. Genauer gesagt, macht Kopieraktivität Folgendes:

1. Lesen von Daten aus der Quelle und Bestimmen des Quellschemas
2. Verwenden Sie die standardmäßige Spaltenzuordnung, um Spalten nach Name zuzuordnen, oder wenden Sie eine explizite Spaltenzuordnung an, falls angegeben.
3. Schreiben der Daten in die Senke

### <a name="explicit-mapping"></a>Explizite Zuordnung

Sie können die in der Kopieraktivität zuzuordnenden Spalten angeben -> `translator` -> `mappings`-Eigenschaft. Das folgende Beispiel definiert eine Kopieraktivität in einer Pipeline, um Daten aus Text mit Trennzeichen nach Azure SQL-Datenbank zu kopieren.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

Die folgenden Eigenschaften werden unter `translator` -> `mappings` unterstützt -> Objekt mit `source` und `sink`:

| Eigenschaft | BESCHREIBUNG                                                  | Erforderlich |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Name der Quell- oder Senkenspalte.                           | Ja      |
| Ordinal  | Spaltenindex. Beginnen Sie mit 1. <br>Anwenden und erforderlich, wenn Text mit Trennzeichen und ohne Kopfzeile verwendet wird. | Nein       |
| path     | Der Ausdruck des JSON-Pfads für jedes Feld, das extrahiert oder zugeordnet werden soll. Anwenden auf hierarchische Daten, z. B. MongoDB/REST.<br>Der JSON-Pfad für Felder unter der Stammobjekt beginnt mit dem Stamm „$“. Für Felder innerhalb des von der `collectionReference`-Eigenschaften ausgewählten Arrays beginnt der JSON-Pfad mit dem Arrayelement. | Nein       |
| type     | Data Factory-Zwischendatentyp der Quell- oder Senkenspalte. | Nein       |
| culture  | Kultur der Quell- oder Senkenspalte. <br>Anwenden, wenn der Typ `Datetime` oder `Datetimeoffset` ist. Der Standardwert lautet `en-us`. | Nein       |
| format   | Zu verwendende Formatzeichenfolge, wenn der Typ `Datetime` oder `Datetimeoffset` ist. Informationen zum Formatieren von Datum und Uhrzeit finden Sie unter [Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). | Nein       |

Die folgenden Eigenschaften werden unter `translator` -> `mappings` unterstützt, zusätzlich zum Objekt mit `source` und `sink`:

| Eigenschaft            | BESCHREIBUNG                                                  | Erforderlich |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Wird nur unterstützt, wenn hierarchische Daten, z. B. MongoDB/REST, die Quelle sind.<br>Wenn Sie Daten durchlaufen und Objekte **innerhalb eines Arrayfelds** mit demselben Muster extrahieren, und wenn Sie möchten, dass jedes Objekt in einer neuen Zeile steht, geben Sie den JSON-Pfad dieses Arrays für die übergreifende Anwendung an. | Nein       |

### <a name="alternative-column-mapping"></a>Alternative Spaltenzuordnung

Sie können die Kopieraktivität -> `translator` -> `columnMappings` angeben, um zwischen tabellenförmigen Daten zu wechseln. In diesem Fall ist der Abschnitt „structure“ für Eingabe- und Ausgabedatasets erforderlich. Die Spaltenzuordnung unterstützt die **Zuordnung aller oder einer Teilmenge der Spalten in „structure“ des Quelldatasets zu allen Spalten in „structure“ des Senkendatasets**. Im Folgenden sind Fehlerbedingungen angegeben, die zu einer Ausnahme führen:

* Das Ergebnis der Abfrage des Quelldatenspeichers enthält keinen Spaltennamen, der im Abschnitt „structure“ des Eingabedatasets angegeben wird.
* Der Senkendatenspeicher (sofern mit vordefiniertem Schema) enthält keinen Spaltennamen, der im Abschnitt „structure“ des Ausgabedatasets angegeben wird.
* Entweder sind weniger Spalten oder mehr Spalten in „structure“ des Senkendatasets, als in der Zuordnung angegeben.
* Doppelte Zuordnung.

Im folgenden Beispiel verfügt das Eingabedataset über eine Struktur, und diese verweist auf eine Tabelle in einer lokalen Oracle-Datenbank.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

In diesem Beispiel verfügt das Ausgabedataset über eine Struktur, und diese verweist auf eine Tabelle in Salesforce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Der folgende JSON-Code definiert eine Kopieraktivität in einer Pipeline. Die Spalten der Quelle werden mithilfe der **translator** -> **columnMappings**-Eigenschaft den Spalten der Senke zugeordnet.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Wenn Sie zum Angeben der Spaltenzuordnung die Syntax `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` verwenden, wird sie weiterhin unverändert unterstützt.

### <a name="alternative-schema-mapping"></a>Alternative Schemazuordnung

Sie können die Kopieraktivität -> `translator` -> `schemaMapping` angeben, um zwischen hierarchisch und tabellarisch strukturierten Daten zuzuordnen, z. B. Kopieren aus MongoDB/REST in eine Textdatei oder aus Oracle in die Azure Cosmos DB-API für MongoDB. Folgende Eigenschaften werden im Abschnitt `translator` der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Kopieraktivität „translator“ muss auf Folgendes festgelegt werden: **TabularTranslator** | Ja |
| schemaMapping | Eine Sammlung von Schlüssel-Wert-Paaren, die die Zuordnungsbeziehung **von der Quelle zur Senke** darstellt.<br/>- **Schlüssel:** stellt die Quelle dar. Für eine **tabellarische Quelle** legen Sie den Spaltennamen wie in der Datasetstruktur definiert fest. Für eine **hierarchische Quelle** legen Sie den Ausdruck des JSON-Pfads für jedes zu extrahierende und zuzuordnende Feld fest.<br>- **Wert:** stellt die Senke dar. Für eine **tabellarische Senke** legen Sie den Spaltennamen wie in der Datasetstruktur definiert fest. Für eine **hierarchische Senke** legen Sie den Ausdruck des JSON-Pfads für jedes zu extrahierende und zuzuordnende Feld fest. <br>Bei hierarchischen Daten beginnt der JSON-Pfad für Felder unter der Stammobjekt mit dem Stamm „$“. Für Felder innerhalb des von der `collectionReference`-Eigenschaften ausgewählten Arrays beginnt der JSON-Pfad mit dem Array-Element.  | Ja |
| collectionReference | Wenn Sie Daten durchlaufen und Objekte **innerhalb eines Arrayfelds** mit demselben Muster extrahieren, und wenn Sie möchten, dass jedes Objekt in einer neuen Zeile steht, geben Sie den JSON-Pfad dieses Arrays für die übergreifende Anwendung an. Diese Eigenschaft wird nur unterstützt, wenn hierarchische Daten die Quelle sind. | Nein |

**Beispiel: Kopieren aus MongoDB nach Oracle:**

Sie besitzen zum Beispiel ein MongoDB-Dokument mit folgendem Inhalt:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

Wenn Sie es in eine Azure SQL-Tabelle im folgenden Format kopieren möchten, indem Sie die Daten im Array *(order_pd und order_price)* vereinfachen und mit den allgemeinen Stamminformationen *(Anzahl, Datum und Stadt)* überkreuzt verknüpfen, dann gilt Folgendes:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Konfigurieren Sie die Regel für die Schemazuordnung wie im folgenden JSON-Beispiel für die Kopieraktivität:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Datentypzuordnung

Kopieraktivität führt Zuordnungen von Quelltypen zu Senkentypen mit dem folgenden 2-Schritte-Ansatz durch:

1. Konvertieren von nativen Quelltypen in Azure Data Factory-Zwischendatentypen
2. Konvertieren von Azure Data Factory-Zwischendatentypen in nativen Senkentyp

Sie können die Zuordnung des nativen Typs zum Zwischendatentyp im „Datentypzuordnung“-Abschnitt jedes Connectorthemas finden.

### <a name="supported-data-types"></a>Unterstützte Datentypen

Data Factory unterstützt die folgenden Zwischendatentypen: Sie können beim Konfigurieren von Typinformationen in der Konfiguration der [Datasetstruktur](concepts-datasets-linked-services.md#dataset-structure-or-schema) folgende Werte angeben:

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den anderen Artikeln zur Kopieraktivität:

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
