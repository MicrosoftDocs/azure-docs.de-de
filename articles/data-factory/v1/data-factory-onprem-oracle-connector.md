---
title: Kopieren von Daten in bzw. aus Oracle mit Data Factory
description: Informationen zum Kopieren von Daten in eine und aus einer lokalen Oracle-Datenbank mit Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 02fc142a08176aa577250417c0e394218e832f34
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387341"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Kopieren von Daten in eine bzw. aus einer lokalen Oracle-Instanz mit Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-onprem-oracle-connector.md)
> * [Version 2 (aktuelle Version)](../connector-oracle.md)

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Wenn Sie die aktuelle Version des Azure Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Oracle-Connector in V2](../connector-oracle.md).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in eine und aus einer lokalen Oracle-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Sie können Daten *aus Oracle-Datenbank* in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern *in Oracle-Datenbank* kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>Voraussetzungen

Data Factory unterstützt das Herstellen einer Verbindung mit lokalen Oracle-Datenquellen mithilfe des Datenverwaltungsgateways. Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie Einzelheiten zum Datenverwaltungsgateway. Eine schrittweise Anleitung zum Einrichten des Gateways in einer Datenpipeline zum Verschieben von Daten finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und der Cloud](data-factory-move-data-between-onprem-and-cloud.md).

Das Gateway ist auch dann erforderlich, wenn Oracle in einer Azure IaaS-VM (Infrastructure-as-a-Service) gehostet wird. Sie können das Gateway auf dem gleichen virtuellen IaaS-Computer installieren wie den Datenspeicher oder auch auf einem anderen virtuellen Computer, solange das Gateway eine Verbindung mit der Datenbank herstellen kann.

