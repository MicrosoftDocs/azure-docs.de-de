---
title: Verschieben von Daten aus einer HTTP-Quelle – Azure | Microsoft-Dokumentation
description: Informationen zum Verschieben von Daten aus einer lokalen oder in der Cloud gehosteten HTTP-Quelle mithilfe von Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f7e070788d2fc11addcafc30d9f232f194f44782
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017258"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Verschieben von Daten aus einer HTTP-Quelle mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-http-connector.md)
> * [Version 2 (aktuelle Version)](../connector-http.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Azure Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [HTTP-Connector in V2](../connector-http.md).


Dieser Artikel beschreibt, wie die Kopieraktivität in Azure Data Factory verwendet wird, um Daten von einem lokalen bzw. einem in der Cloud gehosteten HTTP-Endpunkt in einen unterstützten Senkendatenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Verschieben von Daten mit der Kopieraktivität](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet. Der Artikel listet auch die Datenspeicher auf, die die Kopieraktivität als Quellen und Senken unterstützt.

Data Factory unterstützt zurzeit nur das Verschieben von Daten aus einer HTTP-Quelle in andere Datenspeicher. Das Verschieben von Daten aus anderen Datenspeichern an ein HTTP-Ziel wird nicht unterstützt.

## <a name="supported-scenarios-and-authentication-types"></a>Unterstützte Szenarien und Authentifizierungsarten

Sie können mit diesem HTTP-Connector Daten *sowohl aus in der Cloud gehosteten als auch lokalen HTTP(S)-Endpunkten* abrufen. Verwenden Sie dazu die HTTP-Methoden **GET** oder **POST**. Die folgenden Authentifizierungstypen werden unterstützt: **Anonymous**, **Basic**, **Digest**, **Windows** und **ClientCertificate**. Beachten Sie den Unterschied zwischen diesem Connector und dem [Webtabellenconnector](data-factory-web-table-connector.md). Der Webtabellenconnector extrahiert Tabelleninhalt aus einer HTML-Webseite.

Beim Kopieren von Daten von einem lokalen HTTP-Endpunkt müssen Sie in der lokalen Umgebung bzw. auf dem virtuellen Azure-Computer ein Datenverwaltungsgateway installieren. Weitere Informationen zum Datenverwaltungsgateway sowie eine schrittweise Anleitung zum Einrichten des Gateways finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und der Cloud](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Erste Schritte

Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools oder APIs aus einer HTTP-Quelle verschiebt:

- Am einfachsten erstellen Sie eine Pipeline mit dem Assistenten zum Kopieren von Daten. Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten finden Sie im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).

- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: das **Azure-Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlagen** sowie die **.NET-API** und die **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie eine schrittweise Anleitung, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können. JSON-Beispiele zum Kopieren von Daten aus einer HTTP-Quelle in Azure Blob Storage finden Sie unter [JSON-Beispiele](#json-examples).

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

In der folgenden Tabelle werden die JSON-Elemente beschrieben, die für den verknüpften HTTP-Dienst spezifisch sind:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type | Die **type**-Eigenschaft muss auf **Http** festgelegt werden. | JA |
| url | Die Basis-URL zum Webserver. | JA |
| authenticationType | Gibt den Authentifizierungstyp an. Zulässige Werte: **Anonymous**, **Basic**, **Digest**, **Windows** und **ClientCertificate**. <br><br> Weitere Eigenschaften und JSON-Beispiele für diese Authentifizierungstypen finden Sie in den späteren Abschnitten in diesem Artikel. | JA |
| enableServerCertificateValidation | Gibt an, ob die SSL-Serverzertifikatüberprüfung aktiviert werden soll, wenn die Quelle ein HTTPS-Webserver ist. Wenn der HTTPS-Server ein selbstsigniertes Zertifikat verwendet, legen Sie diesen Parameter auf **FALSE** fest. | Nein <br /> (der Standardwert ist **TRUE**) |
| gatewayName | Der Name der Instanz des Datenverwaltungsgateways, die für die Verbindung mit einer lokalen HTTP-Quelle verwendet werden soll. | Ja, wenn Daten aus einer lokalen HTTP-Quelle kopiert werden. |
| encryptedCredential | Die verschlüsselten Anmeldeinformation für den Zugriff auf den HTTP-Endpunkt. Der Wert wird automatisch generiert, wenn Sie die Authentifizierungsinformationen im Kopier-Assistenten oder über das Dialogfeld **ClickOnce** konfigurieren. | Nein <br /> (Betrifft nur das Kopieren von Daten von einem lokalen HTTP-Server.) |

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine lokale HTTP-Connectordatenquelle finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Verwenden der Authentifizierung des Typs „Basic“, „Digest“ oder „Windows“

Legen Sie **authenticationType** auf **Basic**, **Digest** oder **Windows** fest. Legen Sie zusätzlich zu den in den vorangegangenen Abschnitten beschriebenen generischen Eigenschaften des HTTP-Connectors die folgenden Eigenschaften fest:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| username | Der Benutzername, der für den Zugriff auf den HTTP-Endpunkt verwendet werden soll. | JA |
| password | Das Kennwort für den Benutzer (**username**). | JA |

**Beispiel: Verwenden der Authentifizierung des Typs „Basic“, „Digest“ oder „Windows“**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Verwenden der ClientCertificate-Authentifizierung

Legen Sie zum Verwenden der Standardauthentifizierung **authenticationType** auf **ClientCertificate** fest. Legen Sie zusätzlich zu den in den vorangegangenen Abschnitten beschriebenen generischen Eigenschaften des HTTP-Connectors die folgenden Eigenschaften fest:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| embeddedCertData | Der Base64-codierte Inhalt der Binärdaten der PFX-Datei. | Geben Sie **embeddedCertData** oder **certThumbprint** an. |
| certThumbprint | Der Fingerabdruck des Zertifikats, das im Zertifikatspeicher des Gatewaycomputers installiert wurde. Betrifft nur das Kopieren von Daten aus einer lokalen HTTP-Quelle. | Geben Sie **embeddedCertData** oder **certThumbprint** an. |
| password | Das Kennwort, das dem Zertifikat zugeordnet ist. | Nein  |

Wenn Sie **certThumbprint** für die Authentifizierung verwenden und das Zertifikat im persönlichen Speicher des lokalen Computers installiert wird, gewähren Sie dem Gatewaydienst Leseberechtigungen:

1. Öffnen Sie die Microsoft Management Console (MMC). Fügen Sie das für **Lokaler Computer** vorgesehene Snap-In **Zertifikate** hinzu.
2. Erweitern Sie **Zertifikate** > **Persönlich**, und wählen Sie dann **Zertifikate** aus.
3. Klicken Sie im persönlichen Speicher mit der rechten Maustaste auf das Zertifikat, und wählen Sie dann **Alle Aufgaben** >**Private Schlüssel verwalten** aus.
3. Fügen Sie auf der Registerkarte **Sicherheit** das Benutzerkonto hinzu, unter dem der Datenverwaltungsgateway-Hostdienst mit Lesezugriff auf das Zertifikat ausgeführt wird.  

**Beispiel: Verwenden eines Clientzertifikats**

Dieser verknüpfte Dienst verbindet Ihre Data Factory mit einem lokalen HTTP-Server. Er verwendet ein Clientzertifikat, das auf dem Computer mit dem Datenverwaltungsgateway installiert ist.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Beispiel: Verwenden eines Clientzertifikats in einer Datei**

Dieser verknüpfte Dienst verbindet Ihre Data Factory mit einem lokalen HTTP-Server. Er verwendet eine Clientzertifikatdatei auf dem Computer, auf dem das Datenverwaltungsgateway installiert ist.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Die Abschnitte einer JSON-Datasetdatei (z.B. „structure“, „availability“ und „policy“) sind für alle Datasettypen (Azure SQL-Datenbank, Azure Blob Storage, Azure Table Storage) ähnlich.

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md).

