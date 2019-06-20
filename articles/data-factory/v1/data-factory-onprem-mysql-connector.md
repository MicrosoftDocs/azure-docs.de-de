---
title: Verschieben von Daten aus MySQL mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Daten aus der MySQL-Datenbank mithilfe von Azure Data Factory verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 452f4fce-9eb5-40a0-92f8-1e98691bea4c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/06/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: de1263d68e96a23bd6b5eca4297e74b56ba22e40
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021636"
---
# <a name="move-data-from-mysql-using-azure-data-factory"></a>Verschieben von Daten aus MySQL mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](data-factory-onprem-mysql-connector.md)
> * [Version 2 (aktuelle Version)](../connector-mysql.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [MySQL connector in V2](../connector-mysql.md) (MySQL-Connector in V2).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer lokalen MySQL-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einer lokalen MySQL-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem MySQL-Datenspeicher in andere Datenspeicher, aber nicht das Verschieben von Daten aus anderen Datenspeichern in einen MySQL-Datenspeicher. 

## <a name="prerequisites"></a>Voraussetzungen
Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit lokalen MySQL-Datenquellen mithilfe des Datenverwaltungsgateways. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways.

Das Gateway ist erforderlich, auch wenn die MySQL-Datenbank auf einem virtuellen Azure IaaS-Computer gehostet wird. Das Gateway kann auf dem gleichen virtuellen Computer wie der Datenspeicher oder auf einem anderen virtuellen Computer installiert werden, solange das Gateway eine Verbindung mit der Datenbank herstellen kann.

> [!NOTE]
> Unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.

## <a name="supported-versions-and-installation"></a>Unterstützte Versionen und Installation
Damit das Datenverwaltungsgateway eine Verbindung mit der MySQL-Datenbank herstellt, müssen Sie den [MySQL Connector/Net für Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) (Version 6.6.5 bis 6.10.7) auf dem System installieren, auf dem sich auch das Datenverwaltungsgateway befindet. Dieser 32-Bit-Treiber ist mit 64-Bit-Datenverwaltungsgateways kompatibel. MySQL Version 5.1 und höher wird unterstützt.

> [!TIP]
> Wenn der Fehler „Fehler bei Authentifizierung, da die Gegenstelle den Transportdatenstrom geschlossen hat.“ auftritt, sollten Sie ein Upgrade für den MySQL-Connector/Net auf eine höhere Version durchführen.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem lokalen Teradata-Datenspeicher verschiebt. 

- Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten. 
- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Azure-Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlagen**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können. 

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. 
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. 

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem lokalen MySQL-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus MySQL in ein Azure-Blob](#json-example-copy-data-from-mysql-to-azure-blob) in diesem Artikel. 

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für MySQL-Datenspeicher verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit MySQL verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **OnPremisesMySql**. |JA |
| server |Name des MySQL-Servers. |JA |
| database |Name der MySQL-Datenbank. |JA |
| schema |Name des Schemas in der Datenbank. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit der MySQL-Datenbank. Mögliche Werte: `Basic`. |JA |
| userName |Geben Sie einen Benutzernamen für das Herstellen der Verbindung mit der MySQL-Datenbank an. |JA |
| password |Geben Sie das Kennwort für das angegebene Benutzerkonto an. |JA |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen MySQL-Datenbank verwenden soll. |JA |

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ **RelationalTable** (wozu ein MySQL-Dataset gehört) hat die folgenden Eigenschaften.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle in der MySQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Nein (wenn **query** von **RelationalSource** angegeben ist) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn die Quelle der Kopieraktivität den Typ **RelationalSource** hat (zu dem MySQL gehört), sind im Abschnitt typeProperties die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| query |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. |Nein (wenn **tableName** von **Dataset** angegeben ist) |


## <a name="json-example-copy-data-from-mysql-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus MySQL in ein Azure-Blob
Dieses Beispiel stellt JSON-Beispieldefinitionen bereit, die Sie zum Erstellen einer Pipeline mit dem [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder mit [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Es wird gezeigt, wie Sie Daten aus einer lokalen MySQL-Datenbank in eine Azure Blob Storage-Instanz kopieren. Daten können jedoch auch mithilfe der Kopieraktivität in Azure Data Factory in eine beliebige der [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

> [!IMPORTANT]
> Dieses Beispiel enthält JSON-Codeausschnitte. Eine schrittweise Anleitung zum Erstellen der Data Factory ist nicht enthalten. Einen Artikel mit schrittweisen Anleitungen finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

Das Beispiel enthält die folgenden Data Factory-Entitäten:

1. Einen verknüpften Dienst des Typs [OnPremisesMySql](data-factory-onprem-mysql-connector.md#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [RelationalTable](data-factory-onprem-mysql-connector.md#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](data-factory-onprem-mysql-connector.md#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer MySQL-Datenbank in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als Erstes richten Sie das Datenverwaltungsgateway ein. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Mit MySQL verknüpfter Dienst:**

```JSON
    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
        }
      }
    }
```

**Mit Azure Storage verknüpfter Dienst:**

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

**MySQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in MySQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
            "typeProperties": {},
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```JSON
    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query** -Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

```JSON
    {
        "name": "CopyMySqlToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }
```


### <a name="type-mapping-for-mysql"></a>Typzuordnung für MySQL
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in MySQL werden die folgenden Zuordnungen zwischen MySQL-Typ und .NET-Typ verwendet.

| Typ "MySQL-Datenbank" | Typ ".NET Framework" |
| --- | --- |
| bigint (ohne Vorzeichen) |DECIMAL |
| bigint |Int64 |
| Bit |DECIMAL |
| Blob |Byte[] |
| bool |Boolescher Wert |
| char |Zeichenfolge |
| date |DateTime |
| Datetime |DateTime |
| decimal |DECIMAL |
| double precision |Double |
| Double |Double |
| enum |Zeichenfolge |
| float |Single |
| int (ohne Vorzeichen) |Int64 |
| int |Int32 |
| integer (ohne Vorzeichen) |Int64 |
| integer |Int32 |
| long varbinary |Byte[] |
| long varchar |Zeichenfolge |
| longblob |Byte[] |
| longtext |Zeichenfolge |
| mediumblob |Byte[] |
| mediumint (ohne Vorzeichen) |Int64 |
| mediumint |Int32 |
| mediumtext |Zeichenfolge |
| numeric |DECIMAL |
| real |Double |
| set |Zeichenfolge |
| smallint (ohne Vorzeichen) |Int32 |
| smallint |Int16 |
| text |Zeichenfolge |
| time |Zeitraum |
| timestamp |DateTime |
| tinyblob |Byte[] |
| tinyint (ohne Vorzeichen) |Int16 |
| tinyint |Int16 |
| tinytext |Zeichenfolge |
| varchar |Zeichenfolge |
| year |int |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