> [!NOTE]
> Unter [Behandlung von Gatewayproblemen](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Problemen, die sich auf die Verbindung und das Gateway beziehen.

## <a name="supported-versions-and-installation"></a>Unterstützte Versionen und Installation

Dieser Connector für Oracle unterstützt zwei Treiberversionen:

- **Microsoft-Treiber für Oracle (empfohlen)** : Ab dem Datenverwaltungsgateway Version 2.7 wird mit dem Gateway automatisch ein Microsoft-Treiber für Oracle installiert. Sie müssen den Treiber nicht installieren oder aktualisieren, um eine Verbindung mit Oracle herzustellen. Sie können auch eine bessere Kopierleistung erzielen, wenn Sie diesen Treiber verwenden. Die folgenden Versionen von Oracle-Datenbanken werden unterstützt:
  - Oracle 12c R1 (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)

    > [!NOTE]
    > Der Oracle-Proxyserver wird nicht unterstützt.

    > [!IMPORTANT]
    > Der Microsoft-Treiber für Oracle unterstützt zurzeit nur das Kopieren von Daten aus Oracle. Der Treiber unterstützt nicht das Schreiben in Oracle. Die Testverbindungsfunktion auf der Registerkarte **Diagnose** des Datenverwaltungsgateways unterstützt diesen Treiber nicht. Alternativ können Sie den Assistenten zum Kopieren verwenden, um die Konnektivität zu überprüfen.
    >

- **Oracle-Datenanbieter für .NET**: Sie können den Oracle-Datenanbieter verwenden, um Daten aus oder nach Oracle zu kopieren. Diese Komponente ist in [Oracle Data Access Components (ODAC) für Windows](https://www.oracle.com/technetwork/topics/dotnet/downloads/)enthalten. Installieren Sie die entsprechende Version (32-Bit oder 64-Bit) auf dem Computer, auf dem das Gateway installiert ist. Der [Oracle-Datenanbieter .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149) kann auf Oracle Database 10g, Version 2 oder höher, zugreifen.

    Wenn Sie **XCopy-Installation** auswählen, führen Sie die in der Datei „readme.htm“ beschriebenen Schritte aus. Es wird empfohlen, den Installer mit der Benutzeroberfläche auszuwählen (nicht den XCopy-Installer).

    Starten Sie nach der Installation des Anbieters den Hostdienst des Datenverwaltungsgateways auf Ihrem Computer mithilfe des Applets „Dienste“ oder des Datenverwaltungsgateway-Konfigurations-Managers neu.

Wenn Sie zum Kopieren der Pipeline den Kopier-Assistenten verwenden, wird der Treibertyp automatisch bestimmt. Der Microsoft-Treiber wird standardmäßig verwendet, es sei denn, Ihre Gatewayversion ist älter als Version 2.7, oder Sie wählen Oracle als Senke aus.

## <a name="get-started"></a>Erste Schritte

Sie können eine Pipeline erstellen, die eine Kopieraktivität aufweist. Die Pipeline verschiebt Daten in eine oder aus einer lokalen Oracle-Datenbank mithilfe verschiedener Tools oder APIs.

Am einfachsten erstellen Sie eine Pipeline mit dem Kopier-Assistenten. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch eines der folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, eine **Azure Resource Manager-Vorlage**, die **.NET-API** oder die **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie eine schrittweise Anleitung, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Führen Sie unabhängig davon, ob Sie Tools oder APIs verwenden, die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory**. Eine Data Factory kann mindestens eine Pipeline enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus einer Oracle-Datenbank in Azure Blob Storage kopieren, erstellen Sie zwei verknüpfte Dienste, um die Oracle-Datenbank und Ihr Azure Storage-Konto mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für Oracle sind, finden Sie unter [Eigenschaften von verknüpften Diensten](#linked-service-properties).
3. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im vorherigen Schritt erwähnt wurde, erstellen Sie ein Dataset, um die Tabelle mit den Eingabedaten in Ihrer Oracle-Datenbank anzugeben. Sie erstellen ein weiteres Dataset zum Angeben eines Blobcontainers und des Ordners, in dem die aus der Oracle-Datenbank kopierten Daten enthalten sind. Informationen zu Dataseteigenschaften, die spezifisch für Oracle sind, finden Sie unter [Dataseteigenschaften](#dataset-properties).
4. Erstellen Sie eine **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im vorherigen Beispiel verwenden Sie **OracleSource** als Quelle und **BlobSink** als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang von Azure Blob Storage in eine Oracle-Datenbank durchführen, verwenden Sie entsprechend **BlobSource** und **OracleSink** in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für eine Oracle-Datenbank sind, finden Sie unter [Eigenschaften der Kopieraktivität](#copy-activity-properties). Weitere Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke finden Sie im vorherigen Abschnitt unter dem Link zu Ihrem Datenspeicher.

Wenn Sie den Assistenten verwenden, werden JSON-Definitionen für diese Data Factory-Entitäten automatisch für Sie erstellt: verknüpfte Dienste, Datasets und die Pipeline. Bei Verwendung von Tools oder APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Beispiele mit JSON-Definitionen für Data Factory-Entitäten, mit denen Sie Daten in eine oder aus einer lokalen Oracle-Datenbank kopieren, finden Sie unter „JSON-Beispiele“.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

In der folgenden Tabelle werden die JSON-Elemente beschrieben, die für den verknüpften Oracle-Dienst spezifisch sind:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die Eigenschaft **type** muss auf **OnPremisesOracle** festgelegt sein. |Ja |
| driverType | Legen Sie fest, welcher Treiber für das Kopieren von Daten aus einer bzw. in eine Oracle-Datenbank verwendet wird. Zulässige Werte sind **Microsoft** und **ODP** (Standardwert). Details zu den Treibern finden Sie unter [Unterstützte Versionen und Installation](#supported-versions-and-installation). | Nein |
| connectionString | Geben Sie Informationen, die für die Verbindung mit der Oracle-Datenbankinstanz erforderlich sind, für die Eigenschaft **connectionString** an. | Ja |
| gatewayName | Der Name des Gateways, das zum Herstellen einer Verbindung mit dem lokalen Oracle-Server verwendet wird. |Ja |

**Beispiel: Verwenden des Microsoft-Treibers**

> [!TIP]
> Wenn eine Fehlermeldung mit dem Hinweis „ORA-01025: UPI parameter out of range“ angezeigt wird und Sie die Oracle-Version 8i nutzen, können Sie Ihrer Verbindungszeichenfolge den Zusatz `WireProtocolMode=1` hinzufügen und den Vorgang wiederholen:

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Beispiel: Verwendung des ODP-Treibers**

Weitere Informationen zu zulässigen Formaten finden Sie unter [Oracle-Datenanbieter für .NET ODP](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/).

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Erstellen von Datasets](data-factory-create-datasets.md).

Die Abschnitte einer JSON-Datasetdatei (z.B. „structure“, „availability“ und „policy“) sind für alle Datasettypen (z.B. Oracle, Azure Blob Storage und Azure Table Storage) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset vom Typ **OracleTable** weist die folgenden Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Der Name der Tabelle in der Oracle-Datenbank, auf die der verknüpfte Dienst verweist. |Nein (wenn **oracleReaderQuery** oder **OracleSource** angegeben ist) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Erstellen von Pipelines](data-factory-create-pipelines.md).

Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

> [!NOTE]
> Die Kopieraktivität verwendet nur eine Eingabe und generiert nur eine Ausgabe.

Eigenschaften, die im Abschnitt **typeProperties** der Aktivität verfügbar sind, können dagegen je nach Aktivitätstyp variieren. Die Eigenschaften für die Kopieraktivität variieren je nach Art der Quelle und der Senke.

### <a name="oraclesource"></a>OracleSource

Wenn bei der Kopieraktivität eine Quelle vom Typ **OracleSource** verwendet wird, sind im Abschnitt **typeProperties** folgende Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| oracleReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |Eine SQL-Abfragezeichenfolge. Beispiel: „select \* from **MyTable**“. <br/><br/>Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: „select \* from **MyTable**“. |Nein<br />(wenn **tableName** von **dataset** angegeben ist) |

### <a name="oraclesink"></a>OracleSink

**OracleSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bevor ein Timeout auftritt. |**timespan**<br/><br/> Beispiel: 00:30:00 (30 Minuten) |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße den Wert von **writeBatchSize** erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 100) |
| sqlWriterCleanupScript |Gibt eine Abfrage für die auszuführende Kopieraktivität an, damit die Daten eines bestimmten Slice bereinigt werden. |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Gibt den Spaltennamen der Spalte an, die die Kopieraktivität mit einem automatisch generierten Slicebezeichner auffüllen soll. Der Wert für **sliceIdentifierColumnName** wird verwendet, um Daten eines bestimmten Slice bei erneuter Ausführung zu bereinigen. |Der Spaltenname einer Spalte, die den Datentyp **binary(32)** aufweist. |Nein |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>JSON-Beispiele zum Kopieren von Daten in die bzw. aus der Oracle-Datenbank

Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Die Beispiele zeigen, wie Daten aus einer oder in eine Oracle-Datenbank und in oder aus Azure Blob Storage kopiert werden. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory in jede der unter [Unterstützte Datenspeicher und Formate](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

**Beispiel: Kopieren von Daten aus Oracle in Azure Blob Storage**

Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Einen verknüpften Dienst des Typs [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties) als Quelle und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) als Senke verwendet.

Das Beispiel kopiert Daten stündlich aus einer Tabelle einer lokalen Oracle-Datenbank in ein Blob. Weitere Informationen zu den verschiedenen Eigenschaften, die im Beispiel verwendet werden, finden Sie in den Abschnitten, die auf die Beispiele folgen.

**Mit Oracle verknüpfter Dienst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Mit Azure Blob Storage verknüpfter Dienst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle-Eingabedataset**

Im Beispiel wird davon ausgegangen, dass Sie eine Tabelle namens **MyTable** in Oracle erstellt haben. Sie enthält eine Spalte namens **timestampcolumn** für Zeitreihendaten.

Durch Festlegen von **external**: **true** wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory generiert wird.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben (**frequency**: **hour**, **interval**: **1**). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slice, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet den Jahres-, Monats-, Tages- und Stundenanteil der Startzeit.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **OracleSource** und der Typ **sink** auf **BlobSink** festgelegt. Die SQL-Abfrage, die Sie mit der Eigenschaft **oracleReaderQuery** angeben, wählt die Daten der vergangenen Stunde zum Kopieren aus.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**Beispiel: Kopieren von Daten aus Azure Blob Storage nach Oracle**

In diesem Beispiel wird gezeigt, wie Sie Daten aus einem Azure Blob Storage-Konto in eine lokale Oracle-Datenbank kopieren. Sie können jedoch Daten *direkt* aus einer der unter [Unterstützte Datenspeicher und Formate](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Quellen kopieren, indem Sie die Kopieraktivität in Azure Data Factory verwenden.

Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Einen verknüpften Dienst des Typs [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) als Quelle und [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties) als Senke verwendet.

Das Beispiel kopiert Daten stündlich aus einem Blob in eine Tabelle in einer lokalen Oracle-Datenbank. Weitere Informationen zu den verschiedenen Eigenschaften, die im Beispiel verwendet werden, finden Sie in den Abschnitten, die auf die Beispiele folgen.

**Mit Oracle verknüpfter Dienst**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**Mit Azure Blob Storage verknüpfter Dienst**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure-Blob-Eingabedataset**

Daten werden stündlich aus einem neuen Blob übernommen (**frequency**: **hour**, **interval**: **1**). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slice, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet den Jahres-, Monats- und Tagesanteil der Startzeit. Im Dateinamen wird der Stundenanteil der Startzeit verwendet. Durch Festlegen der Einstellung **external**: **true** wird der Data Factory-Dienst informiert, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory generiert wird.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Oracle-Ausgabedataset**

Im Beispiel wird davon ausgegangen, dass Sie eine Tabelle namens **MyTable** in Oracle erstellt haben. Erstellen Sie die Tabelle in Oracle mit der gleichen Anzahl von Spalten, die Sie in der CSV-Blobdatei erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **OracleSink** festgelegt.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

### <a name="problem-1-net-framework-data-provider"></a>Problem 1: .NET Framework-Datenanbieter

**Fehlermeldung**

```text
Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.
```

**Mögliche Ursachen**

* Der .NET Framework-Datenanbieter für Oracle wurde nicht installiert.
* Der .NET Framework-Datenanbieter für Oracle wurde für .NET Framework 2.0 installiert und in den Ordnern für .NET Framework 4.0 nicht gefunden.

**Lösung**

* Falls Sie den .NET Framework-Datenanbieter für Oracle nicht installiert haben, [installieren Sie ihn](https://www.oracle.com/technetwork/topics/dotnet/downloads/), und wiederholen Sie anschließend das Szenario.
* Falls Sie die Fehlermeldung auch nach der Installation des Anbieters erhalten, führen Sie die folgenden Schritte aus:
    1. Öffnen Sie die Computerkonfigurationsdatei für .NET 2.0 aus dem Ordner <Systemdatenträger\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config.
    2. Suchen Sie nach **Oracle-Datenanbieter für .NET**. Es sollte ein Eintrag vorhanden sein, wie im folgenden Beispiel unter **system.data** > **DbProviderFactories** gezeigt: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`
* Kopieren Sie diesen Eintrag in die Datei „machine.config“ im folgenden .NET 4.0-Ordner: <Systemdatenträger\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config. Ändern Sie die Version dann in „4.xxx.x.x“.
* Führen Sie **gacutil /i [Anbieterpfad]** aus, um „<ODP.NET-Installationspfad\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll“ im globalen Assemblycache (GAC) zu installieren.

### <a name="problem-2-datetime-formatting"></a>Problem 2: Datums-/Uhrzeitformat

**Fehlermeldung**

```text
Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.
```

**Lösung**

Möglicherweise müssen Sie die Abfragezeichenfolge in Ihrer Kopieraktivität entsprechend der Konfiguration von Datumsangaben in Ihrer Oracle-Datenbank anpassen. Dies ist ein Beispiel (mit der Funktion **to_date**):

```console   
"oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"
```


## <a name="type-mapping-for-oracle"></a>Typzuordnung für Oracle

Wie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren aus nativen Quelltypen in den .NET-Typ.
2. Konvertieren aus dem .NET-Typ in den nativen Senkentyp.

Wenn Sie Daten aus Oracle verschieben, werden die folgenden Zuordnungen zwischen dem Oracle-Datentyp und dem .NET-Typ verwendet (und umgekehrt):

| Oracle-Datentyp | Datentyp ".NET Framework" |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(nur unterstützt für Oracle 10g oder höher bei Verwendung eines Microsoft-Treibers) |
| CHAR |String |
| CLOB |String |
| DATE |Datetime |
| GLEITKOMMAZAHL |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| INTEGER |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |TimeSpan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Dezimal, Zeichenfolge (wenn Genauigkeit > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Datetime |
| TIMESTAMP WITH TIME ZONE |Datetime |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Die Datentypen **INTERVAL YEAR TO MONTH** und **INTERVAL DAY TO SECOND** werden bei Verwendung eines Microsoft-Treibers nicht unterstützt.

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten

Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen

Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird (entweder manuell oder durch eine Wiederholungsrichtlinie), stellen Sie sicher, dass die gleichen Daten unabhängig davon gelesen werden, wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung

Das [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken. Sie können sich auch über verschiedene Möglichkeiten zur Leistungsoptimierung informieren.
