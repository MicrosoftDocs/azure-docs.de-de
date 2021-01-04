---
title: JSON-Format in Azure Data Factory
description: In diesem Thema wird der Umgang mit dem JSON-Format in Azure Data Factory beschrieben.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: d582278debef67e3a6cf9c9e13ffd45e4f320778
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349400"
---
# <a name="json-format-in-azure-data-factory"></a>JSON-Format in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nutzen Sie diesen Artikel, wenn Sie die **JSON-Dateien analysieren oder Daten im JSON-Format schreiben** möchten. 

Das JSON-Format wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom JSON-Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Die type-Eigenschaft des Datasets muss auf **Json** festgelegt werden. | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften. **Informationen hierzu finden Sie im Abschnitt „Dataset-Eigenschaften“ des Artikels über Connectors**. | Ja      |
| encodingName     | Der zu Lesen/Schreiben von Testdateien verwendete Codierungstyp. <br>Es sind die folgenden Werte zulässig: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nein       |
| compression | Gruppe von Eigenschaften zum Konfigurieren der Dateikomprimierung. Konfigurieren Sie diesen Abschnitt, wenn Sie während der Aktivitätsausführung eine Komprimierung/Dekomprimierung durchführen möchten. | Nein |
| type<br/>(*unter `compression`* ) | Der zum Lesen und Schreiben von JSON-Dateien verwendete Komprimierungscodec. <br>Zulässige Werte sind **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** und **lz4**. Standardmäßig erfolgt keine Komprimierung.<br>**Beachten Sie**, dass die Kopieraktivität zurzeit nicht „snappy“ und „lz4“ und der Zuordnungsdatenfluss nicht „ZipDeflate“, „TarGzip“ und „Tar“ unterstützt.<br>**Beachten Sie**, dass bei Verwendung der Kopieraktivität zum Dekomprimieren von **ZipDeflate**-/**TarGzip**-/**Tar**-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher diese Dateien standardmäßig in den Ordner `<path specified in dataset>/<folder named as source compressed file>/` extrahiert werden. Verwenden Sie in diesem Fall `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` als [Quelle der Kopieraktivität](#json-as-source), um zu steuern, ob der Name der komprimierten Dateien als Ordnerstruktur beibehalten werden soll.| Nein.  |
| level<br/>(*unter `compression`* ) | Das Komprimierungsverhältnis. <br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) . | Nein       |

Nachfolgend sehen Sie ein Beispiel für ein JSON-Dataset in Azure Blob Storage:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der JSON-Quelle und -Senke unterstützt werden.

Unter [Schema- und Datentypzuordnung in Kopieraktivität](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Daten aus JSON-Dateien extrahieren und dem Senkendatenspeicher/-format zuordnen (oder umgekehrt).

### <a name="json-as-source"></a>JSON als Quelle

Die folgenden Eigenschaften werden im Abschnitt **\_source\*** der Kopieraktivität unterstützt.

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **Source** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **JSON-Leseeinstellungen** finden Sie in der Tabelle unten. | Nein       |
| storeSettings | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **JSON-Leseeinstellungen** unter `formatSettings`:

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Der Typ von „formatSettings“ muss auf **JsonReadSettings** festgelegt werden. | Ja      |
| compressionProperties | Eine Gruppe von Eigenschaften zur Festlegung, wie Daten bei einem bestimmten Komprimierungscodec dekomprimiert werden können. | Nein       |
| preserveZipFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `ZipDeflateReadSettings`* )  | Diese Eigenschaft gilt, wenn das Eingabedataset mit der **ZipDeflate**-Komprimierung konfiguriert wurde. Sie gibt an, ob der Name der ZIP-Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standard), schreibt Data Factory entzippte Dateien in `<path specified in dataset>/<folder named as source zip file>/`.<br>– Lautet der Wert **false**, schreibt Data Factory entzippte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen ZIP-Quelldateien vorliegen, um „Racing“ oder ein unerwartetes Verhalten zu vermeiden.  | Nein |
| preserveCompressionFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `TarGZipReadSettings` oder `TarReadSettings`* ) | Gilt, wenn das Eingabedataset mit der Komprimierung **TarGzip**/**Tar** konfiguriert wurde. Gibt an, ob der Name der komprimierten Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standardwert), schreibt Data Factory dekomprimierte Dateien in `<path specified in dataset>/<folder named as source compressed file>/`. <br>– Lautet der Wert **false**, schreibt Data Factory dekomprimierte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen Quelldateien vorliegen, um Racebedingungen oder ein unerwartetes Verhalten zu vermeiden. | Nein |

