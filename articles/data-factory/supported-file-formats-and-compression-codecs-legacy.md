---
title: In Azure Data Factory unterstützte Dateiformate (Legacy)
description: In diesem Thema werden die Dateiformate und Komprimierungscodecs beschrieben, die von dateibasierten Connectors in Azure Data Factory unterstützt werden.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d95927a9ea7d3084387a9aedb0dcdd86f84b8e7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384825"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Unterstützte Dateiformate und Komprimierungscodecs in Azure Data Factory (Legacy)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Dieser Artikel bezieht sich auf die folgenden Connectors: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>In Data Factory wurde das neue formatbasierte Datasetmodell eingeführt. Weitere Informationen dazu finden Sie im Artikel zum entsprechenden Format: <br>- [Avro-Format](format-avro.md)<br>- [Binärformat](format-binary.md)<br>- [Textformat mit Trennzeichen](format-delimited-text.md)<br>- [JSON-Format](format-json.md)<br>- [ORC-Format](format-orc.md)<br>- [Parquet-Format](format-parquet.md)<br>Die in diesem Artikel erwähnten restlichen Konfigurationen werden nach wie vor aus Gründen der Abwärtskompatibilität unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden. 

## <a name="text-format-legacy"></a><a name="text-format"></a> Textformat (Legacy)

>[!NOTE]
>Informationen zum neuen Modell finden Sie im Artikel zum [Textformat mit Trennzeichen](format-delimited-text.md). Die folgenden Konfigurationen für dateibasierte Datenspeicherdatasets werden aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

