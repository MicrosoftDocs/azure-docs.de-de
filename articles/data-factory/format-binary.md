---
title: Binärformat in Azure Data Factory
description: In diesem Thema wird der Umgang mit dem Binärformat in Azure Data Factory beschrieben.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: be4338942057cf52bf26b7fd1e15f77e724d49f8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348907"
---
# <a name="binary-format-in-azure-data-factory"></a>Binärformat in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Das Binärformat wird für folgende Connectors unterstützt: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

Sie können ein binäres Dataset in der [Kopieraktivität](copy-activity-overview.md), der [GetMetadata-Aktivität](control-flow-get-metadata-activity.md) oder der [Delete-Aktivität](delete-activity.md) verwenden. Wenn Sie ein binäres Dataset verwenden, analysiert ADF den Dateiinhalt nicht, sondern lässt ihn unverändert. 

>[!NOTE]
>Wenn Sie ein binäres Dataset in der Kopieraktivität verwenden, können Sie aus diesem Dataset nur in ein binäres Dataset kopieren.

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom binären Dataset unterstützt werden.

| Eigenschaft         | BESCHREIBUNG                                                  | Erforderlich |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Die „type“-Eigenschaft des Datasets muss auf **Binär** festgelegt werden. | Ja      |
| location         | Speicherorteinstellungen der Datei(en) Jeder dateibasierte Connector verfügt unter `location` über seinen eigenen Speicherorttyp und unterstützte Eigenschaften. **Informationen hierzu finden Sie im Abschnitt „Dataset-Eigenschaften“ des Artikels über Connectors**. | Ja      |
| compression | Gruppe von Eigenschaften zum Konfigurieren der Dateikomprimierung. Konfigurieren Sie diesen Abschnitt, wenn Sie während der Aktivitätsausführung eine Komprimierung/Dekomprimierung durchführen möchten. | Nein |
| type | Der zum Lesen und Schreiben von Binärdateien verwendete Codec für die Komprimierung. <br>Zulässige Werte sind **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **Tar** und **TarGzip**. <br>**Beachten Sie**, dass bei Verwendung der Kopieraktivität zum Dekomprimieren von **ZipDeflate**-/**TarGzip**-/**Tar**-Dateien und zum Schreiben in den dateibasierten Senkendatenspeicher diese Dateien standardmäßig in den Ordner `<path specified in dataset>/<folder named as source compressed file>/` extrahiert werden. Verwenden Sie in diesem Fall `preserveZipFileNameAsFolder`/`preserveCompressionFileNameAsFolder` als [Quelle der Kopieraktivität](#binary-as-source), um zu steuern, ob der Name der komprimierten Dateien als Ordnerstruktur beibehalten werden soll.| Nein       |
| level | Das Komprimierungsverhältnis. Wenden Sie es an, wenn das Dataset in der Senke der Kopieraktivität verwendet wird.<br>Zulässige Werte sind **Optimal** oder **Sehr schnell**.<br>- **Sehr schnell:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist.<br>- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. Weitere Informationen finden Sie im Thema [Komprimierungsstufe](/dotnet/api/system.io.compression.compressionlevel) . | Nein       |

Nachfolgend sehen Sie ein Beispiel für ein binäres Dataset in Azure Blob Storage:

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der binären Quelle und Senke unterstützt werden.

>[!NOTE]
>Wenn Sie ein binäres Dataset in der Kopieraktivität verwenden, können Sie aus diesem Dataset nur in ein binäres Dataset kopieren.

### <a name="binary-as-source"></a>„Binär“ als Quelle

Die folgenden Eigenschaften werden im Abschnitt **_\_source\*** * der Copy-Aktivität unterstützt.

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Die „type“-Eigenschaft der Quelle für die Kopieraktivität muss auf **BinarySource** festgelegt werden. | Ja      |
| formatSettings | Eine Gruppe von Eigenschaften. Weitere Informationen zu **Leseeinstellungen für Binärdateien** finden Sie in der Tabelle unten. | Nein       |
| storeSettings | Eine Gruppe von Eigenschaften für das Lesen von Daten aus einem Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Leseeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

Unterstützte **Leseeinstellungen für Binärdateien** unter `formatSettings`:

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Der Typ von „formatSettings“ muss auf **BinaryReadSettings** festgelegt werden. | Ja      |
| compressionProperties | Eine Gruppe von Eigenschaften zur Festlegung, wie Daten bei einem bestimmten Komprimierungscodec dekomprimiert werden können. | Nein       |
| preserveZipFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `ZipDeflateReadSettings`* ) | Diese Eigenschaft gilt, wenn das Eingabedataset mit der **ZipDeflate**-Komprimierung konfiguriert wurde. Sie gibt an, ob der Name der ZIP-Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>– Lautet der Wert **true** (Standard), schreibt Data Factory entzippte Dateien in `<path specified in dataset>/<folder named as source zip file>/`.<br>– Lautet der Wert **false**, schreibt Data Factory entzippte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen ZIP-Quelldateien vorliegen, um „Racing“ oder ein unerwartetes Verhalten zu vermeiden.  | Nein |
| preserveCompressionFileNameAsFolder<br>(*unter `compressionProperties`->`type` als `TarGZipReadSettings` oder `TarReadSettings`* ) | Gilt, wenn das Eingabedataset mit der Komprimierung **TarGzip**/**Tar** konfiguriert wurde. Gibt an, ob der Name der komprimierten Quelldatei während Kopiervorgängen als Ordnerstruktur beibehalten werden soll.<br>- Lautet der Wert **true** (Standard), schreibt Data Factory dekomprimierte Dateien in `<path specified in dataset>/<folder named as source compressed file>/`. <br>- Lautet der Wert **false**, schreibt Data Factory dekomprimierte Dateien direkt in `<path specified in dataset>`. Stellen Sie sicher, dass keine doppelten Dateinamen in unterschiedlichen Quelldateien vorliegen, um „Racing“ oder ein unerwartetes Verhalten zu vermeiden. | Nein |

```json
"activities": [
    {
        "name": "CopyFromBinary",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true,
                    "deleteFilesAfterCompletion": true
                },
                "formatSettings": {
                    "type": "BinaryReadSettings",
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

### <a name="binary-as-sink"></a>„Binär“ als Senke

Die folgenden Eigenschaften werden im Abschnitt **_\_sink\*** * der Kopieraktivität unterstützt.

| Eigenschaft      | Beschreibung                                                  | Erforderlich |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | Die „type“-Eigenschaft der Quelle für die Kopieraktivität muss auf **BinarySink** festgelegt werden. | Ja      |
| storeSettings | Eine Gruppe von Eigenschaften für das Schreiben von Daten in einen Datenspeicher. Jeder dateibasierte Connector verfügt unter `storeSettings` über eigene unterstützte Schreibeinstellungen. **Informationen hierzu finden Sie im Abschnitt über die Eigenschaften der Kopieraktivität im Artikel über Connectors**. | Nein       |

## <a name="next-steps"></a>Nächste Schritte

- [Kopieraktivität – Übersicht](copy-activity-overview.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)