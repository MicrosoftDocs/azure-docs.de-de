---
title: Textformat mit Trennzeichen in Azure Data Factory
description: In diesem Thema wird der Umgang mit dem Textformat mit Trennzeichen in Azure Data Factory beschrieben.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: jingwang
ms.openlocfilehash: 0fa68a8dbdcb1f2c0cc4af1b4df751fd81c0de14
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854630"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Textformat mit Trennzeichen in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Beachten Sie diesen Artikel, wenn Sie die **Textdateien mit Trennzeichen analysieren oder die Daten im Textformat mit Trennzeichen schreiben** möchten. 

Das Textformat mit Trennzeichen wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste mit Eigenschaften, die vom DelimitedText-Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Die type-Eigenschaft des Datasets muss auf **DelimitedText** festgelegt werden. | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften.  | Ja      |
| columnDelimiter  | Das Zeichen, das in einer Datei zum Trennen von Spalten verwendet wird. <br>Der Standardwert ist das **Komma `,`** . Wenn das Spaltentrennzeichen als leere Zeichenfolge, d. h. kein Trennzeichen, definiert wurde, wird die gesamte Zeile als eine einzige Spalte betrachtet.<br>Zurzeit wird das Spaltentrennzeichen als leere oder aus mehreren Zeichen bestehende Zeichenfolge nur für den Zuordnungsdatenfluss unterstützt, aber nicht für die Kopieraktivität.  | Nein       |
| rowDelimiter     | Das Einzelzeichen oder „\r\n“, das in einer Datei zum Trennen von Zeilen verwendet wird. <br>Der Standardwert ist einer der folgenden Werte: **[ „\r\n“, „\r“, „\r“, „\n“] (beim Lesen)** und **„\n“ oder „\r\n“ (beim Schreiben)** für Mappingdatenfluss bzw. Kopieraktivität. <br>Wenn das Zeilentrennzeichen auf „kein Trennzeichen“ (leere Zeichenfolge) festgelegt wird, muss auch das Spaltentrennzeichen auf „kein Trennzeichen“ (leere Zeichenfolge) festgelegt werden. Dies bedeutet, dass der gesamte Inhalt als Einzelwert behandelt wird.<br>Zurzeit wird das Zeilentrennzeichen als leere Zeichenfolge nur für den Zuordnungsdatenfluss unterstützt, aber nicht für die Kopieraktivität. | Nein       |
| quoteChar        | Das einzelne Zeichen, um Spaltenwerte mit Anführungszeichen zu versehen, wenn es ein Spaltentrennzeichen enthält. <br>Der Standardwert ist ein **doppeltes Anführungszeichen** `"`. <br>Wenn `quoteChar` als leere Zeichenfolge definiert ist, bedeutet dies, dass es kein Anführungszeichen gibt und der Spaltenwert nicht in Anführungszeichen gesetzt wird. Außerdem wird `escapeChar` verwendet, um das Spaltentrennzeichen und sich selbst mit Escapezeichen zu versehen. | Nein       |
| escapeChar       | Das einzelne Zeichen zum Escapen von Anführungszeichen innerhalb eines mit Anführungszeichen versehenen Wertes.<br>Der Standardwert ist ein **umgekehrter Schrägstrich `\`** . <br>Wenn `escapeChar` als leere Zeichenfolge definiert ist, muss auch `quoteChar` als leere Zeichenfolge festgelegt werden. In diesem Fall ist darauf zu achten, dass die Spaltenwerte keine Trennzeichen enthalten. | Nein       |
| firstRowAsHeader | Gibt an, ob die erste Zeile als Kopfzeile mit Spaltennamen behandelt bzw. zu dieser gemacht werden soll.<br>Zulässige Werte sind **true** und **false** (Standard).<br>Wenn der Wert für die erste Zeile als Kopfzeile „false“ lautet, werden bei der Vorschau der Benutzeroberflächendaten und der Ausgabe der Lookup-Aktivität automatisch Spaltennamen als „Prop_{n}“ (beginnend mit 0) generiert. Für die Kopieraktivität ist eine [explizite Zuordnung](copy-activity-schema-and-type-mapping.md#explicit-mapping) von der Quelle zur Senke erforderlich, Spalten werden nach Ordnungszahl (beginnend mit 1) gesucht, und der Zuordnungsdatenfluss listet und sucht Spalten mit dem Namen „Column_{n}“ (beginnend mit 1).  | Nein       |
| nullValue        | Gibt eine Zeichenfolgendarstellung von Null-Werten an. <br>Der Standardwert ist eine **leere Zeichenfolge**. | Nein       |
| encodingName     | Der zu Lesen/Schreiben von Testdateien verwendete Codierungstyp. <br>Es sind die folgenden Werte zulässig: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", “UTF-7”, "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258”.<br>Beachten Sie, dass Mappingdatenflüsse keine UTF-7-Codierung unterstützen. | Nein       |
| compressionCodec | Der zum Lesen und Schreiben von Textdateien verwendete Komprimierungscodec. <br>Zulässige Werte sind **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** oder **lz4**. Standardmäßig erfolgt keine Komprimierung. <br>**Hinweis**: Derzeit unterstützt die Kopieraktivität „snappy“ und „lz4“ nicht, und der Zuordnungsdatenfluss unterstützt „ZipDeflate“, „TarGzip“ und „Tar“ nicht. <br>**Hinweis**: Bei Verwendung der Kopieraktivität zum Dekomprimieren von **ZipDeflate**/**TarGzip**/**Tar**-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher werden diese Dateien standardmäßig in den Ordner `<path specified in dataset>/<folder named as source compressed file>/` extrahiert. Verwenden Sie in diesem Fall `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` als [Quelle der Kopieraktivität](#delimited-text-as-source), um zu steuern, ob der Name der komprimierten Dateien als Ordnerstruktur beibehalten werden soll. | Nein       |
| compressionLevel | Das Komprimierungsverhältnis. <br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) . | Nein       |

Nachfolgend sehen Sie das Beispiel eines DelimitedText-Datasets in Azure Blob Storage:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit Eigenschaften, die von der DelimitedText-Quelle und -Senke unterstützt werden.

### <a name="delimited-text-as-source"></a>Durch Trennzeichen getrennter Text als Quelle 

Die folgenden Eigenschaften werden im Abschnitt **\_source\*** der Kopieraktivität unterstützt.

| Eigenschaft       | BESCHREIBUNG                                                  | Erforderlich |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **DelimitedTextSource** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **Leseeinstellungen für durch Trennzeichen getrennten Text** finden Sie in der Tabelle unten. |  Nein       |
| storeSettings  | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. | Nein       |

Unterstützte **Leseeinstellungen für durch Trennzeichen getrennten Text** finden Sie unter `formatSettings`:

| Eigenschaft      | BESCHREIBUNG                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Der Typ von „formatSettings“ muss auf **DelimitedTextReadSettings** festgelegt werden. | Ja      |
| skipLineCount | Gibt an, wie viele **nicht leere** Zeilen beim Lesen von Daten aus Eingabedateien übersprungen werden sollen. <br>Wenn „skipLineCount“ und „firstRowAsHeader“ gleichzeitig angegeben sind, werden die Zeilen zuerst übersprungen, und anschließend werden die Kopfzeileninformationen aus der Eingabedatei gelesen. | Nein       |
| compressionProperties | Eine Gruppe von Eigenschaften zur Festlegung, wie Daten bei einem bestimmten Komprimierungscodec dekomprimiert werden können. | Nein       |
| preserveZipFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `ZipDeflateReadSettings`* ) |  Diese Eigenschaft gilt, wenn das Eingabedataset mit der **ZipDeflate**-Komprimierung konfiguriert wurde. Sie gibt an, ob der Name der ZIP-Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standard), schreibt Data Factory entzippte Dateien in `<path specified in dataset>/<folder named as source zip file>/`.<br>– Lautet der Wert **false**, schreibt Data Factory entzippte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen ZIP-Quelldateien vorliegen, um „Racing“ oder ein unerwartetes Verhalten zu vermeiden.  | Nein |
| preserveCompressionFileNameAsFolder<br>(*unter `compressionProperties`->`type`: `TarGZipReadSettings` oder `TarReadSettings`* )  | Gilt, wenn das Eingabedataset mit der Komprimierung **TarGzip**/**Tar** konfiguriert wurde. Gibt an, ob der Name der komprimierten Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standardwert), schreibt Data Factory dekomprimierte Dateien in `<path specified in dataset>/<folder named as source compressed file>/`. <br>– Lautet der Wert **false**, schreibt Data Factory dekomprimierte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen Quelldateien vorliegen, um Racebedingungen oder ein unerwartetes Verhalten zu vermeiden. | Nein |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Durch Trennzeichen getrennter Text als Senke

Die folgenden Eigenschaften werden im Abschnitt **\_sink\*** der Kopieraktivität unterstützt.

| Eigenschaft       | BESCHREIBUNG                                                  | Erforderlich |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **DelimitedTextSink** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **Schreibeinstellungen für durch Trennzeichen getrennten Text** finden Sie in der Tabelle unten. |    Nein      |
| storeSettings  | Eine Gruppe von Eigenschaften für das Schreiben von Daten in einen Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Schreibeinstellungen.  | Nein       |

Unterstützte **Schreibeinstellungen für durch Trennzeichen getrennten Text** finden Sie unter `formatSettings`:

| Eigenschaft      | BESCHREIBUNG                                                  | Erforderlich                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Der Typ von „formatSettings“ muss auf **DelimitedTextWriteSettings** festgelegt werden. | Ja                                                   |
| fileExtension | Die Dateierweiterung, mit der die Ausgabedateien benannt werden, z. B. `.csv`, `.txt`. Es muss angegeben werden, wenn `fileName` nicht in der Ausgabe des DelimitedText-Datasets angegeben ist. Wenn der Dateiname im Ausgabedataset konfiguriert wurde, wird er als Name für die Senkendatei verwendet, und die Dateierweiterungseinstellung wird ignoriert.  | Ja, wenn kein Dateiname im Ausgabedataset angegeben ist |
| maxRowsPerFile | Wenn Sie Daten in einen Ordner schreiben, können Sie in mehrere Dateien zu schreiben und die maximale Anzahl von Zeilen pro Datei angeben.  | Nein |
| fileNamePrefix | Gilt, wenn `maxRowsPerFile` konfiguriert ist.<br> Geben Sie das Dateinamenpräfix beim Schreiben von Daten in mehrere Dateien an, das zu diesem Muster führt: `<fileNamePrefix>_00000.<fileExtension>`. Wenn keine Angabe erfolgt, wird das Dateinamenpräfix automatisch generiert. Diese Eigenschaft findet keine Anwendung, wenn die Quelle ein dateibasierter Speicher oder ein [Datenspeicher mit aktivierter Partitionsoption](copy-activity-performance-features.md) ist.  | Nein |

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Bei Zuordnungsdatenflüssen können Sie in den folgenden Datenspeichern das Textformat mit Trennzeichen lesen und schreiben: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Quelleigenschaften

In der folgenden Tabelle sind die von einer DelimitedText-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Platzhalterpfade | Alle Dateien, die dem Platzhalterpfad entsprechen, werden verarbeitet. Überschreibt den Ordner und den Dateipfad, die im Dataset festgelegt sind. | nein | String[] | wildcardPaths |
| Partitionsstammpfad | Für partitionierte Dateidaten können Sie einen Partitionsstammpfad eingeben, um partitionierte Ordner als Spalten zu lesen. | nein | String | partitionRootPath |
| Liste mit den Dateien | Gibt an, ob Ihre Quelle auf eine Textdatei verweist, in der die zu verarbeitenden Dateien aufgelistet sind. | nein | `true` oder `false` | fileList |
| Mehrzeilig | Gibt an, ob die Quelldatei Zeilen enthält, die sich über mehrere Zeilen erstrecken. Mehrzeilige Werte müssen in Anführungszeichen gesetzt werden. | nein `true` oder `false` | multiLineRow |
| Spalte, in der der Dateiname gespeichert wird | Erstellt eine neue Spalte mit dem Namen und Pfad der Quelldatei. | nein | String | rowUrlColumn |
| Nach Abschluss | Löscht oder verschiebt die Dateien nach der Verarbeitung. Dateipfad beginnt mit dem Containerstamm | nein | Löschen: `true` oder `false` <br> Verschieben: `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Nach der letzten Änderung filtern | Filtern Sie Dateien nach dem Zeitpunkt ihrer letzten Änderung. | nein | Timestamp | modifiedAfter <br> modifiedBefore |
| Finden keiner Dateien zulässig | „true“ gibt an, dass kein Fehler ausgelöst wird, wenn keine Dateien gefunden werden. | nein | `true` oder `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Quellbeispiel

Das folgende Bild ist ein Beispiel für eine DelimitedText-Quellkonfiguration bei Zuordnungsdatenflüssen.

![DelimitedText-Quelle](media/data-flow/delimited-text-source.png)

Das zugehörige Datenflussskript ist:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Datenflussquellen unterstützen eine begrenzte Anzahl von Linux-Platzhaltern, die von Hadoop-Dateisystemen unterstützt werden.

### <a name="sink-properties"></a>Senkeneigenschaften

In der folgenden Tabelle sind die von einer DelimitedText-Senke unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Einstellungen** bearbeiten.

| Name | BESCHREIBUNG | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Ordner löschen | Wenn der Zielordner vor dem Schreiben gelöscht wird. | nein | `true` oder `false` | truncate |
| Dateinamenoption | Das Namensformat der geschriebenen Daten. Standardmäßig eine Datei pro Partition im Format `part-#####-tid-<guid>`. | nein | Muster: String <br> Pro Partition: String[] <br> Wie Daten in Spalte: String <br> Ausgabe in eine einzelne Datei: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |
| Alle in Anführungszeichen | Gibt an, ob alle Werte in Anführungszeichen eingeschlossen werden. | nein | `true` oder `false` | quoteAll |

### <a name="sink-example"></a>Senkenbeispiel

Das folgende Bild ist ein Beispiel für eine DelimitedText-Senkenkonfiguration bei Zuordnungsdatenflüssen.

![DelimitedText-Senke](media/data-flow/delimited-text-sink.png)

Das zugehörige Datenflussskript ist:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