Wenn Sie aus einer Textdatei lesen oder in eine Textdatei schreiben möchten, legen Sie die `type`-Eigenschaft im Abschnitt `format` des Datasets auf **TextFormat** fest. Sie können auch die folgenden **optionalen** Eigenschaften im Abschnitt `format` angeben. Informationen zum Konfigurieren finden Sie im Abschnitt [TextFormat-Beispiel](#textformat-example).

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| columnDelimiter |Das Zeichen, das in einer Datei zum Trennen von Spalten verwendet wird. Sie können ein selten vorkommendes nicht druckbares Zeichen verwenden, das ggf. nicht in Ihren Daten vorkommt. Geben Sie beispielsweise „\u0001“ an, das den Anfang der Überschrift (Start of Heading, SOH) bedeutet. |Es ist nur ein Zeichen zulässig. Der **Standardwert** ist das **Komma (,)** . <br/><br/>Wenn Sie ein Unicode-Zeichen verwenden möchten, finden Sie unter [Unicode-Zeichen](https://en.wikipedia.org/wiki/List_of_Unicode_characters) den zugehörigen Code. |Nein |
| rowDelimiter |Das Zeichen, das zum Trennen von Zeilen in einer Datei verwendet wird. |Es ist nur ein Zeichen zulässig. Der **Standardwert** ist einer der folgenden: **[„\r\n“, „\r“, „\n“]** beim Lesen und **„\r\n“** beim Schreiben. |Nein |
| escapeChar |Das Sonderzeichen, mit dem ein Spaltentrennzeichen im Inhalt der Eingabedatei mit Escapezeichen versehen werden kann. <br/><br/>Sie können nicht gleichzeitig „escapeChar“ und „quoteChar“ für eine Tabelle angeben. |Es ist nur ein Zeichen zulässig. Für dieses Feld gibt es keinen Standardwert. <br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text verwenden möchten (Beispiel: "Hello, world"), können Sie „$“ als Escapezeichen definieren und die Zeichenfolge "Hello$, world" in der Quelle verwenden. |Nein |
| quoteChar |Das Zeichen, mit dem ein Zeichenfolgenwert in Anführungszeichen gesetzt wird. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Diese Eigenschaft gilt sowohl für Eingabe- als auch Ausgabedatasets.<br/><br/>Sie können nicht gleichzeitig „escapeChar“ und „quoteChar“ für eine Tabelle angeben. |Es ist nur ein Zeichen zulässig. Für dieses Feld gibt es keinen Standardwert. <br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello, world>) verwenden möchten, können Sie das doppelte gerade Anführungszeichen (") als Escapezeichen definieren und die Zeichenfolge "Hello, world" in der Quelle verwenden. |Nein |
| nullValue |Ein oder mehrere Zeichen, mit denen ein NULL-Wert dargestellt wird. |Ein oder mehrere Zeichen. Die **Standardwerte** lauten **„\N“ und „NULL“** beim Lesen und **„\N“** beim Schreiben. |Nein |
| encodingName |Geben Sie den Codierungsnamen an. |Ein gültiger Codierungsname. Siehe [Encoding.EncodingName-Eigenschaft](/dotnet/api/system.text.encoding). Beispiel: windows-1250 oder shift_jis. Der **Standardwert** lautet **UTF-8**. |Nein |
| firstRowAsHeader |Gibt an, ob die erste Zeile als Kopfzeile betrachtet werden soll. Bei einem Eingabedataset liest Data Factory die erste Zeile als Kopfzeile. Bei einem Ausgabedataset schreibt Data Factory die erste Zeile als Kopfzeile. <br/><br/>Beispielszenarien finden Sie unter [Szenarien für die Verwendung von `firstRowAsHeader` und `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (Standard)</b> |Nein |
| skipLineCount |Gibt an, wie viele **nicht leere** Zeilen beim Lesen von Daten aus Eingabedateien übersprungen werden sollen. Wenn „skipLineCount“ und „firstRowAsHeader“ gleichzeitig angegeben sind, werden die Zeilen zuerst übersprungen, und anschließend werden die Kopfzeileninformationen aus der Eingabedatei gelesen. <br/><br/>Beispielszenarien finden Sie unter [Szenarien für die Verwendung von `firstRowAsHeader` und `skipLineCount`](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Integer |Nein |
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

## <a name="json-format-legacy"></a><a name="json-format"></a> JSON-Format (Legacy)

>[!NOTE]
>Informationen zum neuen Modell finden Sie im Artikel zum [JSON-Format](format-json.md). Die folgenden Konfigurationen für dateibasierte Datenspeicherdatasets werden aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

Informationen zum **unveränderten Importieren/Exportieren einer JSON-Datei in/aus Azure Cosmos DB** finden Sie im Abschnitt „Importieren oder Exportieren von JSON-Dokumenten“ im Artikel [Verschieben von Daten in/aus Azure Cosmos DB](connector-azure-cosmos-db.md).

Wenn Sie JSON-Dateien analysieren oder die Daten im JSON-Format schreiben möchten, legen Sie für die `type`-Eigenschaft im Abschnitt `format` den Wert **JsonFormat** fest. Sie können auch die folgenden **optionalen** Eigenschaften im Abschnitt `format` angeben. Informationen zum Konfigurieren finden Sie im Abschnitt [JsonFormat-Beispiel](#jsonformat-example).

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| filePattern |Geben Sie das Muster der in jeder JSON-Datei gespeicherten Daten an. Zulässige Werte sind **setOfObjects** und **arrayOfObjects**. Der **Standardwert** ist **setOfObjects**. Weitere Informationen zu diesen Mustern finden Sie im Abschnitt [JSON-Dateimuster](#json-file-patterns). |Nein |
| jsonNodeReference | Falls Sie Daten durchlaufen und aus den Objekten in einem Arrayfeld mit demselben Muster extrahieren möchten, legen Sie den JSON-Pfad dieses Arrays fest. Diese Eigenschaft wird nur beim Kopieren von Daten **aus** JSON-Dateien unterstützt. | Nein |
| jsonPathDefinition | Geben Sie den JSON-Pfadausdruck für jede Spaltenzuordnung mit einem benutzerdefinierten Spaltennamen (beginnend mit einem Kleinbuchstaben) an. Diese Eigenschaft wird nur beim Kopieren von Daten **aus** JSON-Dateien unterstützt. Sie können zudem Daten aus dem Objekt oder Array extrahieren. <br/><br/> Bei Feldern unter dem Stammobjekt beginnen Sie mit Stamm „$“. Bei Feldern innerhalb des Arrays, die anhand der `jsonNodeReference`-Eigenschaft ausgewählt werden, beginnen Sie mit dem Arrayelement. Informationen zum Konfigurieren finden Sie im Abschnitt [JsonFormat-Beispiel](#jsonformat-example). | Nein |
| encodingName |Geben Sie den Codierungsnamen an. Die Liste der gültigen Codierungsnamen finden Sie unter: [Encoding.EncodingName](/dotnet/api/system.text.encoding)-Eigenschaft. Beispiel: Windows-1250 oder Shift-JIS. Der **Standardwert** lautet: **UTF-8**. |Nein |
| nestingSeparator |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. Der Standardwert ist „.“ (Punkt). |Nein |

>[!NOTE]
>Für den Fall, dass anwendungsübergreifende Daten in Arrays in mehreren Zeilen vorliegen (Fall 1 -> Beispiel 2 in [JsonFormat-Beispielen](#jsonformat-example)), können Sie nur einzelne Arrays über die Eigenschaft `jsonNodeReference` erweitern.

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

- Abschnitt `structure` definiert die benutzerdefinierten Spaltennamen und den entsprechenden Datentyp beim Konvertieren in tabellarische Daten. Dieser Abschnitt ist **optional**, solange Sie keine Spaltenzuordnung ausführen müssen. Weitere Informationen finden Sie unter [Zuordnen von Spalten im Quelldataset zu Spalten im Zieldataset](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition` gibt den JSON-Pfad für jede Spalte an, die anzeigt, wo die Daten extrahiert werden sollen. Um Daten aus dem Array zu kopieren, können Sie mit `array[x].property` den Wert der angegebenen Eigenschaft aus dem `xth`-Objekt extrahieren oder mit `array[*].property` in einem beliebigen Objekt mit entsprechender Eigenschaft nach dem Wert suchen.

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

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


Das Eingabedataset vom Typ **JsonFormat** ist wie folgt definiert: (Teildefinition ausschließlich mit den relevanten Teilen). Dies gilt insbesondere in folgenden Fällen:

- Abschnitt `structure` definiert die benutzerdefinierten Spaltennamen und den entsprechenden Datentyp beim Konvertieren in tabellarische Daten. Dieser Abschnitt ist **optional**, solange Sie keine Spaltenzuordnung ausführen müssen. Weitere Informationen finden Sie unter [Zuordnen von Spalten im Quelldataset zu Spalten im Zieldataset](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` gibt an, dass die Iteration und Extraktion von Daten aus den Objekten mit dem Muster unter **Array** `orderlines` erfolgt.
- `jsonPathDefinition` gibt den JSON-Pfad für jede Spalte an, die anzeigt, wo die Daten extrahiert werden sollen. In diesem Beispiel unterliegen `ordernumber`, `orderdate` und `city` dem Stammobjekt mit dem JSON-Pfad, der mit `$.` beginnt. `order_pd` und `order_price` beginnen hingegen mit dem Pfad, der sich aus dem Array-Element ohne `$.` ableitet.

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

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a> Parquet-Format (Legacy)

>[!NOTE]
>Informationen zum neuen Modell finden Sie im Artikel zum [Parquet-Format](format-parquet.md). Die folgenden Konfigurationen für dateibasierte Datenspeicherdatasets werden aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

Wenn Sie ORC-Dateien analysieren oder die Daten im Parquet-Format schreiben möchten, legen Sie für die `format` `type`-Eigenschaft **ParquetFormat** fest. Sie müssen im Abschnitt „Format“ innerhalb des Abschnitts „typeProperties“ keine Eigenschaften angeben. Beispiel:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Beachten Sie folgende Punkte:

* Komplexe Datentypen werden nicht unterstützt (MAP, LIST).
* Ein Leerzeichen im Spaltennamen wird nicht unterstützt.
* Für die Parquet-Datei stehen die folgenden mit der Komprimierung zusammenhängenden Optionen zur Verfügung: NONE, SNAPPY, GZIP und LZO. Data Factory unterstützt das Lesen von Daten aus Parquet-Dateien in jedem der oben genannten komprimierten Formate mit Ausnahme von LZO. Zum Lesen der Daten wird der Komprimierungscodec in den Metadaten verwendet. Beim Schreiben in eine Parquet-Datei wählt Data Factory hingegen SNAPPY (Standardeinstellung für das Parquet-Format). Derzeit gibt es keine Option zum Überschreiben dieses Verhaltens.

> [!IMPORTANT]
> Wenn Sie bei Kopiervorgängen mithilfe einer selbstgehosteten Integration Runtime, z.B. zwischen lokalen Datenspeichern und der Cloud, Parquet-Dateien nicht **unverändert** kopieren, müssen Sie die **64-Bit-Version der JRE 8 (Java Runtime Environment) oder OpenJDK** auf Ihrem IR-Computer installieren. Weitere Details finden Sie im folgenden Absatz.

Für Kopiervorgänge in der selbstgehosteten Integration Runtime mit Serialisierung/Deserialisierung von Parquet-Dateien sucht ADF die Java Runtime Environment, indem es zunächst die Registrierung *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* auf JRE überprüft. Wird diese nicht gefunden, wird im nächsten Versuch die Systemvariable *`JAVA_HOME`* auf OpenJDK überprüft.

- **Für JRE:** Die 64-Bit-Integration Runtime erfordert die 64-Bit-JRE. Diese steht [hier](https://go.microsoft.com/fwlink/?LinkId=808605) zur Verfügung.
- **Für OpenJDK:** Die Unterstützung ist seit Version 3.13 der Integration Runtime verfügbar. Packen Sie die Datei „jvm.dll“ zusammen mit allen anderen erforderlichen OpenJDK-Assemblys in einem selbstgehosteten IR-Computer, und legen Sie die Umgebungsvariable JAVA_HOME des Systems entsprechend fest.

>[!TIP]
>Wenn Sie Daten mit der selbstgehosteten Integration Runtime in das/aus dem Parquet-Format kopieren und ein Fehler mit dem Text „Fehler beim Aufrufen von Java, Meldung: **java.lang.OutOfMemoryError:Java-Heapspeicher**“ auftritt, können Sie auf dem Computer, auf dem sich die selbstgehosteten IR befindet, eine Umgebungsvariable `_JAVA_OPTIONS` hinzufügen, um die min./max. Heapgröße für JVM anzupassen, sodass eine solche Kopie möglich ist, und dann die Pipeline erneut ausführen.

![JVM-Heapgröße für selbstgehostete IR festlegen](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Beispiel: Legen Sie für die Variable `_JAVA_OPTIONS` den Wert `-Xms256m -Xmx16g` fest. Das Flag `Xms` gibt den anfänglichen Speicherzuweisungspool für eine Java Virtual Machine (JVM) an, während `Xmx` den maximalen Speicherzuweisungspool angibt. Das bedeutet, dass die JVM mit einer Speichergröße von `Xms` gestartet wird und eine maximale Speichergröße von `Xmx` verwenden kann. Standardmäßig verwendet ADF mindestens 64 MB und maximal 1 G.

### <a name="data-type-mapping-for-parquet-files"></a>Datentypzuordnung für Parquet-Dateien

| Data Factory-Zwischendatentyp | Primitiver Parquet-Typ | Ursprünglicher Parquet-Typ (Deserialisieren) | Ursprünglicher Parquet-Typ (Serialisieren) |
|:--- |:--- |:--- |:--- |
| Boolean | Boolean | – | – |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binär | UInt64 | Decimal |
| Single | Float | – | – |
| Double | Double | – | – |
| Decimal | Binary | Decimal | Decimal |
| String | Binary | Utf8 | Utf8 |
| Datetime | Int96 | – | – |
| TimeSpan | Int96 | – | – |
| DateTimeOffset | Int96 | – | – |
| ByteArray | Binary | – | – |
| Guid | Binary | Utf8 | Utf8 |
| Char | Binary | Utf8 | Utf8 |
| CharArray | Nicht unterstützt | – | – |

## <a name="orc-format-legacy"></a><a name="orc-format"></a> ORC-Format (Legacy)

>[!NOTE]
>Informationen zum neuen Modell finden Sie im Artikel zum [ORC-Format](format-orc.md). Die folgenden Konfigurationen für dateibasierte Datenspeicherdatasets werden aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

Wenn Sie ORC-Dateien analysieren oder die Daten im ORC-Format schreiben möchten, legen Sie für die `format` `type`-Eigenschaft **OrcFormat** fest. Sie müssen im Abschnitt „Format“ innerhalb des Abschnitts „typeProperties“ keine Eigenschaften angeben. Beispiel:

```json
"format":
{
    "type": "OrcFormat"
}
```

Beachten Sie folgende Punkte:

* Komplexe Datentypen werden nicht unterstützt (STRUCT, MAP, LIST, UNION).
* Ein Leerzeichen im Spaltennamen wird nicht unterstützt.
* Für die ORC-Datei stehen drei [mit der Komprimierung zusammenhängende Optionen](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) zur Verfügung: NONE, ZLIB, SNAPPY. Data Factory unterstützt das Lesen von Daten aus ORC-Dateien in jedem der oben genannten komprimierten Formate. Zum Lesen der Daten wird der Komprimierungscodec in den Metadaten verwendet. Beim Schreiben in eine ORC-Datei wählt Data Factory hingegen ZLIB (Standardeinstellung für ORC). Derzeit gibt es keine Option zum Überschreiben dieses Verhaltens.

> [!IMPORTANT]
> Wenn Sie bei Kopiervorgängen mithilfe einer selbstgehosteten Integration Runtime, z.B. zwischen lokalen Datenspeichern und der Cloud, ORC-Dateien nicht **unverändert** kopieren, müssen Sie die **64-Bit-Version der JRE 8 (Java Runtime Environment) oder OpenJDK** auf Ihrem IR-Computer installieren. Weitere Details finden Sie im folgenden Absatz.

Für Kopiervorgänge in der selbstgehosteten Integration Runtime mit Serialisierung/Deserialisierung von ORC-Dateien sucht ADF die Java Runtime Environment, indem es zunächst die Registrierung *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* auf JRE überprüft. Wird diese nicht gefunden, wird im nächsten Versuch die Systemvariable *`JAVA_HOME`* auf OpenJDK überprüft.

- **Für JRE:** Die 64-Bit-Integration Runtime erfordert die 64-Bit-JRE. Diese steht [hier](https://go.microsoft.com/fwlink/?LinkId=808605) zur Verfügung.
- **Für OpenJDK:** Die Unterstützung ist seit Version 3.13 der Integration Runtime verfügbar. Packen Sie die Datei „jvm.dll“ zusammen mit allen anderen erforderlichen OpenJDK-Assemblys in einem selbstgehosteten IR-Computer, und legen Sie die Umgebungsvariable JAVA_HOME des Systems entsprechend fest.

### <a name="data-type-mapping-for-orc-files"></a>Datentypzuordnung für ORC-Dateien

| Data Factory-Zwischendatentyp | ORC-Typen |
|:--- |:--- |
| Boolean | Boolean |
| SByte | Byte |
| Byte | Short |
| Int16 | Short |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Long |
| Int64 | Long |
| UInt64 | String |
| Single | Float |
| Double | Double |
| Decimal | Decimal |
| String | String |
| Datetime | Timestamp |
| DateTimeOffset | Timestamp |
| TimeSpan | Timestamp |
| ByteArray | Binary |
| Guid | String |
| Char | Char(1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a> Avro-Format (Legacy)

>[!NOTE]
>Informationen zum neuen Modell finden Sie im Artikel zum [AVRO-Format](format-avro.md). Die folgenden Konfigurationen für dateibasierte Datenspeicherdatasets werden aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

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

## <a name="compression-support-legacy"></a><a name="compression-support"></a> Unterstützung der Komprimierung (Legacy)

Azure Data Factory unterstützt das Komprimieren und Dekomprimieren während des Kopiervorgangs. Wenn Sie die `compression`-Eigenschaft in einem Eingabedataset angeben, kann die Kopieraktivität die komprimierten Daten aus der Quelle lesen und dekomprimieren. Bei Angabe der Eigenschaft in einem Ausgabedataset kann die Kopieraktivität Daten erst komprimieren und dann in die Senke schreiben. Es folgen einige Beispielszenarios:

* Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, und schreiben Sie die resultierenden Daten in Azure SQL-Datenbank. Sie definieren das Azure-Blob-Eingabedataset mit der `compression` `type`-Eigenschaft als GZIP.
* Lesen Sie Daten aus einer Nur-Text-Datei aus einem lokalen Dateisystem, komprimieren Sie sie mithilfe des GZip-Formats, und schreiben Sie die komprimierten Daten in einen Azure-Blob. Sie definieren ein Azure-Blob-Ausgabedataset mit der `compression` `type`-Eigenschaft als GZip.
* Lesen Sie die ZIP-Datei vom FTP-Server, dekomprimieren Sie sie, um die Dateien zu extrahieren, und stellen Sie die Dateien in Azure Data Lake Store bereit. Sie definieren ein FTP-Eingabedataset mit der `compression` `type`-Eigenschaft als ZipDeflate.
* Lesen Sie GZIP-komprimierte Daten aus einem Azure-Blob, dekomprimieren Sie sie, komprimieren Sie sie mit BZIP2, und schreiben Sie die resultierenden Daten in einen Azure-Blob. Sie definieren das Azure-Blob-Eingabedataset mit Einstellung von `compression` `type` auf GZIP und das Ausgabedataset mit Einstellung von `compression` `type` auf BZIP2.

Um die Komprimierung für ein Dataset anzugeben, verwenden Sie im JSON-Dataset die Eigenschaft für die **Komprimierung** wie im folgenden Beispiel:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

Der Abschnitt für die **Komprimierung** enthält zwei Eigenschaften:

* **Typ:** Der Komprimierungscodec, z.B. **GZIP**, **Deflate**, **BZIP2** oder **ZipDeflate**. Hinweis: Bei der Verwendung der Kopieraktivität zum Dekomprimieren einer oder mehrerer ZipDeflate-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher werden Dateien in den folgenden Ordner extrahiert: `<path specified in dataset>/<folder named as source zip file>/`.
* **Ebene:** Das Komprimierungsverhältnis, z.B. **Optimal** oder **Schnellstes**.

  * **Schnellstes:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.
  * **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt.

    Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) .

> [!NOTE]
> Für Daten im **AvroFormat**, **OrcFormat** oder **ParquetFormat** werden keine Komprimierungseinstellungen unterstützt. Beim Lesen der Daten in diesen Formaten erkennt und verwendet Data Factory den Komprimierungscodec in den Metadaten. Für das Schreiben in eine Datei in diesen Formaten wählt Data Factory den Standardkomprimierungscodec für das jeweilige Format. Beispiel: ZLIB für OrcFormat und SNAPPY für ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>Nicht unterstützte Dateitypen und Komprimierungsformate

Sie können mit den Erweiterungsfeatures von Azure Data Factory Dateien transformieren, die nicht unterstützt werden.
Zwei Optionen sind Azure Functions und benutzerdefinierte Aufgaben mithilfe von Azure Batch.

Sehen Sie sich ein Beispiel an, in dem mithilfe einer Azure-Funktion [der Inhalt einer tar-Datei extrahiert wird](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). Weitere Informationen finden Sie unter [Aktivität „Azure Function“ in Azure Data Factory](./control-flow-azure-function-activity.md).

Sie können diese Funktionalität auch mit einer benutzerdefinierten Dotnet-Aktivität erstellen. Weitere Informationen sind [hier](./transform-data-using-dotnet-custom-activity.md) verfügbar.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den aktuell unterstützten Dateiformaten und Komprimierungen finden Sie unter [Unterstützte Dateiformate und Komprimierungen](supported-file-formats-and-compression-codecs.md).