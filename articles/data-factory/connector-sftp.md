---
title: Kopieren von Daten vom SFTP-Server mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie mehr über den MySQL-Connector in Azure Data Factory, mit dem Sie Daten von einem SFTP-Server in einem als Senke unterstützten Datenspeicher kopieren können.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 1a62e9e8377705af1a70e356f554cfa549c58f20
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233468"
---
# <a name="copy-data-from-sftp-server-using-azure-data-factory"></a>Kopieren von Daten von einem SFTP-Server mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-sftp-connector.md)
> * [Aktuelle Version](connector-sftp.md)

In diesem Artikel wird beschrieben, wie Sie Daten vom SFTP-Server kopieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der SFTP-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Der SFTP-Connector unterstützt insbesondere Folgendes:

- Kopieren von Dateien mit der **Basic**- oder **SshPublicKey**-Authentifizierung
- Kopieren von Dateien im jeweiligen Zustand oder Analysieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md)

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für SFTP verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SFTP verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SFTP**. |Ja |
| host | Name oder IP-Adresse des SFTP-Servers. |Ja |
| port | Port, an dem der SFTP-Server lauscht.<br/>Zulässige Werte sind ganze Zahlen (Standardwert **22**). |Nein  |
| skipHostKeyValidation | Angabe, ob die Überprüfung des Hostschlüssels übersprungen werden soll.<br/>Zulässige Werte sind **true** oder **false** (Standard).  | Nein  |
| hostKeyFingerprint | Angabe des Fingerabdrucks des Hostschlüssels. | Ja, sofern die „skipHostKeyValidation“ auf „false“ festgelegt ist.  |
| authenticationType | Angeben des Authentifizierungstyps.<br/>Zulässige Werte sind: **Basic**, **SshPublicKey**. Weitere Eigenschaften bzw. JSON-Beispiele finden Sie unter [Verwenden von Standardauthentifizierung](#using-basic-authentication) und [Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel](#using-ssh-public-key-authentication). |Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure-Integrationslaufzeit oder selbstgehostete Integrationslaufzeit verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein  |

### <a name="using-basic-authentication"></a>Verwenden der Standardauthentifizierung

Legen Sie zum Verwenden der Standardauthentifizierung die Eigenschaft „authenticationType“ auf **Basic** fest, und geben Sie neben den im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| userName | Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| password | Kennwort für den Benutzer (userName). Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |

**Beispiel:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-ssh-public-key-authentication"></a>Verwenden von Authentifizierung mit öffentlichem SSH-Schlüssel

Legen Sie zum Verwenden der Authentifizierung mit öffentlichem SSH-Schlüssel die Eigenschaft „authenticationType“ auf **SshPublicKey** fest, und geben Sie neben den im letzten Abschnitt beschriebenen allgemeinen Eigenschaften des SFTP-Connectors die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| userName | Benutzer, der Zugriff auf den SFTP-Server hat. |Ja |
| privateKeyPath | Geben Sie den absoluten Pfad der privaten Schlüsseldatei ein, auf die die Integrationslaufzeit zugreifen kann. Ist nur anwendbar, wenn für „connectVia“ eine Integrationslaufzeit vom Typ „selbstgehostet“ angegeben wird. | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an.  |
| privateKeyContent | Inhalt des Base64-codierten privaten SSH-Schlüssels. Der private SSH-Schlüssel sollte das Format „OpenSSH“ aufweisen. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Geben Sie entweder `privateKeyPath` oder `privateKeyContent` an. |
| passPhrase | Geben Sie die Passphrase/das Kennwort zum Entschlüsseln des privaten Schlüssels ein, wenn die Schlüsseldatei mithilfe einer Passphrase geschützt ist. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, wenn die private Schlüsseldatei mithilfe einer Passphrase geschützt ist. |

> [!NOTE]
> Der SFTP-Connector unterstützt nur RSA/DSA OpenSSH-Schlüssel. Stellen Sie sicher, dass der Inhalt Ihrer Schlüsseldatei mit „-----BEGIN [RSA/DSA] PRIVATE KEY-----“ beginnt. Wenn die Datei mit dem privaten Schlüssel eine Datei im PPK-Format ist, verwenden Sie das Tool Putty für die Konvertierung aus dem PPK- in das OpenSSH-Format . 

**Beispiel 1: SshPublicKey-Authentifizierung unter Verwendung des Dateipfads des privaten Schlüssels**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: SshPublicKey-Authentifizierung unter Verwendung des Inhalts des privaten Schlüssels**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
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

Informationen zum Kopieren von Daten aus SFTP im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Datasets und unterstützten Einstellungen. Folgende Eigenschaften werden für SFTP unter den `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die „type“-Eigenschaft unter `location` im Dataset muss auf **SftpLocation** festgelegt werden. | Ja      |
| folderPath | Der Pfad zum Ordner. Wenn Sie Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein        |
| fileName   | Der Name der Datei unter dem angegebenen „folderPath“. Wenn Sie Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein        |

> [!NOTE]
> Das Dataset vom Typ **FileShare** mit dem im nächsten Abschnitt beschriebenen Parquet-Format/Textformat wird aus Gründen der Abwärtskompatibilität weiterhin unverändert für Kopieren-/Suchen-/GetMetadata-Aktivitäten unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
                "folderPath": "root/folder/subfolder"
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

Zum Kopieren von Daten aus SFTP in den Formaten **ORC/Avro/JSON/Binärformat** werden folgende Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **FileShare** |Ja |
| folderPath | Pfad zum Ordner. Platzhalterfilter werden unterstützt. Zulässige Platzhalter sind: `*` (entspricht Null oder mehr Zeichen) und `?` (entspricht Null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiele: Stammordner/Unterordner/. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Ja |
| fileName |  **Name oder Platzhalterfilter** für die Dateien unter dem angegebenen Wert für „folderPath“. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind folgende Platzhalter zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. |Nein  |
| modifiedDatetimeStart | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein  |
| modifiedDatetimeEnd | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein  |
| format | Wenn Sie **Dateien unverändert zwischen dateibasierten Speichern kopieren** möchten (binäre Kopie), können Sie den Formatabschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen.<br/><br/>Für das Analysieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type** -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Folgende Typen werden unterstützt: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**.<br/>Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. |Nein  |

>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur **folderPath** an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie **folderPath** mit dem Ordner und **fileName** mit dem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie **folderPath** mit dem Ordner und **fileName** mit dem Platzhalterfilter an.

>[!NOTE]
>Wenn Sie die Eigenschaft „fileFilter“ für den Dateifilter verwendet haben, wird sie weiterhin unverändert unterstützt. Es wird aber empfohlen, ab sofort die Filterfunktion zu verwenden, die „fileName“ neu hinzugefügt wurde.

**Beispiel:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
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

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SFTP-Quelle unterstützt werden.

### <a name="sftp-as-source"></a>SFTP als Quelle

- Informationen zum Kopieren aus dem **Parquet-Format und dem Textformat mit Trennzeichen** finden Sie im Abschnitt [Quelle im Parquet-Format und im Textformat mit Trennzeichen](#parquet-and-delimited-text-format-source).
- Informationen zum Kopieren aus anderen Formaten wie **ORC/Avro/JSON/Binär** finden Sie im Abschnitt [Quelle in anderen Formaten](#other-format-source).

#### <a name="parquet-and-delimited-text-format-source"></a>Quelle im Parquet-Format und im Textformat mit Trennzeichen

Informationen zum Kopieren von Daten aus SFTP im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Quellen für Kopieraktivitäten und unterstützten Einstellungen. Folgende Eigenschaften werden für SFTP unter den `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **SftpReadSetting** festgelegt werden. | Ja                                           |
| recursive                | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. Zulässige Werte sind **true** (Standard) und **false**. | Nein                                             |
| wildcardFolderPath       | Der Ordnerpfad mit Platzhalterzeichen, um Quellordner zu filtern. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                             |
| wildcardFileName         | Der Dateiname mit Platzhalterzeichen unter dem angegebenen „folderPath/wildcardFolderPath“ für das Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja, wenn `fileName` nicht im Dataset angegeben ist |
| modifiedDatetimeStart    | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist. | Nein                                             |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                             |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                             |

> [!NOTE]
> Für das Parquet-Format/Textformat mit Trennzeichen wird der im nächsten Abschnitt erwähnte Typ **FileSystemSource**der Quelle der Kopieraktivität aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSetting",
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

Für das Kopieren von Daten aus SFTP in den Formaten **ORC/Avro/JSON/Binärformat** werden folgende Eigenschaften im Abschnitt **source** der Copy-Aktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **FileSystemSource** |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf TRUE festgelegt und die Senke ein dateibasierter Speicher ist, wird ein leerer Ordner/Unterordner nicht in die Senke kopiert bzw. nicht in ihr erstellt.<br/>Zulässige Werte sind **true** (Standard) oder **false**. | Nein  |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein  |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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

Dieser Abschnitt beschreibt das resultierende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| folderPath | fileName | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leer, Standardwert verwenden) | false | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | (leer, Standardwert verwenden) | true | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | false | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | true | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
