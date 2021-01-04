---
title: Verschieben von Daten aus DB2 mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie Daten aus einer lokalen DB2-Datenbank mit der Kopieraktivität von Azure Data Factory verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e5d2c6b0460c3a7566adb17601aceb57e57f4d0b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001064"
---
# <a name="move-data-from-db2-by-using-azure-data-factory-copy-activity"></a>Verschieben von Daten aus DB2 mithilfe der Kopieraktivität von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-onprem-db2-connector.md)
> * [Version 2 (aktuelle Version)](../connector-db2.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [DB2 connector in V2](../connector-db2.md) (DB2-Connector in V2).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer lokalen DB2-Datenbank in einen anderen Datenspeicher zu verschieben. Sie können Daten in einen beliebigen Speicher kopieren, der als unterstützte Senke im Artikel [Aktivitäten zur Datenverschiebung in Data Factory](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführt wird. Dieser Artikel baut auf dem Artikel zu Data Factory auf, der eine Übersicht zur Datenverschiebung mit der Kopieraktivität und unterstützten Datenspeicherkombinationen bietet. 

Data Factory unterstützt derzeit nur das Verschieben von Daten aus einer DB2-Datenbank in eine [unterstützte Datenspeichersenke](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Das Verschieben von Daten aus anderen Datenspeichern in eine DB2-Datenbank wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen
Data Factory unterstützt das Herstellen einer Verbindung mit einer lokalen DB2-Datenbank mithilfe des [Datenverwaltungsgateways](data-factory-data-management-gateway.md). Ausführliche Anweisungen zum Einrichten des Gateways für eine Datenpipeline zum Verschieben von Daten finden Sie unter [Verschieben von Daten von lokalen Standorten in die Cloud](data-factory-move-data-between-onprem-and-cloud.md).

Es ist auch ein Gateway erforderlich, wenn DB2 auf einem virtuellen Azure-IaaS-Computer gehostet wird. Sie können das Gateway auf derselben IaaS-VM, auf der sich der Datenspeicher befindet, installieren. Solange das Gateway eine Verbindung mit der Datenbank herstellen kann, kann es auf einem anderen virtuellen Computer installiert werden.

Das Datenverwaltungsgateway verfügt über einen integrierten DB2-Treiber, daher entfällt das manuelle Installieren von Treibern, wenn Sie Daten aus DB2 kopieren.

> [!NOTE]
> Im Artikel [Beheben von Problemen bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- und Gatewayproblemen.


## <a name="supported-versions"></a>Unterstützte Versionen
Der DB2-Connector von Data Factory unterstützt die folgenden IBM DB2-Plattformen und -Versionen mit Distributed Relational Database Architecture (DRDA) SQL Access Manager Version 9, 10 und 11:

* IBM DB2 für z/OS, Version 11.1
* IBM DB2 für z/OS, Version 10.1
* IBM DB2 für i (AS400), Version 7.2
* IBM DB2 für i (AS400), Version 7.1
* IBM DB2 für Linux, UNIX und Windows (LUW), Version 11
* IBM DB2 für LUW, Version 10.5
* IBM DB2 für LUW, Version 10.1

> [!TIP]
> Wenn die Fehlermeldung „Das einer Anforderung zum Ausführen einer SQL-Anweisung entsprechende Paket wurde nicht gefunden. SQLSTATE=51002 SQLCODE=-805“ angezeigt wird, ist die Ursache dafür, dass für einen normalen Benutzer unter diesem Betriebssystem ein erforderliches Paket nicht erstellt wird. Um dieses Problem zu beheben, befolgen Sie die folgenden Anweisungen für Ihren DB2-Servertyp:
> - DB2 für i (AS400): Lassen Sie vor der Verwendung der Kopieraktivität einen Poweruser die Sammlung für den normalen Benutzer erstellen. Um die Sammlung zu erstellen, verwenden Sie den Befehl `create collection <username>`.
> - DB2 für z/OS oder LUW: Verwenden Sie ein Konto mit hohen Berechtigungen (Poweruser oder Administrator mit Paketberechtigungen und den Berechtigungen BIND, BINDADD und GRANT EXECUTE TO PUBLIC), um die Kopieraktivität einmal auszuführen. Das erforderliche Paket wird während des Kopiervorgangs automatisch erstellt. Anschließend können Sie für die folgenden Kopiervorgänge wieder das normale Benutzerkonto verwenden.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, um Daten mithilfe verschiedener Tools und APIs aus einem lokalen DB2-Datenspeicher zu verschieben: 

- Am einfachsten erstellen Sie eine Pipeline mit dem Kopier-Assistenten von Azure Data Factory. Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Kopier-Assistenten finden Sie im [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md). 
- Sie können auch Tools zum Erstellen einer Pipeline verwenden. Dazu gehören u. a. Visual Studio, Azure PowerShell, Azure Resource Manager-Vorlagen sowie .NET-API und REST-API. Eine ausführliche Anleitung zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen Sie verknüpfte Dienste zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen Sie DataSets zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. 
3. Erstellen Sie eine Pipeline mit einer Kopieraktivität, die ein DataSet als Eingabe und ein DataSet als Ausgabe akzeptiert. 

Wenn Sie den Kopier-Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Dienste, DataSets und die Pipeline) erstellt. Bei Verwendung von Tools oder APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Das JSON-Beispiel „Kopieren von Daten aus DB2 in Azure Blob Storage“ zeigt die JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem lokalen DB2-Datenspeicher verwendet werden.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für DB2-Datenspeicher verwendet werden.

## <a name="db2-linked-service-properties"></a>Eigenschaften des mit DB2 verknüpften Diensts
Die folgende Tabelle enthält die JSON-Eigenschaften, die spezifisch für einen mit DB2 verknüpften Dienst sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **type** |Diese Eigenschaft muss auf **OnPremisesDb2** festgelegt werden. |Ja |
| **server** |Der Name des DB2-Servers |Ja |
| **database** |Der Name der DB2-Datenbank |Ja |
| **schema** |Der Name des Schemas in der DB2-Datenbank. Diese Eigenschaft erfordert die Beachtung der Groß-/Kleinschreibung. |Nein |
| **authenticationType** |Der Typ der Authentifizierung für die Verbindung mit der DB2-Datenbank. Mögliche Werte: „Anonymous“, „Basic“ und „Windows“. |Ja |
| **username** |Der Name für das Benutzerkonto, wenn Sie die Standard- oder Windows-Authentifizierung verwenden. |Nein |
| **password** |Das Kennwort für das Benutzerkonto |Nein |
| **gatewayName** |Der Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der lokalen DB2-Datenbank verwenden soll |Ja |

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von DataSets zur Verfügung stehen, finden Sie im Artikel [Erstellen von DataSets](data-factory-create-datasets.md). Abschnitte wie **structure**, **availability** und **policy** des JSON-Codes eines DataSets sind bei allen DataSet-Typen (Azure SQL, Azure Blob Storage, Azure Table Storage usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein DataSet vom Typ **RelationalTable** (das das DB2-DataSet einschließt) hat die folgende Eigenschaft:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **tableName** |Der Name der Tabelle in der DB2-Datenbankinstanz, auf die der verknüpfte Dienst verweist. Diese Eigenschaft erfordert die Beachtung der Groß-/Kleinschreibung. |Nein (wenn die **query**-Eigenschaft einer Kopieraktivität vom Typ **RelationalSource** angegeben wurde) |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Kopieraktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften von Kopieraktivitäten wie **name**, **description**, **inputs** (Tabelle), **outputs** (Tabelle) und **policy** sind für alle Arten von Aktivitäten verfügbar. Die Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften des Typs je nach Art der Datenquellen und Senken.

Wenn bei der Kopieraktivität „source“ den Typ **RelationalSource** aufweist (zu dem DB2 gehört), sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| **query** |Verwendet die benutzerdefinierte Abfrage zum Lesen der Daten. |SQL-Abfragezeichenfolge. Beispiel: `"query": "select * from "MySchema"."MyTable""` |Nein (wenn die **tableName**-Eigenschaft eines DataSets angegeben wurde) |

> [!NOTE]
> Bei Schema- und Tabellennamen wird zwischen Groß- und Kleinschreibung unterschieden. Schließen Sie in der Abfrageanweisung Eigenschaftennamen in doppelte Anführungszeichen ("") ein.

## <a name="json-example-copy-data-from-db2-to-azure-blob-storage"></a>JSON-Beispiel: Kopieren von Daten aus DB2 in Azure Blob Storage
Dieses Beispiel stellt JSON-Beispieldefinitionen bereit, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Das Beispiel veranschaulicht das Kopieren von Daten aus einer DB2-Datenbank nach Blob Storage. Allerdings können mithilfe der Kopieraktivität von mithilfe von Azure Data Factory Daten in [alle unterstützten Datenspeicher-Senkentypen](data-factory-data-movement-activities.md#supported-data-stores-and-formats) kopiert werden.

Das Beispiel enthält die folgenden Data Factory-Entitäten:

- Einen verknüpften DB2-Dienst vom Typ [OnPremisesDb2](data-factory-onprem-db2-connector.md)
- Einen mit Azure Blob Storage verknüpften Dienst vom Typ [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
- Ein Eingabe-[DataSet](data-factory-create-datasets.md) vom Typ [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties)
- Ein [Ausgabedataset](data-factory-create-datasets.md) vom Typ [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
- Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die die Eigenschaften [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer DB2-Datenbank in ein Azure-Blob. Die im Beispiel verwendeten JSON-Eigenschaften werden in den Abschnitten nach den Entitätsdefinitionen beschrieben.

Zuerst installieren und konfigurieren Sie ein Datengateway. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und der Cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Mit DB2 verknüpfter Dienst**

```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Mit Azure Blob Storage verknüpfter Dienst**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**DB2-Eingabe-DataSet**

Im Beispiel wird vorausgesetzt, dass Sie in DB2 die Tabelle „MyTable“ erstellt haben, die für Zeitreihendaten eine Spalte namens „timestamp“ enthält.

Die **external**-Eigenschaften ist auf „TRUE“ festgelegt. Diese Einstellung teilt dem Data Factory-Dienst mit, dass dieses DataSet für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird. Beachten Sie, dass die **type**-Eigenschaft auf **RelationalTable** festgelegt ist.


```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
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

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben, indem Sie die **frequency**-Eigenschaft auf „Hour“ und die **interval**-Eigenschaft auf „1“ festlegen. Die **folderPath**-Eigenschaft des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobDb2DataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Pipeline für die Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung festgelegter Ein- und Ausgabe-DataSets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die SQL-Abfrage für die **query**-Eigenschaft wählt die Daten aus der Tabelle „Orders“ aus.

```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for the copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "select * from \"Orders\""
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "inputs": [
                    {
                        "name": "Db2DataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDb2DataSet"
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
                "name": "Db2ToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="type-mapping-for-db2"></a>Typzuordnung für DB2
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von nativen Quelltypen in .NET-Typen
2. Konvertieren von .NET-Typen in native Senkentypen

Die folgenden Zuordnungen werden angewendet, wenn die Kopieraktivität Daten aus einem DB2-Typ in einen .NET-Datentyp konvertiert:

| DB2-Datenbanktyp | .NET Framework-Typ |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| Xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Binary |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Numeric |Decimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| Xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quell-DataSet zu Spalten im Senken-DataSet finden Sie unter [Zuordnen von DataSet-Spalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-reads-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch die **policy**-Eigenschaft für Wiederholung für ein DataSet konfigurieren, um einen Slice erneut auszuführen, wenn ein Fehler auftritt. Stellen Sie sicher, dass dieselben Daten gelesen werden – egal, wie oft ein Slice ausgeführt wird, und egal, wie Sie den Slice erneut ausführen. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Im [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) werden wichtige Faktoren beschrieben, die sich auf die Leistung der Kopieraktivität auswirken, sowie Möglichkeiten zur Leistungsoptimierung.
