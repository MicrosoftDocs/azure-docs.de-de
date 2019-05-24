---
title: Kopieren von Daten aus Amazon Simple Storage Service (S3) mit Azure Data Factory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten aus Amazon Simple Storage Service (S3) in unterstützte Senkendatenspeicher kopieren.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: a04dc76a110b1d126d5d826761e2e0c09c6aac47
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520239"
---
# <a name="copy-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Kopieren von Daten aus Amazon Simple Storage Service mit Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
>
> * [Version 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [Aktuelle Version](connector-amazon-simple-storage-service.md)

In diesem Artikel wird beschrieben, wie Daten aus Amazon Simple Storage Service (Amazon S3) kopiert werden. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Amazon S3-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Der Amazon S3-Connector unterstützt insbesondere das Kopieren von Dateien im jeweiligen Zustand oder Analysieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md). Er verwendet [AWS Signatur Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) zum Authentifizieren von Anforderungen in S3.

>[!TIP]
>Mit diesem Amazon S3-Connector können Sie Daten von **allen S3-kompatiblen Speicheranbietern** kopieren, z.B. aus [Google Cloud Storage](connector-google-cloud-storage.md). Geben Sie die entsprechende Dienst-URL in der Konfiguration des verknüpften Diensts an.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Um Daten von Amazon S3 zu kopieren, müssen Sie sicherstellen, dass Ihnen die folgenden Berechtigungen erteilt wurden:

- **Für die Ausführung der Kopieraktivität:** `s3:GetObject` und `s3:GetObjectVersion` für Amazon S3-Objektvorgänge.
- **Für das Erstellen mit der Data Factory-Benutzeroberfläche:** Berechtigungen `s3:ListAllMyBuckets` und `s3:ListBucket`/`s3:GetBucketLocation` für Amazon S3-Bucketvorgänge sind zusätzlich erforderlich für Vorgänge wie das Testen der Verbindung und das Durchsuchen/Navigieren in Dateipfaden. Wenn Sie diese Berechtigung nicht erteilen möchten, überspringen Sie die Testverbindung auf der Seite zur Erstellung verlinkter Dienste, und geben Sie den Pfad direkt in den Dataseteinstellungen an.

Ausführliche Informationen zur vollständigen Liste der Amazon S3-Berechtigungen finden Sie unter [Specifying Permissions in a Policy](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) (Angeben von Berechtigungen in einer Richtlinie).

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren spezifischer Data Factory-Entitäten für Amazon S3 verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Amazon S3 verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **AmazonS3** festgelegt werden. | Ja |
| accessKeyId | ID des geheimen Zugriffsschlüssels. |Ja |
| secretAccessKey | Der geheime Zugriffsschlüssel selbst. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| serviceUrl | Geben Sie den benutzerdefinierten S3-Endpunkt an, wenn Sie Daten von einem anderen S3-kompatiblen Speicheranbieter als dem offiziellen Amazon S3-Dienst kopieren. Um beispielsweise Daten aus Google Cloud Storage zu kopieren, geben Sie `https://storage.googleapis.com` an. | Nein  |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

>[!TIP]
>Geben Sie die URL des benutzerdefinierten S3-Diensts an, wenn Sie Daten aus einem anderen S3-kompatiblen Speicher als dem offiziellen Amazon S3-Dienst kopieren.

>[!NOTE]
>Dieser Connector erfordert Zugriffsschlüssel für ein IAM-Konto zum Kopieren von Daten aus Amazon S3. [Temporäre Sicherheitsanmeldeinformationen](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) werden nicht unterstützt.
>

Beispiel: 

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

