---
title: Kopieren von Daten aus Google Cloud Storage mithilfe von Azure Data Factory
description: Es wird beschrieben, wie Sie mithilfe von Azure Data Factory Daten aus Google Cloud Storage in unterstützte Senkendatenspeicher kopieren.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: jingwang
ms.openlocfilehash: 4d8ddb89055d318aaea9ada7f38f908223ff5a57
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388446"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Kopieren von Daten aus Google Cloud Storage mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten von Google Cloud Storage (GCS) kopieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Google Cloud Storage-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Der Google Cloud Storage-Connector unterstützt insbesondere das Kopieren von Dateien im jeweiligen Zustand oder Analysieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md). Er nutzt die S3-kompatible Interoperabilität von GCS.

## <a name="prerequisites"></a>Voraussetzungen

Für Ihr Google Cloud Storage-Konto ist folgendes Setup erforderlich:

1. Aktivieren der Interoperabilität für Ihr Google Cloud Storage-Konto
2. Festlegen des Standardprojekts, das die Daten enthält, die Sie aus dem Ziel-GCS-Bucket kopieren möchten
3. Erstellen eines Dienstkontos und Definieren der richtigen Berechtigungsebenen mithilfe von Cloud IAM auf GCP 
4. Generieren der Zugriffsschlüssel für dieses Dienstkonto

![Abrufen des Zugriffsschlüssels für Google Cloud Storage](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Sie müssen zum Kopieren von Daten aus Google Cloud Storage sicherstellen, dass Ihnen die folgenden Berechtigungen für Objektvorgänge erteilt wurden: ` storage.objects.get` und ` storage.objects.list`

Wenn Sie die Data Factory-Benutzeroberfläche für die Erstellung verwenden, ist eine zusätzliche ` storage.buckets.list`-Berechtigung für Vorgänge wie das Testen der Verbindung mit dem verknüpften Dienst und das Durchsuchen vom Stamm erforderlich. Wenn Sie diese Berechtigung nicht erteilen möchten, können Sie die Optionen „Test connection to file path“ (Verbindung mit Dateipfad testen) bzw. „Browse from specified path“ (Von angegebenem Pfad suchen) auf der Benutzeroberfläche auswählen.

Eine vollständige Liste der Google Cloud Storage-Rollen und zugehöriger Berechtigungen finden Sie unter [IAM-Rollen für Cloud Storage](https://cloud.google.com/storage/docs/access-control/iam-roles) auf der Google Cloud-Website.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Google Cloud Storage-spezifischen Data Factory-Entitäten verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für mit Google Cloud Storage verknüpfte Dienste unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **GoogleCloudStorage** festgelegt werden. | Ja |
| accessKeyId | ID des geheimen Zugriffsschlüssels. Informationen zum Ermitteln des Zugriffsschlüssels und des Geheimnisses finden Sie unter [Voraussetzungen](#prerequisites). |Ja |
| secretAccessKey | Der geheime Zugriffsschlüssel selbst. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Geben Sie den benutzerdefinierten GCS-Endpunkt als `https://storage.googleapis.com` an. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |

Hier sehen Sie ein Beispiel:

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für Google Cloud Storage unter `location`-Einstellungen in einem formatbasierten Dataset unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die **type**-Eigenschaft unter `location` im Dataset muss auf **GoogleCloudStorageLocation** festgelegt werden. | Ja      |
| bucketName | Der Name des GCS-Buckets.                                          | Ja      |
| folderPath | Der Pfad zum Ordner unter dem angegebenen Bucket. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen Bucket und Ordnerpfad. Wenn Sie einen Platzhalter verwenden möchten, um die Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Google Cloud Storage-Quelle unterstützt werden.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage als Quelle

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für Google Cloud Storage unter `storeSettings`-Einstellungen in einer formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Die **type**-Eigenschaft unter `storeSettings` muss auf **GoogleCloudStorageReadSettings** festgelegt werden. | Ja                                                         |
| ***Suchen Sie die zu kopierenden Dateien:*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Bucket oder Ordner/Dateipfad. Wenn Sie alle Dateien aus einem Bucket oder Ordner kopieren möchten, geben Sie zusätzlich für `wildcardFileName` den Wert `*` an. |  |
| OPTION 2: GCS-Präfix<br>– prefix | Präfix für den GCS-Schlüsselnamen unter dem angegebenen Bucket, konfiguriert im Dataset zum Filtern von GCS-Dateien. Es werden die GCS-Schlüssel ausgewählt, deren Namen mit `bucket_in_dataset/this_prefix` beginnen. Es wird der dienstseitige Filter von GCS verwendet, dessen Leistung im Vergleich zu Platzhalterfiltern besser ist. | Nein |
| OPTION 3: Platzhalter<br>– wildcardFolderPath | Der Ordnerpfad mit Platzhalterzeichen unter dem angegebenen Bucket, der in einem Dataset für das Filtern von Quellordnern konfiguriert ist. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 3: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen unter dem angegebenen Bucket und Ordnerpfad (oder Platzhalterordnerpfad) für das Filtern von Quelldateien. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 3: eine Liste von Dateien<br>– fileListPath | Gibt an, dass eine bestimmte Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Dies ist der relative Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, geben Sie keinen Dateinamen im Dataset an. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen:*** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn **recursive** auf **TRUE** festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. <br>Zulässige Werte sind **true** (Standard) und **false**.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                                          |
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections | Die Anzahl gleichzeitiger Verbindungen mit dem Speicher. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                                          |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "GoogleCloudStorageReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das sich ergebende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| bucket | Schlüssel | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit Fettformatierung werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In diesem Abschnitt wird das resultierende Verhalten beschrieben, wenn ein Dateilistenpfad in der Quelle einer Kopieraktivität verwendet wird.

Angenommen, Sie haben die folgende Quellordnerstruktur und möchten die Dateien kopieren, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | Konfiguration der Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Bucket: `bucket`<br>– Ordnerpfad: `FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `bucket/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Diese enthält den relativen Pfad zu dem im Dataset konfigurierten Pfad. |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Delete-Aktivität](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

Wenn Sie einen Amazon S3-Connector zum Kopieren von Daten aus Google Cloud Storage verwenden, wird er für die Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, das oben erwähnte neue Modell zu verwenden. Die Data Factory-Benutzeroberfläche für die Erstellung ist zum Generieren des neuen Modells gewechselt.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die von der Kopieraktivität in Azure Data Factory als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