Der Abschnitt **typeProperties** ist bei jeder Art von Dataset unterschiedlich. Der Abschnitt **typeProperties** bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset des Typs **Http** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Der **type** des Datasets muss auf **Http** festgelegt werden. | JA |
| relativeUrl | Eine relative URL zu der Ressource, die die Daten enthält. Wenn der Pfad nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. <br><br> Zum Generieren von dynamischen URLs können Sie [Data Factory-Funktionen und -Systemvariablen](data-factory-functions-variables.md) verwenden. Beispiel: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** . | Nein  |
| requestMethod | Die HTTP-Methode. Zulässige Werte sind **GET** und **POST**. | Nein  <br />(Der Standardwert ist **GET**.) |
| additionalHeaders | Zusätzliche HTTP-Anforderungsheader | Nein  |
| requestBody | Der Text der HTTP-Anforderung. | Nein  |
| format | Wenn Sie *die Daten ohne Änderung von einem HTTP-Endpunkt abrufen* möchten, ohne sie zu analysieren, überspringen Sie die Einstellung **format**. <br><br> Wenn der HTTP-Antwortinhalt während des Kopierens analysiert werden soll, werden die folgenden Formattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format). |Nein  |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Unterstützte Typen: **GZip**, **Deflate**, **BZip2** und **ZipDeflate**. Folgende Ebenen werden unterstützt: **Optimal** und **Fastest**. Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein  |

**Beispiel: Verwenden der GET-Methode (Standard)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Beispiel: Verwenden der POST-Methode**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Erstellen von Pipelines](data-factory-create-pipelines.md). 

Eigenschaften, die im Abschnitt **typeProperties** der Aktivität verfügbar sind, können dagegen je nach Aktivitätstyp variieren. Für eine Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn bei der Kopieraktivität die Quelle den Typ **HttpSource** aufweist, werden derzeit die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| -------- | ----------- | -------- |
| httpRequestTimeout | Das Timeout (der Wert **TimeSpan**) für die HTTP-Anforderung, um eine Antwort zu empfangen. Dabei handelt es sich um das Timeout zum Empfangen einer Antwort, nicht das Timeout zum Lesen von Antwortdaten. | Nein <br />(Standardwert: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Unterstützte Datei- und Komprimierungsformate

Weitere Informationen finden Sie unter [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>JSON-Beispiele

Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit dem [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Die Beispiele veranschaulichen, wie Sie Daten aus einer HTTP-Quelle in Azure Blob Storage kopieren. Daten können jedoch *direkt* aus einer beliebigen Quelle in eine der Senken kopiert werden, die durch die Verwendung der Kopieraktivität in Azure Data Factory [unterstützt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) werden.

**Beispiel: Kopieren von Daten aus einer HTTP-Quelle in Azure Blob Storage**

Die Data Factory-Lösung für dieses Beispiel enthält die folgenden Data Factory-Entitäten:

*   Einen verknüpften Dienst des Typs [HTTP](#linked-service-properties)
*   Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
*   Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [Http](#dataset-properties)
*   Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
*   Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [HttpSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet.

Im Beispiel werden stündlich Daten aus einer HTTP-Quelle in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

### <a name="http-linked-service"></a>Verknüpfter HTTP-Dienst

Bei diesem Beispiel wird der verknüpfte HTTP-Dienst mit anonymer Authentifizierung verwendet. Informationen zu den verschiedenen Authentifizierungstypen, die Sie verwenden können, finden Sie unter [Verknüpfter HTTP-Dienst](#linked-service-properties).

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>HTTP-Eingabedataset

Durch Festlegen von **external** auf **TRUE** wird der Data Factory-Dienst informiert, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory generiert wird.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Azure-Blob-Ausgabedataset

Daten werden stündlich in ein neues Blob geschrieben (**frequency**: **hour**, **interval**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline, die eine Kopieraktivität verwendet

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets konfiguriert ist. Für die Kopieraktivität ist die Ausführung einmal pro Stunde geplant. In der JSON-Definition der Pipeline ist der Typ **source** auf **HttpSource** und der Typ **sink** auf **BlobSink** festgelegt.

Eine Liste der von **HttpSource** unterstützten Eigenschaften finden Sie unter [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
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
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten aus einem Quelldataset zu Spalten aus einem Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung

Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.