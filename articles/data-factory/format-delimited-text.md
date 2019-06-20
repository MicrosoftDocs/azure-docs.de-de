---
title: Textformat mit Trennzeichen in Azure Data Factory | Microsoft-Dokumentation
description: In diesem Thema wird der Umgang mit dem Textformat mit Trennzeichen in Azure Data Factory beschrieben.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 407b8ba2fda35d3acbf1b425bb15fe20778613d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146002"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Textformat mit Trennzeichen in Azure Data Factory

Beachten Sie diesen Artikel, wenn Sie die **Textdateien mit Trennzeichen analysieren oder die Daten im Textformat mit Trennzeichen schreiben** möchten. 

Das Textformat mit Trennzeichen wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste mit Eigenschaften, die vom DelimitedText-Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Die type-Eigenschaft des Datasets muss auf **DelimitedText** festgelegt werden. | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften. **Informationen hierzu finden Sie im Abschnitt „Dataset-Eigenschaften“ des Artikels über Connectors**. | Ja      |
| columnDelimiter  | Das Zeichen, das in einer Datei zum Trennen von Spalten verwendet wird. Derzeit wird das mehrstellige Trennzeichen nur für Mapping Data Flow unterstützt, nicht aber für die Kopieraktivität. <br>Der Standardwert ist **Komma `,`** . wenn das Spaltentrennzeichen als leere Zeichenfolge definiert ist, d.h. kein Trennzeichen, wird die gesamte Zeile als eine einzige Spalte betrachtet. | Nein       |
| rowDelimiter     | Das Einzelzeichen oder „\r\n“, das in einer Datei zum Trennen von Zeilen verwendet wird.<br>Der Standardwert ist einer der folgenden Werte  **beim Lesen: ["\r\n", "\r", "\r", "\n"]** , und **"\n" oder "\r\n" auf Schreiben** für Mapping Data Flow bzw. die Kopieraktivität. <br>Wenn `rowDelimiter` auf kein Trennzeichen (leere Zeichenfolge) gesetzt ist, darf auch `columnDelimiter` auf kein Trennzeichen (leere Zeichenfolge) gesetzt werden, was bedeutet, dass der gesamte Inhalt als Einzelwert behandelt wird. | Nein       |
| quoteChar        | Das einzelne Zeichen, um Spaltenwerte mit Anführungszeichen zu versehen, wenn es ein Spaltentrennzeichen enthält. <br>Der Standardwert ist ein **doppeltes Anführungszeichen** `"`. <br>Für Mapping Data Flow darf `quoteChar` keine leere Zeichenfolge sein. <br>Wenn `quoteChar` als leere Zeichenfolge definiert ist, bedeutet dies, dass es kein Anführungszeichen gibt und der Spaltenwert nicht in Anführungszeichen gesetzt wird, und `escapeChar` wird verwendet, um das Spaltentrennzeichen und sich selbst zu escapen. | Nein       |
| escapeChar       | Das einzelne Zeichen zum Escapen von Anführungszeichen innerhalb eines mit Anführungszeichen versehenen Wertes.<br>Der Standardwert ist ein **umgekehrter Schrägstrich `\`** . <br>Für Mapping Data Flow darf `escapeChar` keine leere Zeichenfolge sein. <br/>Wenn `escapeChar` als leere Zeichenfolge definiert ist, muss für die Kopieraktivität auch `quoteChar` als leere Zeichenfolge festgelegt werden, wobei in diesem Fall darauf zu achten ist, dass alle Spaltenwerte keine Trennzeichen enthalten. | Nein       |
| firstRowAsHeader | Gibt an, ob die erste Zeile als Kopfzeile mit Spaltennamen behandelt bzw. zu dieser gemacht werden soll.<br>Zulässige Werte sind **true** und **false** (Standard). | Nein       |
| nullValue        | Gibt eine Zeichenfolgendarstellung von Null-Werten an. <br>Der Standardwert ist eine **leere Zeichenfolge**. | Nein       |
| encodingName     | Der zu Lesen/Schreiben von Testdateien verwendete Codierungstyp. <br>Es sind die folgenden Werte zulässig: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", “UTF-7”, "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258”.<br>Beachten Sie, dass Mapping Data Flow keine UTF-7-Codierung unterstützt. | Nein       |
| compressionCodec | Der zum Lesen und Schreiben von Textdateien verwendete Komprimierungscodec. <br>Zulässige Werte sind **bzip2**, **Gzip**, **deflate**, **ZipDeflate**, **snappy** oder **lz4**, die beim Speichern der Datei verwendet werden können. <br>Hinweis: Die Kopieraktivität unterstützt derzeit „snappy“ und „lz4“ nicht, und Mapping Data Flow unterstützt „ZipDeflate“ nicht. | Nein       |
| compressionLevel | Das Komprimierungsverhältnis. <br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . | Nein       |

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
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit Eigenschaften, die von der DelimitedText-Quelle und -Senke unterstützt werden.

### <a name="delimited-text-as-source"></a>Durch Trennzeichen getrennter Text als Quelle 

Die folgenden Eigenschaften werden im Abschnitt ***\*source\**** der Kopieraktivität unterstützt.

| Eigenschaft       | BESCHREIBUNG                                                  | Erforderlich |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **DelimitedTextSource** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **Leseeinstellungen für durch Trennzeichen getrennten Text** finden Sie in der Tabelle unten. | Nein       |
| storeSettings  | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **Leseeinstellungen für durch Trennzeichen getrennten Text** finden Sie unter `formatSettings`:

| Eigenschaft      | BESCHREIBUNG                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Der Typ des FormatSettings muss festgelegt werden, um **DelimitedTextReadSetting**. | Ja      |
| skipLineCount | Gibt an, wie viele **nicht leere** Zeilen beim Lesen von Daten aus Eingabedateien übersprungen werden sollen. <br>Wenn sowohl skipLineCount und firstRowAsHeader angegeben sind, werden erst die Zeilen übersprungen und dann die Kopfzeileninformationen aus der Eingabedatei gelesen. | Nein       |

### <a name="delimited-text-as-sink"></a>Durch Trennzeichen getrennter Text als Senke

Die folgenden Eigenschaften werden im Abschnitt ***\*sink\**** der Kopieraktivität unterstützt:

| Eigenschaft       | BESCHREIBUNG                                                  | Erforderlich |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **DelimitedTextSink** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **Schreibeinstellungen für durch Trennzeichen getrennten Text** finden Sie in der Tabelle unten. |          |
| storeSettings  | Eine Gruppe von Eigenschaften für das Schreiben von Daten in einen Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Schreibeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **Schreibeinstellungen für durch Trennzeichen getrennten Text** finden Sie unter `formatSettings`:

| Eigenschaft      | BESCHREIBUNG                                                  | Erforderlich                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | Der Typ des FormatSettings muss festgelegt werden, um **DelimitedTextWriteSetting**. | Ja                                                   |
| fileExtension | Die Dateierweiterung, mit der die Ausgabedateien benannt werden, z.B. `.csv`, `.txt`. Es muss angegeben werden, wenn `fileName` nicht in der Ausgabe des DelimitedText-Datasets angegeben ist. | Ja, wenn kein Dateiname im Ausgabedataset angegeben ist |

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Ausführliche Informationen hierzu finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Mapping Data Flow.

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)