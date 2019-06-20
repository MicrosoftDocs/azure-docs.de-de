---
title: Verschieben von Daten vom SFTP-Server mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Informationen zum Verschieben von Daten von einem lokalen oder in der Cloud gehosteten SFTP-Server mithilfe von Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fe253feca6a22ee0177082e178f897c5b634bb3a
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526723"
---
# <a name="move-data-from-an-sftp-server-using-azure-data-factory"></a>Verschieben von Daten von einem SFTP-Server mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-sftp-connector.md)
> * [Version 2 (aktuelle Version)](../connector-sftp.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [SFTP-Connector in V2](../connector-sftp.md).

Dieser Artikel beschreibt, wie die Kopieraktivität in Azure Data Factory verwendet wird, um Daten von einem lokalen bzw. einem in der Cloud gehosteten SFTP-Server zu einem unterstützten Senkendatenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität und der als Quellen/Senken unterstützten Datenspeichern darstellt.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem SFTP-Server in andere Datenspeicher und nicht das Verschieben aus anderen Datenspeichern auf einen SFTP-Server. Es werden sowohl lokale als auch in der Cloud gehostete SFTP-Server unterstützt.

> [!NOTE]
> Bei der Kopieraktivität wird die Quelldatei nicht gelöscht, nachdem sie erfolgreich in das Ziel kopiert wurde. Wenn Sie die Quelldatei nach dem erfolgreichen Kopieren löschen müssen, erstellen Sie eine benutzerdefinierte Aktivität, um die Datei zu löschen, und verwenden Sie die Aktivität in der Pipeline.

## <a name="supported-scenarios-and-authentication-types"></a>Unterstützte Szenarien und Authentifizierungsarten
Sie können diesen SFTP-Connector zum Kopieren von Daten **sowohl von in der Cloud gehosteten SFTP-Servern als auch lokalen SFTP-Servern** verwenden. Die Authentifizierungsarten **Standard** und **SshPublicKey** werden zum Herstellen von Verbindungen mit dem SFTP-Server unterstützt.

Beim Kopieren von Daten von einem lokalen SFTP-Server müssen Sie in der lokalen Umgebung bzw. auf dem virtuellen Azure-Computer ein Datenverwaltungsgateway installieren. Weitere Informationen zum Gateway finden Sie unter [Gateway zur Datenverwaltung](data-factory-data-management-gateway.md). Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erhalten Sie eine Schritt-für-Schritt-Anleitung zum Einrichten und Verwenden des Gateways.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einer SFTP-Quelle verschiebt.

- Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Eine Schritt-für-Schritt-Anleitung finden Sie im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Azure-Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlagen**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können. JSON-Beispiele zum Kopieren von Daten vom SFTP-Server in Azure Blob Storage finden Sie unter [JSON-Beispiel: Kopieren von Daten von einem SFTP-Server in ein Azure-Blob](#json-example-copy-data-from-sftp-server-to-azure-blob) in diesem Artikel.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den verknüpften FTP-Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type | Die Typeigenschaft muss auf `Sftp` festgelegt sein. |Ja |
| host | Name oder IP-Adresse des SFTP-Servers. |Ja |
| port |Port, an dem der SFTP-Server lauscht. Der Standardwert lautet: 21 |Nein |
| authenticationType |Angeben des Authentifizierungstyps. Zulässige Werte: **Basic**, **SshPublicKey**. <br><br> Weitere Eigenschaften bzw. JSON-Beispiele finden Sie unter [Verwenden von Standardauthentifizierung](#using-basic-authentication) und [Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel](#using-ssh-public-key-authentication). |Ja |
| skipHostKeyValidation | Angabe, ob die Überprüfung des Hostschlüssels übersprungen werden soll. | Nein. Standardwert: FALSCH |
| hostKeyFingerprint | Angabe des Fingerabdrucks des Hostschlüssels. | Ja, wenn `skipHostKeyValidation` auf FALSCH festgelegt ist.  |
| gatewayName |Name des Datenverwaltungsgateways für die Verbindung mit einen lokalen SFTP-Server. | Ja beim Kopieren von Daten von einem lokalen SFTP-Server. |
| encryptedCredential | Verschlüsselte Anmeldeinformation für den Zugriff auf den SFTP-Server. Beim Festlegen von Standardauthentifizierung (Benutzername + Kennwort) oder SshPublicKey-Authentifizierung (Benutzername + Pfad oder Inhalt des privaten Schlüssels) im Kopier-Assistenten oder im ClickOnce-Popupdialogfeld automatisch generiert. | Nein. Betrifft nur das Kopieren von Daten von einem lokalen SFTP-Server. |

### <a name="using-basic-authentication"></a>Verwenden der Standardauthentifizierung

Legen Sie zum Verwenden der Standardauthentifizierung `authenticationType` auf `Basic` fest, und geben Sie über die im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors hinaus die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username | Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| password | Kennwort für den Benutzer (username) | Ja |

#### <a name="example-basic-authentication"></a>Beispiel: Standardauthentifizierung
```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Beispiel: Standardauthentifizierung mit verschlüsselten Anmeldeinformationen

```JSON
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
      }
}
```

### <a name="using-ssh-public-key-authentication"></a>Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel

Legen Sie zum Verwenden der Authentifizierung mit öffentlichem SSH-Schlüssel `authenticationType` auf `SshPublicKey` fest, und geben Sie über die im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors hinaus die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username |Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| privateKeyPath | Geben Sie den absoluten Pfad der privaten Schlüsseldatei ein, auf die das Gateway zugreifen kann. | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an. <br><br> Betrifft nur das Kopieren von Daten von einem lokalen SFTP-Server. |
| privateKeyContent | Eine serialisierte Zeichenfolge mit dem Inhalt des privaten Schlüssels. Der Kopier-Assistent kann die private Schlüsseldatei lesen und den privaten Schlüsselinhalt automatisch extrahieren. Wenn Sie andere Tools/SDKs verwenden, nutzen Sie stattdessen die privateKeyPath-Eigenschaft. | Geben Sie entweder den `privateKeyPath` oder den `privateKeyContent` an. |
| passPhrase | Geben Sie die Passphrase/das Kennwort zum Entschlüsseln des privaten Schlüssels ein, wenn die Schlüsseldatei mithilfe einer Passphrase geschützt ist. | Ja, wenn die private Schlüsseldatei mithilfe einer Passphrase geschützt ist. |

> [!NOTE]
> Der SFTP-Connector unterstützt nur RSA/DSA OpenSSH-Schlüssel. Stellen Sie sicher, dass der Inhalt Ihrer Schlüsseldatei mit „-----BEGIN [RSA/DSA] PRIVATE KEY-----“ beginnt. Wenn die Datei mit dem privaten Schlüssel eine Datei im PPK-Format ist, verwenden Sie das Tool Putty für die Konvertierung aus dem PPK- in das OpenSSH-Format .

#### <a name="example-sshpublickey-authentication-using-private-key-filepath"></a>Beispiel: SshPublicKey-Authentifizierung unter Verwendung des Dateipfads des privaten Schlüssels

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "mygateway"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Beispiel: SshPublicKey-Authentifizierung unter Verwendung des Inhalts des privaten Schlüssels

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen ähnlich.

Der Abschnitt **typeProperties** ist bei jeder Art von Dataset unterschiedlich. Er enthält Informationen, die spezifisch für den Datasettyp sind. Bei einem Dataset vom Typ **FileShare** enthält Abschnitt „typeProperties“ die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| folderPath |Unterpfad zum Ordner. Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen „\“. Beispiele finden Sie unter „Beispieldefinitionen für verknüpfte Dienste und Datasets“.<br/><br/>Sie können diese Eigenschaft mit **partitionBy** kombinieren, um Ordnerpfade auf der Grundlage von Datum und Uhrzeit für Start und Ende des Slices zu erhalten. |Ja |
| fileName |Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: <br/><br/>`Data.<Guid>.txt` (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). |Nein |
| fileFilter |Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an.<br/><br/>Zulässige Werte: `*` (mehrere Zeichen) und `?` (einzelnes Zeichen).<br/><br/>Beispiel 1: `"fileFilter": "*.log"`<br/>Beispiel 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter eignet sich für das Eingabedataset FileShare. Diese Eigenschaft wird mit HDFS nicht unterstützt. |Nein |
| partitionedBy |Mit „partitionedBy“ kann für Zeitreihendaten ein dynamischer Wert für „folderPath“ und „filename“ angegeben werden. Beispiel: Parametrisierung von „folderPath“ für Daten nach Stunde. |Nein |
| format | Die folgenden Formattypen werden unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |
| useBinaryTransfer |Gibt an, ob der binären Übertragungsmodus verwendet werden soll. Bei TRUE wird der Binärmodus und bei FALSE der ASCII-Modus verwendet. Standardwert: True. Diese Eigenschaft kann nur verwendet werden, wenn der zugehörige verknüpfte Dienst vom Typ „FtpServer“ ist. |Nein |

> [!NOTE]
> "filename" und "fileFilter" können nicht gleichzeitig verwendet werden.

### <a name="using-partionedby-property"></a>Verwenden der partionedBy-Eigenschaft
Wie im vorherigen Abschnitt erwähnt, kann „partitionedBy“ verwendet werden, um für Zeitreihendaten einen dynamischen Wert für „folderPath“ und „filename“ anzugeben. Sie können dazu die Data Factory-Makros und Systemvariablen "SliceStart" und "SliceEnd" verwenden, die den logischen Zeitraum für einen bestimmten Datenslice angeben.

In den Artikeln [Erstellen von Datasets](data-factory-create-datasets.md), [Planung und Ausführung](data-factory-scheduling-and-execution.md) und [Erstellen von Pipelines](data-factory-create-pipelines.md) finden Sie weitere Informationen zu Zeitreihen-Datasets, Planung und Slices.

#### <a name="sample-1"></a>Beispiel 1:

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
Im obigen Beispiel wird „{Slice}“ durch den Wert der Data Factory-Systemvariablen „SliceStart“ ersetzt, die im Format „JJJJMMTTHH“ angegeben ist. "SliceStart" bezieht sich auf die Startzeit des Slices. "folderPath" unterscheidet sich für jeden Slice. Beispiel: „wikidatagateway/wikisampledataout/2014100103“ oder „wikidatagateway/wikisampledataout/2014100104“.

#### <a name="sample-2"></a>Beispiel 2:

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
Im Beispiel oben werden Jahr, Monat, Tag und Uhrzeit von SliceStart in separate Variablen extrahiert, die von den Eigenschaften „folderPath“ und „fileName“ verwendet werden.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt „typeProperties“ der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften des Typs je nach Art der Quellen und Senken.

[!INCLUDE [data-factory-file-system-source](../../../includes/data-factory-file-system-source.md)]

## <a name="supported-file-and-compression-formats"></a>Unterstützte Datei- und Komprimierungsformate
Einzelheiten finden Sie im Artikel [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-example-copy-data-from-sftp-server-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten von einem SFTP-Server in ein Azure-Blob
Das folgende Beispiel stellt JSON-Beispieldefinitionen bereit, die Sie zum Erstellen einer Pipeline mit dem [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder mit [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Darin wird veranschaulicht, wie Sie Daten aus einer SFTP-Quelle nach Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

> [!IMPORTANT]
> Dieses Beispiel enthält JSON-Codeausschnitte. Eine schrittweise Anleitung zum Erstellen der Data Factory ist nicht enthalten. Einen Artikel mit schrittweisen Anleitungen finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Ein verknüpfter Dienst vom Typ [sftp](#linked-service-properties).
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [FileShare](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden stündlich Daten von einem SFTP-Server in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Verknüpfter SFTP-Dienst**

Dieses Beispiel verwendet die Standardauthentifizierung mit dem Benutzernamen und Kennwort im Nur-Text-Format. Sie können auch eine der folgenden Methoden verwenden:

* Standardauthentifizierung mit verschlüsselten Anmeldeinformationen
* Authentifizierung mit öffentlichem SSH-Schlüssel

Informationen zu den verschiedenen Authentifizierungstypen finden Sie im Abschnitt [Eigenschaften des verknüpften FTP-Diensts](#linked-service-properties).

```JSON

{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver",
            "port": 22,
            "authenticationType": "Basic",
            "username": "myuser",
            "password": "mypassword",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "mygateway"
        }
    }
}
```
**Mit Azure Storage verknüpfter Dienst**

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
**SFTP-Eingabedataset**

Dieses Dataset verweist auf den SFTP-Ordner `mysharedfolder` und die Datei `test.csv`. Die Pipeline kopiert die Datei in das Ziel.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "SFTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "SftpLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00Z",
        "end": "2017-02-20T19:00:00Z"
    }
}
```

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

* [Kopieraktivität-Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) für schrittweise Anleitungen zum Erstellen einer Pipeline mit einer Kopieraktivität.
