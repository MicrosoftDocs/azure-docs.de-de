---
title: Kopieren von Daten aus einer HTTP-Quelle mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus einer Cloud- oder lokalen HTTP-Quelle mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: jingwang
ms.openlocfilehash: 0462dac12d41fff667212902152b420d1460186d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383635"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Kopieren von Daten von einem HTTP-Endpunkt mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [Aktuelle Version](connector-http.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten von einem HTTP-Endpunkt zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

Die Unterschiede zwischen diesem HTTP-Connector, dem [REST-Connector](connector-rest.md) und dem [Webtabellenconnector](connector-web-table.md) sind die folgenden:

- **REST-Connector:** Dieser unterstützt insbesondere das Kopieren von Daten aus RESTful-APIs. 
- **HTTP-Connector:** Dieser dient allgemein dazu, Daten von jedem HTTP-Endpunkt abzurufen, z. B. um Dateien herunterzuladen. Solange der REST-Connector noch nicht verfügbar ist, verwenden Sie möglicherweise den HTTP-Connector, um Daten aus RESTful-APIs zu kopieren. Dieser wird unterstützt, hat jedoch weniger Funktionen als der REST-Connector.
- **Webtabellenconnector:** Dieser extrahiert Tabelleninhalte aus einer HTML-Webseite.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der HTTP-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer HTTP-Quelle in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Sie können diesen HTTP-Connector für die folgenden Aufgaben verwenden:

- Abrufen von Daten von einem HTTP/HTTPS-Endpunkt mithilfe der HTTP-Methoden **GET** oder **POST**.
- Abrufen von Daten mithilfe eines der folgenden Authentifizierungstypen: **Anonym**, **Standard**, **Digest**, **Windows**, **Clientzertifikat**.
- Kopieren der HTTP-Antwort im jeweiligen Zustand oder Analysieren der HTTP-Antwort mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Um eine HTTP-Anforderung für den Datenabruf zu testen, bevor Sie den HTTP-Connector in Data Factory konfigurieren, informieren Sie sich über die API-Spezifikation für Header- und Textanforderungen. Sie können Tools wie Postman oder einen Webbrowser für die Überprüfung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

In den folgenden Abschnitten finden Sie Details zu Eigenschaften, mit denen Sie Data Factory-Entitäten definieren können, die spezifisch für den HTTP-Connector sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit HTTP verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **HttpServer** festgelegt werden. | Ja |
| url | Die Basis-URL zum Webserver. | Ja |
| enableServerCertificateValidation | Geben Sie an, ob die Überprüfung des TLS-/SSL-Zertifikats des Servers aktiviert werden soll, wenn eine Verbindung mit einem HTTP-Endpunkt hergestellt wird. Wenn der HTTPS-Server ein selbstsigniertes Zertifikat verwendet, legen Sie diese Eigenschaft auf **FALSE** fest. | Nein<br /> (der Standardwert ist **TRUE**) |
| authenticationType | Gibt den Authentifizierungstyp an. Zulässige Werte: **Anonymous**, **Basic**, **Digest**, **Windows** und **ClientCertificate**. <br><br> Weitere Eigenschaften und JSON-Beispiele für diese Authentifizierungstypen, finden Sie in den Abschnitten, die auf diese Tabelle folgen. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

### <a name="using-basic-digest-or-windows-authentication"></a>Verwenden der Authentifizierung des Typs „Basic“, „Digest“ oder „Windows“

Legen Sie die **authenticationType**-Eigenschaft auf **Basic**, **Digest** oder **Windows** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| userName | Der Benutzername, der für den Zugriff auf den HTTP-Endpunkt verwendet werden soll. | Ja |
| password | Das Kennwort für den Benutzer (der Wert **userName**). Markieren Sie dieses Feld als Typ **SecureString**, um es sicher in Data Factory zu speichern. Sie können auch [auf ein Geheimnis verweisen, das in Azure Key Vault](store-credentials-in-key-vault.md) gespeichert ist. | Ja |

**Beispiel**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
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

### <a name="using-clientcertificate-authentication"></a>Verwenden der ClientCertificate-Authentifizierung

Um ClientCertificate-Authentifizierung zu verwenden, legen Sie die Eigenschaft **authenticationType** auf **ClientCertificate** fest. Geben Sie zusätzlich zu den im vorherigen Abschnitt beschriebenen generischen Eigenschaften die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| embeddedCertData | Base64-codierte Zertifikatdaten. | Geben Sie **embeddedCertData** oder **certThumbprint** an. |
| certThumbprint | Der Fingerabdruck des Zertifikats, das im Zertifikatspeicher Ihres Computers für die selbstgehostete Integration Runtime installiert wurde. Ist nur anwendbar, wenn für die **connectVia**-Eigenschaft eine Integration Runtime vom Typ „selbstgehostet“ angegeben wird. | Geben Sie **embeddedCertData** oder **certThumbprint** an. |
| password | Das Kennwort, das dem Zertifikat zugeordnet ist. Markieren Sie dieses Feld als Typ **SecureString**, um es sicher in Data Factory zu speichern. Sie können auch [auf ein Geheimnis verweisen, das in Azure Key Vault](store-credentials-in-key-vault.md) gespeichert ist. | Nein |

