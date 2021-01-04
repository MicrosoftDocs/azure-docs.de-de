---
title: Verschieben von Daten von einem FTP-Server mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie Daten mithilfe von Azure Data Factory von einem FTP-Server verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/02/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: eeeb122d240d8c3eae4ebe1650f67cf0e4b9dac6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001642"
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Verschieben von Daten von einem FTP-Server mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-ftp-connector.md)
> * [Version 2 (aktuelle Version)](../connector-ftp.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [FTP-Connector in V2](../connector-ftp.md).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten von einem FTP-Server zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten von einem FTP-Server in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem FTP-Server in andere Datenspeicher und nicht das Verschieben aus anderen Datenspeichern auf einen FTP-Server. Es werden jeweils lokale als auch cloudbasierte FTP-Server unterstützt.

> [!NOTE]
> Bei der Kopieraktivität wird die Quelldatei nicht gelöscht, nachdem sie erfolgreich in das Ziel kopiert wurde. Wenn Sie die Quelldatei nach dem erfolgreichen Kopieren löschen müssen, erstellen Sie eine benutzerdefinierte Aktivität, um die Datei zu löschen, und verwenden Sie die Aktivität in der Pipeline.

## <a name="enable-connectivity"></a>Herstellen von Konnektivität
Wenn Sie Daten aus einem **lokalen** FTP-Server in einen Clouddatenspeicher (z.B. Azure Blob Storage) verschieben, installieren und verwenden Sie das Datenverwaltungsgateway. Das Datenverwaltungsgateway ist ein Client-Agent, der auf dem lokalen Computer installiert ist, und der ermöglicht, dass die Clouddienste eine Verbindung mit einer lokalen Ressource herstellen. Ausführliche Informationen finden Sie unter [Datenverwaltungsgateway](data-factory-data-management-gateway.md). Unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erhalten Sie eine Schritt-für-Schritt-Anleitung zum Einrichten und Verwenden des Gateways. Sie verwenden das Gateway zum Herstellen der Verbindung mit einem FTP-Server, auch wenn sich der Server in einer Azure-Infrastruktur als virtueller IaaS-Computer (IaaS-VM) befindet.

Das Gateway kann auf dem gleichen lokalen Computer oder IaaS-VM wie der FTP-Server installiert werden. Es wird jedoch empfohlen, das Gateway auf einem separaten Computer/einem separaten IaaS-VM zu installieren, um Ressourcenkonflikte zu vermeiden und die Leistung zu steigern. Wenn Sie das Gateway auf einem separaten Computer installieren, muss dieser Computer auf den FTP-Server zugreifen können.

## <a name="get-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools oder APIs aus einer FTP-Quelle verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Data Factory-Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Führen Sie unabhängig davon, ob Sie Tools oder APIs verwenden, die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools oder APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem FTP-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten vom FTP-Server in ein Azure-Blob](#json-example-copy-data-from-ftp-server-to-azure-blob) in diesem Artikel.

> [!NOTE]
> Weitere Informationen zu unterstützten Datei- und Komprimierungsformaten, die verwendet werden können, finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für FTP verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
In der folgenden Tabelle werden die JSON-Elemente beschrieben, die für den verknüpften FTP-Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich | Standard |
| --- | --- | --- | --- |
| type |Legen Sie diese Eigenschaft auf „FtpServer“ fest. |Ja |&nbsp; |
| host |Gibt den Namen oder die IP-Adresse des FTP-Servers an. |Ja |&nbsp; |
| authenticationType |Gibt den Authentifizierungstyp an. |Ja |Standard, Anonym |
| username |Gibt den Benutzer an, der Zugriff auf den FTP-Server hat. |Nein |&nbsp; |
| password |Gibt das Kennwort für den Benutzer (username) an. |Nein |&nbsp; |
| encryptedCredential |Gibt die verschlüsselten Anmeldeinformationen für den Zugriff auf den FTP-Server an. |Nein |&nbsp; |
| gatewayName |Gibt den Namen des Gateways im Datenverwaltungsgateway zum Herstellen einer Verbindung mit einem lokalen FTP-Server an. |Nein |&nbsp; |
| port |Gibt den Port, den der FTP-Server abhört, an. |Nein |21 |
| enableSsl |Gibt an, ob FTP über einen SSL/TLS-Kanal verwendet werden soll. |Nein |true |
| enableServerCertificateValidation |Gibt an, ob die Überprüfung des TLS/SSL-Serverzertifikats aktiviert werden soll, wenn Sie FTP über den SSL/TLS-Kanal verwenden. |Nein |true |

>[!NOTE]
>Der FTP-Connector unterstützt den Zugriff auf FTP-Server ohne Verschlüsselung oder mit expliziter SSL/TLS-Verschlüsselung. Er unterstützt keine implizite SSL/TLS-Verschlüsselung.

### <a name="use-anonymous-authentication"></a>Verwenden von anonymer Authentifizierung

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Verwenden von „username“ und „password“ im Nur-Text-Format für die Standardauthentifizierung

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Verwenden von „port“, „enableSsl“ und „enableServerCertificateValidation“

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Verwenden von encryptedCredential für die Authentifizierung und das Gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen ähnlich.

Der Abschnitt **typeProperties** ist bei jeder Art von Dataset unterschiedlich. Er enthält Informationen, die spezifisch für den Datasettyp sind. Der Abschnitt **typeProperties** für ein Dataset des Typs **FileShare** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Unterpfad zum Ordner. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn **fileName** für ein Ausgabedataset nicht angegeben ist, weist der Name der generierten Datei das folgende Format auf: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in **folderPath** statt alle Dateien an.<br/><br/>Zulässige Werte sind: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: `"fileFilter": "*.log"`<br/>Beispiel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** eignet sich für das Eingabedataset „FileShare“. Diese Eigenschaft wird für HDFS (Hadoop Distributed Datei System) nicht unterstützt. |Nein |
| partitionedBy |Wird verwendet, um einen dynamischen Wert von **folderPath** und **fileName** für Zeitreihendaten anzugeben. Sie können z.B. einen **folderPath** angeben, der für jede Stunde von Daten parametrisiert wird. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Komprimierungsstufen werden unterstützt: **Optimal** und **Schnellste**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |
| useBinaryTransfer |Gibt an, ob der binäre Übertragungsmodus verwendet werden soll. Für den Binärmodus (dies ist der Standardwert) lautet der Wert „true“, und für ASCII lautet er „false“. Diese Eigenschaft kann nur verwendet werden, wenn der zugehörige verknüpfte Dienst vom Typ „FtpServer“ ist. |Nein |

> [!NOTE]
> **fileName** und **fileFilter** können nicht gleichzeitig verwendet werden.

### <a name="use-the-partionedby-property"></a>Verwenden der partionedBy-Eigenschaft
Wie im vorherigen Abschnitt erwähnt, kann die **partitionedBy**-Eigenschaft verwendet werden, um für Zeitreihendaten einen dynamischen Wert für **folderPath** und **fileName** anzugeben.

Unter [Erstellen von Datasets](data-factory-create-datasets.md), [Planung und Ausführung](data-factory-scheduling-and-execution.md) und [Erstellen von Pipelines](data-factory-create-pipelines.md) finden Sie weitere Informationen zu Zeitreihen-Datasets, Planung und Slices.

#### <a name="sample-1"></a>Beispiel 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Im obigen Beispiel wird {Slice} durch den Wert der Data Factory-Systemvariablen „SliceStart“ ersetzt, die im Format (JJJJMMTTHH) angegeben ist. "SliceStart" bezieht sich auf die Startzeit des Slices. Der Ordnerpfad unterscheidet sich für jeden Slice. (Beispiel: „wikidatagateway/wikisampledataout/2014100103“ oder „wikidatagateway/wikisampledataout/2014100104“.)

#### <a name="sample-2"></a>Beispiel 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Im Beispiel oben werden Jahr, Monat, Tag und Uhrzeit von „SliceStart“ in eigene Variablen extrahiert, die von den Eigenschaften **folderPath** und **fileName** verwendet werden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

Die Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften des Typs je nach Art der Quellen und Senken.

Wenn die Quelle der Kopieraktivität vom Typ **FileSystemSource** ist, steht im Abschnitt **typeProperties** die folgende Eigenschaft zur Verfügung:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| recursive |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True/False (Standardwert) |Nein |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten vom FTP-Server in ein Azure-Blob
Dieses Beispiel zeigt, wie Sie Daten von einem FTP-Server nach Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Data Factory direkt in die unter [Unterstützte Datenspeicher und Formate](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können:

* Ein verknüpfter Dienst vom Typ [FtpServer](#linked-service-properties)
* Ein verknüpfter Dienst vom Typ [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) vom Typ [FileShare](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) vom Typ [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden stündlich Daten aus einem FTP-Server in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

### <a name="ftp-linked-service"></a>Verknüpfter FTP-Dienst

Dieses Beispiel verwendet Standardauthentifizierung mit dem Benutzernamen und Kennwort im Nur-Text-Format. Sie können auch eine der folgenden Methoden verwenden:

* Anonyme Authentifizierung
* Standardauthentifizierung mit verschlüsselten Anmeldeinformationen
* FTP über SSL/TLS (FTPS)

Informationen zu den verschiedenen Authentifizierungstypen finden Sie im Abschnitt [Verknüpfter FTP-Dienst](#linked-service-properties).

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
    }
}
```
### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
### <a name="ftp-input-dataset"></a>FTP-Eingabedataset

Dieses Dataset verweist auf den FTP-Ordner `mysharedfolder` und die Datei `test.csv`. Die Pipeline kopiert die Datei in das Ziel.

Durch Festlegen von **external** auf **true** wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Azure-Blob-Ausgabedataset

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Kopieraktivität in einer Pipeline mit einer Dateisystemquelle und einer Blobsenke

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

* Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

* Eine Schritt-für-Schritt-Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Kopieraktivität-Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
