---
title: Datei- und Komprimierungsformate in Azure Data Factory
description: Informationen zu den von Azure Data Factory unterstützten Dateiformaten.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7b53d9cd64c50d8305714878324dd355eb6d1840
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368726"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Von Azure Data Factory unterstützte Datei- und Komprimierungsformate
*Dieses Thema bezieht sich auf die folgenden Connectors: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure-Blob](data-factory-azure-blob-connector.md), [Azure Data Lake Store](data-factory-azure-datalake-connector.md), [Dateisystem](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md), [HDFS](data-factory-hdfs-connector.md), [HTTP](data-factory-http-connector.md) und [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [unterstützten Dateiformaten und Komprimierungscodes in Data Factory](../supported-file-formats-and-compression-codecs.md).

Azure Data Factory unterstützt die folgenden Dateiformattypen:

* [Textformat](#text-format)
* [JSON-Format](#json-format)
* [Avro-Format](#avro-format)
* [ORC-Format](#orc-format)
* [Parquet-Format](#parquet-format)

## <a name="text-format"></a>Textformat
Wenn Sie aus einer Textdatei lesen oder in eine Textdatei schreiben möchten, legen Sie die `type`-Eigenschaft im Abschnitt `format` des Datasets auf **TextFormat** fest. Sie können auch die folgenden **optionalen** Eigenschaften im Abschnitt `format` angeben. Informationen zum Konfigurieren finden Sie im Abschnitt [TextFormat-Beispiel](#textformat-example).

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| columnDelimiter |Das Zeichen, das in einer Datei zum Trennen von Spalten verwendet wird. Sie können ein selten vorkommendes nicht druckbares Zeichen verwenden, das nur mit sehr geringer Wahrscheinlichkeit in Ihren Daten vorkommt. Geben Sie beispielsweise „\u0001“ an, das den Anfang der Überschrift (Start of Heading, SOH) bedeutet. |Es ist nur ein Zeichen zulässig. Der **Standardwert** ist das **Komma (,)** . <br/><br/>Wenn Sie ein Unicode-Zeichen verwenden möchten, finden Sie unter [Unicode-Zeichen](https://en.wikipedia.org/wiki/List_of_Unicode_characters) den zugehörigen Code. |Nein |
| rowDelimiter |Das Zeichen, das zum Trennen von Zeilen in einer Datei verwendet wird. |Es ist nur ein Zeichen zulässig. Der **Standardwert** ist einer der folgenden: **[„\r\n“, „\r“, „\n“]** beim Lesen und **„\r\n“** beim Schreiben. |Nein |
| escapeChar |Das Sonderzeichen, mit dem ein Spaltentrennzeichen im Inhalt der Eingabedatei mit Escapezeichen versehen werden kann. <br/><br/>Sie können nicht gleichzeitig „escapeChar“ und „quoteChar“ für eine Tabelle angeben. |Es ist nur ein Zeichen zulässig. Für dieses Feld gibt es keinen Standardwert. <br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text verwenden möchten (Beispiel: "Hello, world"), können Sie „$“ als Escapezeichen definieren und die Zeichenfolge "Hello$, world" in der Quelle verwenden. |Nein |
| quoteChar |Das Zeichen, mit dem ein Zeichenfolgenwert in Anführungszeichen gesetzt wird. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Diese Eigenschaft gilt sowohl für Eingabe- als auch Ausgabedatasets.<br/><br/>Sie können nicht gleichzeitig „escapeChar“ und „quoteChar“ für eine Tabelle angeben. |Es ist nur ein Zeichen zulässig. Für dieses Feld gibt es keinen Standardwert. <br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello, world>) verwenden möchten, können Sie das doppelte gerade Anführungszeichen (") als Escapezeichen definieren und die Zeichenfolge "Hello, world" in der Quelle verwenden. |Nein |
| nullValue |Ein oder mehrere Zeichen, mit denen ein NULL-Wert dargestellt wird. |Ein oder mehrere Zeichen. Die **Standardwerte** lauten **„\N“ und „NULL“** beim Lesen und **„\N“** beim Schreiben. |Nein |
| encodingName |Geben Sie den Codierungsnamen an. |Ein gültiger Codierungsname. Siehe [Encoding.EncodingName-Eigenschaft](/dotnet/api/system.text.encoding). Beispiel: windows-1250 oder shift_jis. Der **Standardwert** lautet **UTF-8**. |Nein |
| firstRowAsHeader |Gibt an, ob die erste Zeile als Kopfzeile betrachtet werden soll. Bei einem Eingabedataset liest Data Factory die erste Zeile als Kopfzeile. Bei einem Ausgabedataset schreibt Data Factory die erste Zeile als Kopfzeile. <br/><br/>Beispielszenarien finden Sie unter [Szenarien für die Verwendung von `firstRowAsHeader` und `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (Standard)</b> |Nein |
| skipLineCount |Gibt an, wie viele Zeilen beim Lesen von Daten aus Eingabedateien übersprungen werden sollen. Wenn „skipLineCount“ und „firstRowAsHeader“ gleichzeitig angegeben sind, werden die Zeilen zuerst übersprungen, und anschließend werden die Kopfzeileninformationen aus der Eingabedatei gelesen. <br/><br/>Beispielszenarien finden Sie unter [Szenarien für die Verwendung von `firstRowAsHeader` und `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Nein |
| treatEmptyAsNull |Gibt an, ob Null- oder leere Zeichenfolgen beim Lesen von Daten aus einer Eingabedatei als NULL-Werte behandelt werden sollen. |**True (Standard)**<br/>False |Nein |

### <a name="textformat-example"></a>TextFormat-Beispiel
In der folgenden JSON-Definition für ein Dataset sind einige der optionalen Eigenschaften angegeben.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

Zur Verwendung von `escapeChar` anstelle von `quoteChar` ersetzen Sie die Zeile mit `quoteChar` durch das folgende escapeChar-Element:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>Szenarien für die Verwendung von firstRowAsHeader und skipLineCount
* Sie kopieren Daten aus einer Quelle, bei der es sich nicht um eine Datei handelt, in eine Textdatei und möchten eine Kopfzeile mit den Schemametadaten hinzufügen (Beispiel: SQL-Schema). Legen Sie für dieses Szenario `firstRowAsHeader` im Ausgabedataset als „true“ fest.
* Sie kopieren aus einer Textdatei mit einer Kopfzeile in eine Senke, bei der es sich nicht um eine Datei handelt, und möchten diese Zeile löschen. Legen Sie `firstRowAsHeader` im Eingabedataset als „true“ fest.
* Sie kopieren aus einer Textdatei und möchten einige Zeilen am Anfang überspringen, die keine Daten oder Kopfzeileninformationen enthalten. Geben Sie mit `skipLineCount` die Anzahl von Zeilen an, die übersprungen werden sollen. Wenn der Rest der Datei eine Kopfzeile enthält, können Sie auch `firstRowAsHeader` angeben. Wenn sowohl `skipLineCount` als auch `firstRowAsHeader` angegeben sind, werden erst die Zeilen übersprungen und dann die Kopfzeileninformationen aus der Eingabedatei gelesen.

## <a name="json-format"></a>JSON-Format
Informationen zum **unveränderten Importieren/Exportieren einer JSON-Datei in/aus Azure Cosmos DB** finden Sie im Abschnitt [Importieren oder Exportieren von JSON-Dokumenten](data-factory-azure-documentdb-connector.md#importexport-json-documents) im Artikel [Verschieben von Daten in/aus Azure Cosmos DB](data-factory-azure-documentdb-connector.md).

Wenn Sie JSON-Dateien analysieren oder die Daten im JSON-Format schreiben möchten, legen Sie für die `type`-Eigenschaft im Abschnitt `format` den Wert **JsonFormat** fest. Sie können auch die folgenden **optionalen** Eigenschaften im Abschnitt `format` angeben. Informationen zum Konfigurieren finden Sie im Abschnitt [JsonFormat-Beispiel](#jsonformat-example).

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| filePattern |Geben Sie das Muster der in jeder JSON-Datei gespeicherten Daten an. Zulässige Werte sind **setOfObjects** und **arrayOfObjects**. Der **Standardwert** ist **setOfObjects**. Weitere Informationen zu diesen Mustern finden Sie im Abschnitt [JSON-Dateimuster](#json-file-patterns). |Nein |
| jsonNodeReference | Falls Sie Daten durchlaufen und aus den Objekten in einem Arrayfeld mit demselben Muster extrahieren möchten, legen Sie den JSON-Pfad dieses Arrays fest. Diese Eigenschaft wird nur beim Kopieren von Daten aus JSON-Dateien unterstützt. | Nein |
| jsonPathDefinition | Geben Sie den JSON-Pfadausdruck für jede Spaltenzuordnung mit einem benutzerdefinierten Spaltennamen (beginnend mit einem Kleinbuchstaben) an. Diese Eigenschaft wird nur beim Kopieren von Daten aus JSON-Dateien unterstützt. Sie können zudem Daten aus dem Objekt oder Array extrahieren. <br/><br/> Bei Feldern unter dem Stammobjekt beginnen Sie mit Stamm „$“. Bei Feldern innerhalb des Arrays, die anhand der `jsonNodeReference`-Eigenschaft ausgewählt werden, beginnen Sie mit dem Arrayelement. Informationen zum Konfigurieren finden Sie im Abschnitt [JsonFormat-Beispiel](#jsonformat-example). | Nein |
| encodingName |Geben Sie den Codierungsnamen an. Die Liste der gültigen Codierungsnamen finden Sie unter: [Encoding.EncodingName](/dotnet/api/system.text.encoding)-Eigenschaft. Beispiel: Windows-1250 oder Shift-JIS. Der **Standardwert** lautet: **UTF-8**. |Nein |
| nestingSeparator |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. Der Standardwert ist „.“ (Punkt). |Nein |

### <a name="json-file-patterns"></a>JSON-Dateimuster

Die Kopieraktivität kann die Muster der folgenden JSON-Dateien analysieren:

- **Typ I: setOfObjects**

    Jede Datei enthält ein einzelnes Objekt oder mehrere durch Zeilen getrennte/verkettete Objekte. Wenn diese Option in einem Ausgabedataset ausgewählt wird, erzeugt die Kopieraktivität eine einzelne JSON-Datei mit jedem Objekt pro Zeile (durch Zeilen getrennt).

    * **JSON-Beispiel mit einzelnem Objekt**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **JSON-Beispiel mit getrennten Zeilen**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **JSON-Beispiel mit Verkettung**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Typ II: arrayOfObjects**

    Jede Datei enthält ein Array von Objekten.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat-Beispiel

**Fall 1: Kopieren von Daten aus JSON-Dateien**

Betrachten Sie dazu die zwei folgenden Beispiele beim Kopieren von Daten aus JSON-Dateien. Die zu beachtenden allgemeinen Punkte:

**Beispiel 1: Extrahieren von Daten aus Objekt und Array**

In diesem Beispiel wird ein einzelnes JSON-Stammobjekt einem einzelnen Datensatz in einem tabellarischen Ergebnis zugeordnet. Wenn Sie eine JSON-Datei mit dem folgenden Inhalt haben:  

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```
und ihn im folgenden Format in eine Azure SQL-Tabelle kopieren möchten, indem Sie Daten aus Objekten und dem Array extrahieren:

| id | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

Das Eingabedataset vom Typ **JsonFormat** ist wie folgt definiert: (Teildefinition ausschließlich mit den relevanten Teilen). Dies gilt insbesondere in folgenden Fällen:

- Abschnitt `structure` definiert die benutzerdefinierten Spaltennamen und den entsprechenden Datentyp beim Konvertieren in tabellarische Daten. Dieser Abschnitt ist **optional**, solange Sie keine Spaltenzuordnung ausführen müssen. Weitere Informationen finden Sie unter [Zuordnen von Spalten im Quelldataset zu Spalten im Zieldataset](data-factory-map-columns.md).
- `jsonPathDefinition` gibt den JSON-Pfad für jede Spalte an, die anzeigt, wo die Daten extrahiert werden sollen. Um Daten aus dem Array zu kopieren, können Sie mit **array[x].property** den Wert der angegebenen Eigenschaft aus dem xth-Objekt extrahieren oder mit **array[*].property** in einem beliebigen Objekt mit entsprechender Eigenschaft nach dem Wert suchen.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}      
        }
    }
}
```

**Beispiel 2: Übergreifendes Anwenden mehrerer Objekte mit dem gleichen Muster über das Array**

In diesem Beispiel wird ein einzelnes JSON-Stammobjekt im tabellarischen Ergebnis in mehrere Datensätze transformiert. Wenn Sie eine JSON-Datei mit dem folgenden Inhalt haben:  

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
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
    "city": [ { "sanmateo": "No 1" } ]
}
```
und Sie ihn in eine Azure SQL-Tabelle im folgenden Format kopieren möchten, indem Sie die Daten im Array vereinfachen und mit den allgemeinen Stamminformationen verknüpfen möchten:

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

Das Eingabedataset vom Typ **JsonFormat** ist wie folgt definiert: (Teildefinition ausschließlich mit den relevanten Teilen). Dies gilt insbesondere in folgenden Fällen:

- Abschnitt `structure` definiert die benutzerdefinierten Spaltennamen und den entsprechenden Datentyp beim Konvertieren in tabellarische Daten. Dieser Abschnitt ist **optional**, solange Sie keine Spaltenzuordnung ausführen müssen. Weitere Informationen finden Sie unter [Zuordnen von Spalten im Quelldataset zu Spalten im Zieldataset](data-factory-map-columns.md).
- `jsonNodeReference` gibt an, dass die Iteration und Extraktion von Daten aus den Objekten mit dem Muster unter **Array** Orderlines erfolgt.
- `jsonPathDefinition` gibt den JSON-Pfad für jede Spalte an, die anzeigt, wo die Daten extrahiert werden sollen. In diesem Beispiel unterliegen „ordernumber“, „orderdate“ und „city“ dem Stammobjekt mit dem JSON-Pfad, der mit „$.“ beginnt. „order_pd“ und „order_price“ beginnen hingegen mit dem Pfad, der sich aus dem Array-Element ohne „$.“ ableitet.

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}         
        }
    }
}
```

**Beachten Sie folgende Punkte:**

* Wenn `structure` und `jsonPathDefinition` im Data Factory-Dataset nicht definiert sind, erkennt die Kopieraktivität das Schema des ersten Objekts und vereinfacht das gesamte Objekt.
* Wenn die JSON-Eingabe ein Array aufweist, konvertiert die Kopieraktivität standardmäßig den gesamten Array-Wert in eine Zeichenfolge. Sie können Daten mit `jsonNodeReference` und/oder `jsonPathDefinition` daraus extrahieren oder diesen Schritt überspringen, indem Sie ihn in `jsonPathDefinition` nicht angeben.
* Wenn auf derselben Ebene doppelte Namen vorkommen, wählt die Kopieraktivität den letzten aus.
* Bei Eigenschaftennamen wird zwischen Groß- und Kleinschreibung unterschieden. Zwei Eigenschaften mit demselben Namen und unterschiedlicher Groß- und Kleinschreibung werden als zwei getrennte Eigenschaften behandelt.

**Fall 2: Schreiben von Daten in eine JSON-Datei**

Wenn Sie in der SQL-Datenbank über die folgende Tabelle verfügen:

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

Und für jeden Datensatz erwarten Sie, ein JSON-Objekt im folgenden Format zu schreiben:
```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

Das Ausgabedataset vom Typ **JsonFormat** ist wie folgt definiert: (Teildefinition ausschließlich mit den relevanten Teilen). Genauer gesagt definiert der Abschnitt `structure` die angepassten Eigenschaftennamen in der Zieldatei, und `nestingSeparator` (der Standardwert ist „.“) wird verwendet, um die nächste Ebene ab dem Namen zu kennzeichnen. Dieser Abschnitt ist **optional**, solange Sie den Namen der Eigenschaft im Vergleich zum Quellspaltennamen nicht ändern oder einige der Eigenschaften verschachteln möchten.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="avro-format"></a>AVRO-Format
Wenn Sie Avro-Dateien analysieren oder die Daten im Avro-Format schreiben möchten, legen Sie für die `format` `type`-Eigenschaft **AvroFormat** fest. Sie müssen im Abschnitt „Format“ innerhalb des Abschnitts „typeProperties“ keine Eigenschaften angeben. Beispiel:

```json
"format":
{
    "type": "AvroFormat",
}
```

Um das Avro-Format in einer Hive-Tabelle zu verwenden, sehen Sie sich zuvor das [Apache Hive-Tutorial](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) an.

Beachten Sie folgende Punkte:  

* [Komplexe Datentypen](https://avro.apache.org/docs/current/spec.html#schema_complex) werden nicht unterstützt (Datensätze, Enumerationen, Arrays, Zuordnungen, Unions und Konstanten).

## <a name="orc-format"></a>ORC-Format
Wenn Sie ORC-Dateien analysieren oder die Daten im ORC-Format schreiben möchten, legen Sie für die `format` `type`-Eigenschaft **OrcFormat** fest. Sie müssen im Abschnitt „Format“ innerhalb des Abschnitts „typeProperties“ keine Eigenschaften angeben. Beispiel:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Wenn Sie ORC-Dateien nicht **unverändert** zwischen lokalen und Clouddatenspeichern kopieren, müssen Sie die JRE 8 (Java Runtime Environment) auf Ihrem Gatewaycomputer installieren. Für ein 64-Bit-Gateway ist die 64-Bit-JRE erforderlich, für ein 32-Bit-Gateway die 32-Bit-JRE. Beide Versionen finden Sie [hier](https://go.microsoft.com/fwlink/?LinkId=808605). Wählen Sie die geeignete Version aus.
>
>

Beachten Sie folgende Punkte:

* Komplexe Datentypen werden nicht unterstützt (STRUCT, MAP, LIST, UNION).
* Für die ORC-Datei stehen drei [mit der Komprimierung zusammenhängende Optionen](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) zur Verfügung: NONE, ZLIB, SNAPPY. Data Factory unterstützt das Lesen von Daten aus ORC-Dateien in jedem der oben genannten komprimierten Formate. Zum Lesen der Daten wird der Komprimierungscodec in den Metadaten verwendet. Beim Schreiben in eine ORC-Datei wählt Data Factory hingegen ZLIB (Standardeinstellung für ORC). Derzeit gibt es keine Option zum Überschreiben dieses Verhaltens.

## <a name="parquet-format"></a>Parquet-Format
Wenn Sie ORC-Dateien analysieren oder die Daten im Parquet-Format schreiben möchten, legen Sie für die `format` `type`-Eigenschaft **ParquetFormat** fest. Sie müssen im Abschnitt „Format“ innerhalb des Abschnitts „typeProperties“ keine Eigenschaften angeben. Beispiel:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Wenn Sie Parquet-Dateien nicht **unverändert** zwischen lokalen und Clouddatenspeichern kopieren, müssen Sie die JRE 8 (Java Runtime Environment) auf Ihrem Gatewaycomputer installieren. Für ein 64-Bit-Gateway ist die 64-Bit-JRE erforderlich, für ein 32-Bit-Gateway die 32-Bit-JRE. Beide Versionen finden Sie [hier](https://go.microsoft.com/fwlink/?LinkId=808605). Wählen Sie die geeignete Version aus.
>
>

Beachten Sie folgende Punkte:

* Komplexe Datentypen werden nicht unterstützt (MAP, LIST).
* Für die Parquet-Datei stehen die folgenden mit der Komprimierung zusammenhängenden Optionen zur Verfügung: NONE, SNAPPY, GZIP und LZO. Data Factory unterstützt das Lesen von Daten aus ORC-Dateien in jedem der oben genannten komprimierten Formate. Zum Lesen der Daten wird der Komprimierungscodec in den Metadaten verwendet. Beim Schreiben in eine Parquet-Datei wählt Data Factory hingegen SNAPPY (Standardeinstellung für das Parquet-Format). Derzeit gibt es keine Option zum Überschreiben dieses Verhaltens.

## <a name="compression-support"></a>Unterstützung für die Komprimierung
Die Verarbeitung großer Datenmengen kann zu E/A- und Netzwerkengpässen führen. Aus diesem Grund kann die Komprimierung von Daten in Speichern nicht nur die Datenübertragung im Netzwerk beschleunigen und Speicherplatz sparen, sondern auch erhebliche Leistungssteigerungen bei der Verarbeitung von Big Data bewirken. Zurzeit wird die Komprimierung für dateibasierte Datenspeicher wie etwa Azure-Blobs oder das lokale Dateisystem unterstützt.  

Um die Komprimierung für ein Dataset anzugeben, verwenden Sie im JSON-Dataset die Eigenschaft für die **Komprimierung** wie im folgenden Beispiel:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Angenommen, das Beispieldataset wird als Ausgabe eines Kopiervorgangs verwendet. Der Kopiervorgang komprimiert die Ausgabedaten mit dem GZIP-Codec bei optimalem Verhältnis und schreibt die komprimierten Daten anschließend in die Datei „pagecounts.csv.gz“ in Azure Blob Storage.

> [!NOTE]
> Für Daten im **AvroFormat**, **OrcFormat** oder **ParquetFormat** werden keine Komprimierungseinstellungen unterstützt. Beim Lesen der Daten in diesen Formaten erkennt und verwendet Data Factory den Komprimierungscodec in den Metadaten. Für das Schreiben in eine Datei in diesen Formaten wählt Data Factory den Standardkomprimierungscodec für das jeweilige Format. Beispiel: ZLIB für OrcFormat und SNAPPY für ParquetFormat.   

Der Abschnitt für die **Komprimierung** enthält zwei Eigenschaften:  

* **Typ:** Der Komprimierungscodec, z.B. **GZIP**, **Deflate**, **BZIP2** oder **ZipDeflate**.  
* **Ebene:** Das Komprimierungsverhältnis, z.B. **Optimal** oder **Schnellstes**.

  * **Schnellstes:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.
  * **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt.

    Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) .

Wenn Sie die `compression`-Eigenschaft in einem JSON-Eingabedataset angeben, kann die Pipeline komprimierte Daten aus der Quelle lesen. Bei Angabe der Eigenschaft in einem JSON-Ausgabedataset kann der Kopiervorgang komprimierte Daten am Ziel schreiben. Es folgen einige Beispielszenarios:

* Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, und schreiben Sie die resultierenden Daten in Azure SQL-Datenbank. Sie definieren das Azure-Blobeingabedataset mit der JSON-Eigenschaft `compression` `type` als GZIP.
* Lesen Sie Daten aus einer Nur-Text-Datei aus einem lokalen Dateisystem, komprimieren Sie sie mithilfe des GZip-Formats, und schreiben Sie die komprimierten Daten in einen Azure-Blob. Sie definieren ein Azure-Blobausgabedataset mit der JSON-Eigenschaft `compression` `type` als GZip.
* Lesen Sie die ZIP-Datei vom FTP-Server, dekomprimieren Sie sie, um die Dateien zu extrahieren, und stellen Sie die Dateien im Azure Data Lake Store bereit. Sie definieren ein FTP-Eingabedataset mit der JSON-Eigenschaft `compression` `type` als ZipDeflate.
* Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, komprimieren Sie sie mit BZIP2, und schreiben Sie die resultierenden Daten in einen Azure-Blob. In diesem Fall definieren Sie für das Azure-Blobeingabedataset die Einstellung GZIP als `compression` `type` und für das Ausgabedataset BZIP2 als `compression` `type`.   


## <a name="next-steps"></a>Nächste Schritte
Informationen zu den von Azure Data Factory unterstützten dateibasierten Datenspeichern finden Sie in den folgenden Artikeln:

- [Azure Blob Storage](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Dateisystem](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)