Wenn Sie **certThumbprint** für die Authentifizierung verwenden und das Zertifikat im persönlichen Speicher des lokalen Computers installiert wird, erteilen Sie der selbstgehosteten Integration Runtime Leseberechtigungen:

1. Öffnen Sie die Microsoft Management Console (MMC). Fügen Sie das für **Lokaler Computer** vorgesehene Snap-In **Zertifikate** hinzu.
2. Erweitern Sie **Zertifikate** > **Persönlich**, und wählen Sie dann **Zertifikate** aus.
3. Klicken Sie im persönlichen Speicher mit der rechten Maustaste auf das Zertifikat, und wählen Sie dann **Alle Aufgaben** > **Private Schlüssel verwalten** aus.
3. Fügen Sie auf der Registerkarte **Sicherheit** das Benutzerkonto hinzu, unter dem der Hostdienst der Integration Runtime (DIAHostService) mit Lesezugriff auf das Zertifikat ausgeführt wird.

**Beispiel 1: Verwenden von „certThumbprint“**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel 2: Verwenden von „embeddedCertData“**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für HTTP unter den `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft    | BESCHREIBUNG                                                  | Erforderlich |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | Die „type“-Eigenschaft unter `location` im Dataset muss auf **HttpServerLocation** festgelegt werden. | Ja      |
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Der HTTP-Connector kopiert Daten aus der kombinierten URL: `[URL specified in linked service][relative URL specified in dataset]`.   | Nein       |

> [!NOTE]
> Die unterstützte Nutzlastgröße für HTTP-Anforderungen beträgt etwa 500 KB. Wenn die Nutzlast, die Sie an Ihre Webendpunkt übergeben möchten, größer als 500 KB ist, sollten Sie eine Aufteilung der Nutzlast in kleinere Blöcke erwägen.

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
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

Dieser Abschnitt enthält eine Liste der Eigenschaften, die die HTTP-Quelle unterstützt.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP als Quelle

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für HTTP unter den `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **HttpReadSettings** festgelegt werden. | Ja      |
| requestMethod            | Die HTTP-Methode. <br>Zulässige Werte sind **Get** (Standardwert) und **Post**. | Nein       |
| addtionalHeaders         | Zusätzliche HTTP-Anforderungsheader                             | Nein       |
| requestBody              | Der Text der HTTP-Anforderung.                               | Nein       |
| httpRequestTimeout           | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Der Standardwert ist **00:01:40**. | Nein       |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein       |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
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
                    "type": "HttpReadSettings",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das in den obigen Abschnitten erwähnte neue Modell zu verwenden, da das neue Modell nun von der ADF-Benutzeroberfläche für die Erstellung generiert wird.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **HttpFile** festgelegt werden. | Ja |
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Wenn die Eigenschaft nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. | Nein |
| requestMethod | Die HTTP-Methode. Zulässige Werte sind **Get** (Standardwert) und **Post**. | Nein |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein |
| requestBody | Der Text der HTTP-Anforderung. | Nein |
| format | Wenn Sie Daten vom HTTP-Endpunkt im vorliegenden Zustand abrufen möchten, ohne diese analysieren und in einen dateibasierten Speicher kopieren zu müssen, überspringen Sie den Abschnitt **format** in den Definitionen des Eingabe- und Ausgabedatasets.<br/><br/>Wenn der HTTP-Antwortinhalt während des Kopierens analysiert werden soll, werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Legen Sie die **type**-Eigenschaft unter **format** auf einen dieser Werte fest. Weitere Informationen finden Sie unter [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/><br/>Unterstützte Typen: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**.<br/>Folgende Ebenen werden unterstützt:  **Optimal** und **Fastest**. |Nein |

> [!NOTE]
> Die unterstützte Nutzlastgröße für HTTP-Anforderungen beträgt etwa 500 KB. Wenn die Nutzlast, die Sie an Ihre Webendpunkt übergeben möchten, größer als 500 KB ist, sollten Sie eine Aufteilung der Nutzlast in kleinere Blöcke erwägen.

**Beispiel 1: Verwenden der Get-Methode (Standard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Beispiel 2: Verwenden der POST-Methode**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Legacy-Kopieraktivität: Quellenmodell

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **HttpSource** festgelegt werden. | Ja |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Bei diesem Wert handelt es sich um das Timeout zum Empfangen einer Antwort, nicht um das Timeout zum Lesen von Antwortdaten. Der Standardwert ist **00:01:40**.  | Nein |

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
