---
title: Verschieben von Daten aus SAP Business Warehouse mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie Daten aus SAP Business Warehouse mithilfe von Azure Data Factory verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fb91a09ed31658c2d547a7b46cf2f986bfbd0e50
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508287"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Verschieben von Daten aus SAP Business Warehouse mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-sap-business-warehouse-connector.md)
> * [Version 2 (aktuelle Version)](../connector-sap-business-warehouse.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [SAP Business Warehouse-Connector in V2](../connector-sap-business-warehouse.md).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer lokalen SAP Business Warehouse-Datenbank (SAP BW) zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einem lokalen SAP Business Warehouse-Datenspeicher in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus SAP Business Warehouse in andere Datenspeicher, aber nicht das Verschieben aus anderen Datenspeichern in SAP Business Warehouse. 

## <a name="supported-versions-and-installation"></a>Unterstützte Versionen und Installation
Dieser Connector unterstützt SAP Business Warehouse Version 7.x. Er unterstützt Kopieren von Daten aus InfoCubes und QueryCubes (einschließlich BEx-Abfragen) über MDX-Abfragen.

Um Konnektivität zur SAP BW-Instanz zu aktivieren, installieren Sie die folgenden Komponenten:
- **Datenverwaltungsgateway:** Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit lokalen Datenspeichern (z.B. SAP Business Warehouse) über eine Komponente, die als Datenverwaltungsgateway bezeichnet wird. Wenn Sie mehr zum Datenverwaltungsgateway erfahren möchten und eine schrittweise Anleitung zum Einrichten des Gateways wünschen, lesen Sie den Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md). Das Gateway ist selbst dann erforderlich, wenn SAP Business Warehouse auf einem virtuellen Azure IaaS-Computer gehostet wird. Das Gateway kann auf dem gleichen virtuellen Computer wie der Datenspeicher oder auf einem anderen virtuellen Computer installiert werden, solange das Gateway eine Verbindung mit der Datenbank herstellen kann.
- **SAP NetWeaver-Bibliothek** auf dem Gatewaycomputer. Sie erhalten die SAP Netweaver-Bibliothek vom SAP-Administrator oder direkt aus dem [SAP Software Download Center](https://support.sap.com/swdc). Suchen Sie nach der **SAP Note #1025361**, um den Downloadspeicherort für die neueste Version zu ermitteln. Vergewissern Sie sich, dass die Architektur für die SAP NetWeaver-Bibliothek (32-Bit oder 64-Bit) Ihrer Gatewayinstallation entspricht. Installieren Sie dann alle Dateien, die im SAP NetWeaver RFC SDK enthalten sind, entsprechend der SAP-Mitteilung (SAP Note). Die SAP NetWeaver-Bibliothek ist auch in der SAP Client Tools-Installation enthalten.

> [!TIP]
> Legen Sie die DLLs, die aus dem NetWeaver-RFC-SDK extrahiert wurden, im Ordner „system32“ ab.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem lokalen Teradata-Datenspeicher verschiebt. 

- Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten. 
- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können. 

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. 
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. 

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einer lokalen SAP Business Warehouse-Instanz verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus SAP Business Warehouse in ein Azure-Blob](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob) in diesem Artikel. 

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für SAP BW-Datenspeicher verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SAP Business Warehouse (BW) verknüpften Dienst spezifisch sind.

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
server | Der Name des Servers, auf dem sich die SAP BW-Instanz befindet. | Zeichenfolge | Ja
systemNumber | Die Systemnummer des SAP BW-Systems. | Zweistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja
clientId | Client-ID des Clients im SAP BW-System. | Dreistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja
username | Der Name des Benutzers, der Zugriff auf den SAP-Server hat | Zeichenfolge | Ja
password | Kennwort für den Benutzer | Zeichenfolge | Ja
gatewayName | Der Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen SAP BW-Instanz verwenden soll. | Zeichenfolge | Ja
encryptedCredential | Die verschlüsselte Zeichenfolge mit Anmeldeinformationen | Zeichenfolge | Nein

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Es sind keine typspezifischen Eigenschaften, die für das SAP BW-Dataset des Typs **RelationalTable** unterstützt werden. 


## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn die Quelle bei der Kopieraktivität den Typ **RelationalSource** hat (zu dem SAP BW gehört), sind im Abschnitt „typeProperties“ die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage | Gibt die MDX-Abfrage an, mit der Daten aus der SAP BW-Instanz gelesen werden. | MDX-Abfrage | Ja |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus SAP Business Warehouse in ein Azure-Blob
Das folgende Beispiel zeigt JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. In diesem Beispiel wird gezeigt, wie Sie Daten aus einem lokalen SAP Business Warehouse-System in Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.  

> [!IMPORTANT]
> Dieses Beispiel enthält JSON-Codeausschnitte. Eine schrittweise Anleitung zum Erstellen der Data Factory ist nicht enthalten. Einen Artikel mit schrittweisen Anleitungen finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

Das Beispiel enthält die folgenden Data Factory-Entitäten:

1. Einen verknüpften Dienst des Typs [SapBw](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [RelationalTable](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden stündlich Daten aus einer SAP Business Warehouse-Instanz in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als Erstes richten Sie das Datenverwaltungsgateway ein. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Mit SAP Business Warehouse verknüpfter Dienst
Dieser verknüpfte Dienst verbindet Ihre SAP BW-Instanz mit der Data Factory. Die „type“-Eigenschaft wird auf **SapBw** festgelegt. Im Abschnitt „typeProperties“ werden Verbindungsinformationen für die SAP BW-Instanz bereitgestellt. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst
Dieser verknüpfte Dienst verbindet Ihr Azure Storage-Konto mit der Data Factory. Die „type“-Eigenschaft wird auf **AzureStorage** festgelegt. Im Abschnitt „typeProperties“ werden Verbindungsinformationen für das Azure Storage-Konto bereitgestellt.

```json
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

### <a name="sap-bw-input-dataset"></a>SAP BW-Eingabedataset
Dieses Dataset definiert das SAP Business Warehouse-Dataset. Sie legen Sie den Typ des Data Factory-Datasets auf **RelationalTable** fest. Aktuell geben Sie keine typspezifischen Eigenschaften für ein SAP BW-Dataset an. Die Abfrage in der Definition der Kopieraktivität gibt an, welche Daten aus der SAP BW-Instanz gelesen werden sollen. 

Durch Festlegen der „external“-Eigenschaft auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

Die Eigenschaften „frequency“ und „interval“ definieren den Zeitplan. In diesem Fall werden die Daten stündlich aus der SAP BW-Instanz gelesen. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Azure-Blob-Ausgabedataset
Dieses Dataset definiert das Azure-Blob-Ausgabedatase. Die „type“-Eigenschaft wird auf „AzureBlob“ festgelegt. Im Abschnitt „typeProperties“ ist angegeben, wo die Daten, die aus der SAP BW-Instanz kopiert werden, gespeichert werden. Die Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline mit Kopieraktivität
Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** (für SAP BW-Quelle) und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query**-Eigenschaft angegebene Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```

### <a name="type-mapping-for-sap-bw"></a>Typzuordnung für SAP BW
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten aus SAP BW werden die folgenden Zuordnungen zwischen den SAP BW-Typen und den .NET-Typen verwendet.

Datentyp im ABAP-Wörterbuch | .NET-Datentyp
-------------------------------- | --------------
ACCP |  Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNIT | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).


## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
