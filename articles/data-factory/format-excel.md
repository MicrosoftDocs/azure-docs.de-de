---
title: Excel-Format in Azure Data Factory
description: In diesem Thema wird der Umgang mit dem Excel-Format in Azure Data Factory beschrieben.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: 8f19ccc90c44ef90cee7bb1ae881086321e863b6
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902033"
---
# <a name="excel-format-in-azure-data-factory"></a>Excel-Format in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verwenden Sie diesen Artikel, wenn Sie die **Excel-Dateien** analysieren möchten. Azure Data Factory unterstützt sowohl „.xls“ als auch „.xlsx“.

Das Excel-Format wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md). Es wird als Quelle, aber nicht als Senke unterstützt. 

**Hinweis:** Das XLS-Format wird bei Verwendung von [HTTP](connector-http.md) nicht unterstützt. 

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die vom Excel-Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Die type-Eigenschaft des Datasets muss auf **Excel** festgelegt werden.   | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften. | Ja      |
| sheetName        | Der Name des Excel-Arbeitsblatts zum Lesen der Daten.                       | Geben Sie `sheetName` oder `sheetIndex` an. |
| sheetIndex | Der Excel-Arbeitsblattindex zum Lesen von Daten, beginnend bei 0 (null). | Geben Sie `sheetName` oder `sheetIndex` an. |
| range            | Der Zellenbereich im jeweiligen Arbeitsblatt zum Ermitteln der selektiven Daten, z. B.:<br>Nicht angegeben: Das gesamte Arbeitsblatt wird als Tabelle ab der ersten nicht leeren Zeile und Spalte gelesen.<br>- `A3`: Eine Tabelle wird ab der angegebenen Zelle gelesen, wobei alle Zeilen darunter und alle Spalten rechts davon dynamisch erkannt werden.<br>- `A3:H5`: Dieser feste Bereich wird als Tabelle gelesen.<br>- `A3:A3`: Diese einzelne Zelle wird gelesen. | Nein       |
| firstRowAsHeader | Gibt an, ob die erste Zeile des jeweiligen Arbeitsblatts bzw. Bereichs als Headerzeile mit den Namen der Spalten behandelt werden soll.<br>Zulässige Werte sind **true** und **false** (Standard). | Nein       |
| nullValue        | Gibt eine Zeichenfolgendarstellung von Null-Werten an. <br>Der Standardwert ist eine **leere Zeichenfolge**. | Nein       |
| compression | Gruppe von Eigenschaften zum Konfigurieren der Dateikomprimierung. Konfigurieren Sie diesen Abschnitt, wenn Sie während der Aktivitätsausführung eine Komprimierung/Dekomprimierung durchführen möchten. | Nein |
| type<br/>(*unter `compression`* ) | Der zum Lesen und Schreiben von JSON-Dateien verwendete Komprimierungscodec. <br>Zulässige Werte sind **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **Tar**, **snappy** und **lz4**. Standardmäßig erfolgt keine Komprimierung.<br>**Beachten Sie**, dass die Kopieraktivität zurzeit „snappy“ und „lz4“ und der Zuordnungsdatenfluss „ZipDeflate“, „TarGzip“ und „Tar“ nicht unterstützt.<br>**Hinweis**: Bei Verwendung der Kopieraktivität zum Dekomprimieren einer oder mehrerer **ZipDeflate**-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher werden Dateien in den folgenden Ordner extrahiert: `<path specified in dataset>/<folder named as source zip file>/`. | Nein.  |
| level<br/>(*unter `compression`* ) | Das Komprimierungsverhältnis. <br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) . | Nein       |

Unten ist ein Beispiel für ein Excel-Dataset in Azure Blob Storage angegeben:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit den Eigenschaften, die von der Excel-Quelle unterstützt werden.

### <a name="excel-as-source"></a>Excel als Quelle 

Die folgenden Eigenschaften werden im Abschnitt **_\_source\*** * der Copy-Aktivität unterstützt.

| Eigenschaft      | BESCHREIBUNG                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf **ExcelSource** festgelegt sein. | Ja      |
| storeSettings | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. | Nein       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Bei Zuordnungsdatenflüssen können Sie das Excel-Format in den folgenden Datenspeichern lesen: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Sie können auf Excel-Dateien entweder mit einem Excel-Dataset oder einem [Inlinedataset](data-flow-source.md#inline-datasets) verweisen.

### <a name="source-properties"></a>Quelleigenschaften

In der folgenden Tabelle sind die von einer Excel-Quelle unterstützten Eigenschaften aufgeführt. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten. Bei Verwendung eines Inlinedatasets werden zusätzliche Dateieinstellungen angezeigt. Diese entsprechen den Eigenschaften, die im Abschnitt zu den [Dataseteigenschaften](#dataset-properties) beschrieben sind.

| Name                      | BESCHREIBUNG                                                  | Erforderlich | Zulässige Werte                                            | Datenflussskript-Eigenschaft         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Platzhalterpfade           | Alle Dateien, die dem Platzhalterpfad entsprechen, werden verarbeitet. Überschreibt den Ordner und den Dateipfad, die im Dataset festgelegt sind. | nein       | String[]                                                  | wildcardPaths                     |
| Partitionsstammpfad       | Für partitionierte Dateidaten können Sie einen Partitionsstammpfad eingeben, um partitionierte Ordner als Spalten zu lesen. | nein       | String                                                    | partitionRootPath                 |
| Liste mit den Dateien             | Gibt an, ob Ihre Quelle auf eine Textdatei verweist, in der die zu verarbeitenden Dateien aufgelistet sind. | nein       | `true` oder `false`                                         | fileList                          |
| Spalte, in der der Dateiname gespeichert wird | Erstellt eine neue Spalte mit dem Namen und Pfad der Quelldatei.       | nein       | String                                                    | rowUrlColumn                      |
| Nach Abschluss          | Löscht oder verschiebt die Dateien nach der Verarbeitung. Dateipfad beginnt mit dem Containerstamm | nein       | Löschen: `true` oder `false` <br> Verschieben: `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Nach der letzten Änderung filtern   | Filtern Sie Dateien nach dem Zeitpunkt ihrer letzten Änderung. | nein       | Timestamp                                                 | modifiedAfter <br> modifiedBefore |
| Finden keiner Dateien zulässig | „true“ gibt an, dass kein Fehler ausgelöst wird, wenn keine Dateien gefunden werden. | nein | `true` oder `false` | ignoreNoFilesFound |

### <a name="source-example"></a>Quellbeispiel

Die Abbildung unten enthält ein Beispiel für eine Excel-Quellkonfiguration in Zuordnungsdatenflüssen per Datasetmodus.

![Excel-Quelle](media/data-flow/excel-source.png)

Das zugehörige Datenflussskript ist:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Bei Verwendung eines Inlinedatasets werden im Zuordnungsdatenfluss die folgenden Quelloptionen angezeigt.

![Inlinedataset für Excel-Quelle](media/data-flow/excel-source-inline-dataset.png)

Das zugehörige Datenflussskript ist:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)