- Informationen zum **Parquet-Format und zum Textformat mit Trennzeichen** finden Sie im Abschnitt [Dataset für Parquet-Format und Textformat mit Trennzeichen](#parquet-and-delimited-text-format-dataset).
- Informationen zu anderen Formaten wie **ORC/Avro/JSON/Binär** finden Sie im Abschnitt [Dataset in anderen Formaten](#other-format-dataset).

### <a name="parquet-and-delimited-text-format-dataset"></a>Dataset für Parquet-Format und Textformat mit Trennzeichen

Informationen zum Kopieren von Daten aus Amazon S3 im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Datasets und unterstützten Einstellungen. Folgende Eigenschaften werden für Amazon S3 unter `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die „type“-Eigenschaft unter `location` im Dataset muss auf **AmazonS3Location** festgelegt werden. | Ja      |
| bucketName | Der Name des S3-Buckets.                                          | Ja      |
| folderPath | Der Pfad zum Ordner unter dem angegebenen Bucket. Wenn Sie Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein        |
| fileName   | Der Name der Datei unter dem angegebenen Bucket + „folderPath“. Wenn Sie Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechenden Aktivitätsquelleneinstellungen an. | Nein        |

> [!NOTE]
> Das Dataset vom Typ **AmazonS3Object** mit dem im nächsten Abschnitt beschriebenen Parquet-Format/Textformat wird aus Gründen der Abwärtskompatibilität weiterhin unverändert für Kopieren-/Suchen-/GetMetadata-Aktivitäten unterstützt, funktioniert aber nicht mit Mapping Data Flow. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

### <a name="other-format-dataset"></a>Dataset in anderen Formaten

Zum Kopieren von Daten aus Amazon S3 in den Formaten **ORC/Avro/JSON/Binärformat** werden folgende Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **AmazonS3Object** |Ja |
| bucketName | Der Name des S3-Buckets. Der Platzhalterfilter wird nicht unterstützt. |Ja für die Copy/Lookup-Aktivität, Nein für die GetMetadata-Aktivität |
| key | Der **Name oder Platzhalterfilter** des S3-Objektschlüssels unter dem angegebenen Bucket. Ist nur anwendbar, wenn die prefix-Eigenschaft nicht angegeben ist. <br/><br/>Der Platzhalterfilter wird sowohl für Ordner- als auch für Dateinamen unterstützt. Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- Beispiel 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordner-/Dateiname einen Platzhalter oder dieses Escapezeichen enthält. |Nein  |
| prefix | Präfix für den S3-Objektschlüssel. Objekte, deren Schlüssel mit diesem Präfix beginnen, werden ausgewählt. Ist nur anwendbar, wenn die key-Eigenschaft nicht angegeben ist. |Nein  |
| version | Die Version des S3-Objekts, wenn die S3-Versionsverwaltung aktiviert ist. Wenn dies nicht angegeben ist, wird die neueste Version abgerufen. |Nein  |
| modifiedDatetimeStart | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein  |
| modifiedDatetimeEnd | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein  |
| format | Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**.<br/>Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. |Nein  |

>[!TIP]
>Um alle Dateien in einem Ordner zu kopieren, geben Sie **bucketName** für den Bucket und **prefix** für den Ordner an.<br>Um eine einzelne Datei mit einem angegebenen Namen zu kopieren, geben Sie **bucketName** für den Bucket und **key** für den Ordner mit dem Dateinamen an.<br>Um eine Teilmenge der Dateien in einem Ordner zu kopieren, geben Sie **bucketName** für den Bucket und **key** für den Ordner mit dem Platzhalterfilter an.

**Beispiel: Verwenden eines Präfixes**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Beispiel: Verwenden eines Schlüssels und einer Version (optional)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Amazon S3-Quelle unterstützt werden.

### <a name="amazon-s3-as-source"></a>Amazon S3 als Quelle

- Informationen zum Kopieren aus dem **Parquet-Format und dem Textformat mit Trennzeichen** finden Sie im Abschnitt [Quelle im Parquet-Format und im Textformat mit Trennzeichen](#parquet-and-delimited-text-format-source).
- Informationen zum Kopieren aus anderen Formaten wie **ORC/Avro/JSON/Binär** finden Sie im Abschnitt [Quelle in anderen Formaten](#other-format-source).

#### <a name="parquet-and-delimited-text-format-source"></a>Quelle im Parquet-Format und im Textformat mit Trennzeichen

Informationen zum Kopieren von Daten aus Amazon S3 im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Quellen für Kopieraktivitäten und unterstützten Einstellungen. Folgende Eigenschaften werden für Amazon S3 unter `storeSettings`-Einstellungen in formatbasierten Kopierquellen unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **AmazonS3ReadSetting** festgelegt werden. | Ja                                                         |
| recursive                | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. Zulässige Werte sind **true** (Standard) und **false**. | Nein                                                           |
| prefix                   | Präfix für den S3-Objektschlüssel unter dem angegebenen Bucket, konfiguriert im Dataset zum Filtern von Quellobjekten. Objekte, deren Schlüssel mit diesem Präfix beginnen, werden ausgewählt. <br>Nur gültig, wenn die Eigenschaften `wildcardFolderPath` und `wildcardFileName` nicht angegeben sind. | Nein                                                           |
| wildcardFolderPath       | Der Ordnerpfad mit Platzhalterzeichen unter dem angegebenen Bucket, der im Dataset für das Filtern von Quellordnern konfiguriert ist. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                                           |
| wildcardFileName         | Der Dateiname mit Platzhalterzeichen unter dem angegebenen Bucket + „folderPath/wildcardFolderPath“ für das Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja, wenn `fileName` im Dataset und `prefix` nicht angegeben sind |
| modifiedDatetimeStart    | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist. | Nein                                                           |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                                           |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                                           |

> [!NOTE]
> Für das Parquet-Format/Textformat mit Trennzeichen wird der im nächsten Abschnitt erwähnte Typ **FileSystemSource**der Quelle der Kopieraktivität aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AmazonS3ReadSetting",
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

#### <a name="other-format-source"></a>Quelle in anderen Formaten

Zum Kopieren von Daten aus Amazon S3 im **ORC-/Avro-/JSON-/Binärformat** werden folgende Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **FileSystemSource** |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf TRUE festgelegt und die Senke ein dateibasierter Speicher ist, wird ein leerer Ordner/Unterordner nicht in die Senke kopiert bzw. nicht in ihr erstellt.<br/>Zulässige Werte sind **true** (Standard) oder **false**. | Nein  |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein  |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
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

| Bucket | key | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit Fettformatierung werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| Bucket | `Folder*/*` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| Bucket | `Folder*/*` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| Bucket | `Folder*/*.csv` | false | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| Bucket | `Folder*/*.csv` | true | Bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