### <a name="json-as-sink"></a>JSON als Senke

Die folgenden Eigenschaften werden im Abschnitt **\_sink\*** der Kopieraktivität unterstützt.

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **JSONSink** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **JSON-Schreibeinstellungen** finden Sie in der Tabelle unten. | Nein       |
| storeSettings | Eine Gruppe von Eigenschaften für das Schreiben von Daten in einen Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Schreibeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **JSON-Schreibeinstellungen** unter `formatSettings`:

| Eigenschaft      | Beschreibung                                                  | Erforderlich                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Der Typ von „formatSettings“ muss auf **JsonWriteSettings** festgelegt werden. | Ja                                                   |
| filePattern |Geben Sie das Muster der in jeder JSON-Datei gespeicherten Daten an. Zulässige Werte sind **setOfObjects** (JSON-Zeilen) und **arrayOfObjects**. Der **Standardwert** ist **setOfObjects**. Weitere Informationen zu diesen Mustern finden Sie im Abschnitt [JSON-Dateimuster](#json-file-patterns). |Nein |

### <a name="json-file-patterns"></a>JSON-Dateimuster

Beim Kopieren von Daten aus JSON-Dateien kann die Kopieraktivität die folgenden Muster von JSON-Dateien automatisch erkennen und analysieren. Beim Schreiben von Daten in JSON-Dateien können Sie das Dateimuster für die Senke der Kopieraktivität konfigurieren.

- **Typ I: setOfObjects**

    Jede Datei enthält ein einzelnes Objekt, JSON-Zeilen oder verkettete Objekte.

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

    * **JSON-Zeilen (Standard bei Senke)**

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

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Bei Zuordnungsdatenflüssen können Sie in den folgenden Datenspeichern das JSON-Format lesen und schreiben: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Quelleigenschaften

In der folgenden Tabelle sind die von einer JSON-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | Beschreibung | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Platzhalterpfade | Alle Dateien, die dem Platzhalterpfad entsprechen, werden verarbeitet. Überschreibt den Ordner und den Dateipfad, die im Dataset festgelegt sind. | nein | String[] | wildcardPaths |
| Partitionsstammpfad | Für partitionierte Dateidaten können Sie einen Partitionsstammpfad eingeben, um partitionierte Ordner als Spalten zu lesen. | nein | String | partitionRootPath |
| Liste mit den Dateien | Gibt an, ob Ihre Quelle auf eine Textdatei verweist, in der die zu verarbeitenden Dateien aufgelistet sind. | nein | `true` oder `false` | fileList |
| Spalte, in der der Dateiname gespeichert wird | Erstellt eine neue Spalte mit dem Namen und Pfad der Quelldatei. | nein | String | rowUrlColumn |
| Nach Abschluss | Löscht oder verschiebt die Dateien nach der Verarbeitung. Dateipfad beginnt mit dem Containerstamm | nein | Löschen: `true` oder `false` <br> Verschieben: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Nach der letzten Änderung filtern | Filtern Sie Dateien nach dem Zeitpunkt ihrer letzten Änderung. | nein | Timestamp | modifiedAfter <br> modifiedBefore |
| Einzelnes Dokument | Zuordnungsdatenflüsse lesen aus jeder Datei ein JSON-Dokument. | nein | `true` oder `false` | singleDocument |
| Spaltennamen ohne Anführungszeichen | Wenn **Spaltennamen ohne Anführungszeichen** ausgewählt ist, liest Mapping Data Flow JSON-Spalten, die nicht in Anführungszeichen eingeschlossen sind. | nein | `true` oder `false` |  unquotedColumnNames |
| Mit Kommentaren | Wählen Sie **Mit Kommentaren** aus, wenn die JSON-Daten Kommentare im C- oder C++-Stil enthalten. | nein | `true` oder `false` | asComments |
| Einfache Anführungszeichen | Liest JSON-Spalten, die nicht in Anführungszeichen eingeschlossen sind. | nein | `true` oder `false` | singleQuoted |
| Umgekehrter Schrägstrich mit Escapezeichen | Wählen Sie **Umgekehrter Schrägstrich mit Escapezeichen** aus, wenn in den JSON-Daten umgekehrte Schrägstriche als Escapezeichen verwendet werden. | nein | `true` oder `false` | backslashEscape |
| Finden keiner Dateien zulässig | „true“ gibt an, dass kein Fehler ausgelöst wird, wenn keine Dateien gefunden werden. | nein | `true` oder `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>Quellformatoptionen

Wenn Sie ein JSON-Dataset als Quelle in Ihrem Datenfluss verwenden, können Sie fünf weitere Einstellungen festlegen. Diese Einstellungen finden Sie auf der Registerkarte **Quelloptionen**  im Accordion-Element **JSON-Einstellungen**.  

![JSON-Einstellungen](media/data-flow/json-settings.png "JSON-Einstellungen")

#### <a name="default"></a>Standard

JSON-Daten werden standardmäßig im folgenden Format gelesen.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Einzelnes Dokument

Wenn **Einzelnes Dokument** ausgewählt ist, liest Mapping Data Flow ein einzelnes JSON-Dokument aus jeder Datei. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> Wenn Datenflüsse bei der Vorschau Ihrer JSON-Daten einen Fehler zu einem „beschädigten Datensatz“ (corrupt_record) ausgeben, ist es wahrscheinlich, dass die Daten ein einzelnes Dokument in der JSON-Datei enthalten. Durch Festlegen von „einzelnes Dokument“ sollte sich dieser Fehler beseitigen lassen.

#### <a name="unquoted-column-names"></a>Spaltennamen ohne Anführungszeichen

Wenn **Spaltennamen ohne Anführungszeichen** ausgewählt ist, liest Mapping Data Flow JSON-Spalten, die nicht in Anführungszeichen eingeschlossen sind. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Mit Kommentaren

Wählen Sie **Mit Kommentaren** aus, wenn die JSON-Daten Kommentare im C- oder C++-Stil enthalten.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Einfache Anführungszeichen

Wählen Sie **Einfache Anführungszeichen** aus, wenn für JSON-Felder und -Werte einfache Anführungszeichen anstelle von doppelten Anführungszeichen verwendet werden.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Umgekehrter Schrägstrich mit Escapezeichen

Wählen Sie **Umgekehrter Schrägstrich mit Escapezeichen** aus, wenn in den JSON-Daten umgekehrte Schrägstriche als Escapezeichen verwendet werden.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Senkeneigenschaften

In der folgenden Tabelle sind die von einer JSON-Senke unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Einstellungen** bearbeiten.

| Name | Beschreibung | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ordner löschen | Wenn der Zielordner vor dem Schreiben gelöscht wird. | nein | `true` oder `false` | truncate |
| Dateinamenoption | Das Namensformat der geschriebenen Daten. Standardmäßig eine Datei pro Partition im Format `part-#####-tid-<guid>`. | nein | Muster: String <br> Pro Partition: String[] <br> Wie Daten in Spalte: String <br> Ausgabe in eine einzelne Datei: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Erstellen von JSON-Strukturen in abgeleiteten Spalten

Sie können Ihrem Datenfluss eine komplexe Spalte über den Ausdrucks-Editor für abgeleitete Spalten hinzufügen. Fügen Sie in der Transformation für abgeleitete Spalten eine neue Spalte hinzu, und öffnen Sie den Ausdrucks-Generator, indem Sie auf das blaue Feld klicken. Um eine Spalte komplex zu gestalten, können Sie die JSON-Struktur manuell eingeben oder die Benutzeroberfläche verwenden, um Unterspalten interaktiv hinzuzufügen.

#### <a name="using-the-expression-builder-ux"></a>Verwenden der Benutzeroberfläche des Ausdrucks-Generators

Zeigen Sie im Seitenbereich des Ausgabeschemas mit dem Mauszeiger auf eine Spalte, und klicken Sie auf das Pluszeichen. Wählen Sie **Unterspalte hinzufügen** aus, um die Spalte als komplexen Typ zu gestalten.

![Hinzufügen einer Unterspalte](media/data-flow/derive-add-subcolumn.png "Hinzufügen einer Unterspalte")

Sie können weitere Spalten und Unterspalten auf die gleiche Weise hinzufügen. Im Ausdrucks-Editor auf der rechten Seite kann für jedes nicht komplexe Feld ein Ausdruck hinzugefügt werden.

![Hinzufügen einer komplexen Spalte](media/data-flow/derive-complex-column.png "Hinzufügen von Spalten")

#### <a name="entering-the-json-structure-manually"></a>Manuelles Eingeben der JSON-Struktur

Zum manuellen Hinzufügen einer JSON-Struktur fügen Sie eine neue Spalte hinzu, und geben Sie den Ausdruck im Editor ein. Der Ausdruck weist das folgende allgemeine Format auf:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Wenn dieser Ausdruck für eine Spalte mit dem Namen „complexColumn“ eingegeben wird, wird er als der folgende JSON-Code in die Senke geschrieben:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Beispiel für ein manuelles Skript für eine vollständige hierarchische Definition
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)