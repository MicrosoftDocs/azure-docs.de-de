---
title: Verschieben von Daten aus MongoDB
description: Erfahren Sie, wie Sie Daten aus der MongoDB-Datenbank mithilfe von Azure Data Factory verschieben.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387511"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Verschieben von Daten aus MongoDB mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-on-premises-mongodb-connector.md)
> * [Version 2 (aktuelle Version)](../connector-mongodb.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [MongoDB-Connector in V2](../connector-mongodb.md).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer lokalen MongoDB-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einer lokalen MongoDB-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem MongoDB-Datenspeicher in andere Datenspeicher, aber nicht das Verschieben von Daten aus anderen Datenspeichern in einen MongoDB-Datenspeicher.

## <a name="prerequisites"></a>Voraussetzungen
Damit der Azure Data Factory-Dienst eine Verbindung mit Ihrer lokalen MongoDB-Datenbank herstellen kann, müssen die folgenden Komponenten installiert werden:

- Diese MongoDB-Versionen werden unterstützt: 2.4, 2.6, 3.0, 3.2, 3.4 und 3.6.
- Datenverwaltungsgateway auf dem Computer, der die Datenbank hostet, oder auf einem separaten Computer, um zu vermeiden, dass der Computer mit der Datenbank um Ressourcen konkurriert. Datenverwaltungsgateway ist eine Software, die lokale Datenquellen mit Cloud-Diensten auf sichere, verwaltete Weise verbindet. Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie Einzelheiten zum Datenverwaltungsgateway. Schritt-für-Schritt-Anweisungen zum Einrichten des Gateways für eine Datenpipeline zum Verschieben von Daten finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md).

    Beim Installieren des Gateways wird automatisch ein Microsoft MongoDB-ODBC-Treiber installiert, der zum Herstellen einer Verbindung mit MongoDB verwendet wird.

    > [!NOTE]
    > Sie müssen das Gateway nutzen, um eine Verbindung mit MongoDB herzustellen, auch wenn MongoDB auf Azure IaaS-VMs gehostet wird. Wenn Sie versuchen, eine Verbindung mit einer MongoDB-Instanz herzustellen, die in der Cloud gehostet wird, können Sie auch die Gatewayinstanz in der IaaS-VM installieren.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem lokalen MongoDB-Datenspeicher verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem lokalen MongoDB-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus MongoDB in ein Azure-Blob](#json-example-copy-data-from-mongodb-to-azure-blob) in diesem Artikel.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für MongoDB-Quellen verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den verknüpften Dienst **OnPremisesMongoDb** spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **OnPremisesMongoDb**. |Ja |
| server |IP-Adresse oder Hostname des MongoDB-Servers |Ja |
| port |Der TCP-Port, den der MongoDB-Server verwendet, um auf Clientverbindungen zu lauschen |Optional, Standardwert: 27.017 |
| authenticationType |Basic oder Anonymous |Ja |
| username |Benutzerkonto für den Zugriff auf MongoDB |Ja (wenn die Standardauthentifizierung verwendet wird) |
| password |Kennwort für den Benutzer |Ja (wenn die Standardauthentifizierung verwendet wird) |
| authSource |Der Name der MongoDB-Datenbank, die Sie zum Überprüfen Ihrer Anmeldeinformationen zur Authentifizierung verwenden möchten |Optional (wenn die Standardauthentifizierung verwendet wird). Standardwert: verwendet das Administratorkonto und die Datenbank, die mit der databaseName-Eigenschaft angegeben wird |
| databaseName |Der Name der MongoDB-Datenbank, auf die Sie zugreifen möchten |Ja |
| gatewayName |Der Name des Gateways, das auf den Datenspeicher zugreift |Ja |
| encryptedCredential |Anmeldeinformationen, die vom Gateway verschlüsselt werden |Optional |

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **MongoDbCollection** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| collectionName |Der Name der Sammlung in der MongoDB-Datenbank |Ja |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Bei einer Quelle des Typs **MongoDbSource** sind im Abschnitt „typeProperties“ folgende Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-92-Abfragezeichenfolge. Beispiel: select * from MyTable. |Nein (wenn **collectionName** von **dataset** angegeben ist) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus MongoDB in ein Azure-Blob
Dieses Beispiel zeigt JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Es wird gezeigt, wie Sie Daten aus einer lokalen MongoDB in eine Azure Blob Storage-Instanz kopieren. Daten können jedoch auch mithilfe der Kopieraktivität in Azure Data Factory in eine beliebige der [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

Das Beispiel enthält die folgenden Data Factory-Entitäten:

1. Einen verknüpften Dienst des Typs [OnPremisesMongoDb](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [MongoDbCollection](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [MongoDbSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Das Beispiel kopiert Daten stündlich aus einem Abfrageergebnis in einer MongoDB-Datenbank in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) ein.

**Mit MongoDB verknüpfter Dienst:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Mit Azure Storage verknüpfter Dienst:**

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

**MongoDB-Eingabedataset:** Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Kopieraktivität in einer Pipeline mit einer MongoDB-Quelle und einer Blobsenke:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **MongoDbSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query** -Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Schema per Data Factory
Der Azure Data Factory-Dienst leitet ein Schema aus einer MongoDB-Sammlung mithilfe der letzten 100 Dokumente in der Sammlung ab. Wenn diese 100 Dokumente kein vollständiges Schema enthalten, können einige Spalten während des Kopiervorgangs ignoriert werden.

## <a name="type-mapping-for-mongodb"></a>Typzuordnung für MongoDB
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität mithilfe der folgenden beiden Schritte automatische Typkonvertierungen von Quelltypen in Senkentypen durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in MongoDB werden die folgenden Zuordnungen zwischen dem MongoDB-Typ und .NET-Typ verwendet.

| MongoDB-Typ | .NET Framework-Typ |
| --- | --- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |Datetime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| String |String |
| UUID |Guid |
| Object |Renormalisiert in vereinfachte Spalten mit dem geschachtelten Trennzeichen „_“ |

> [!NOTE]
> Weitere Informationen zur Unterstützung für Arrays mit virtuellen Tabellen finden Sie im Abschnitt [Unterstützung für komplexe Typen mit virtuellen Tabellen](#support-for-complex-types-using-virtual-tables) unten.

Die folgenden MongoDB-Datentypen werden derzeit nicht unterstützt: „DBPointer“, „JavaScript“, „MaxKey/MinKey“, „Regular Expression“, „Symbol“, „Timestamp“, „Undefined“.

## <a name="support-for-complex-types-using-virtual-tables"></a>Unterstützung für komplexe Typen mit virtuellen Tabellen
Azure Data Factory verwendet einen integrierten ODBC-Treiber, um eine Verbindung mit der MongoDB-Datenbank herzustellen und Daten daraus zu kopieren. Für komplexe Typen wie Arrays oder Objekte mit unterschiedlichen Typen in den Dokumenten renormalisiert der Treiber die Daten in die entsprechenden virtuellen Tabellen. Wenn eine Tabelle solche Spalten enthält, generiert der Treiber die folgenden virtuellen Tabellen:

* Eine **Basistabelle**, die die gleichen Daten wie die echte Tabelle enthält, mit Ausnahme der Spalten mit komplexen Typen. Für die Basistabelle wird der gleiche Name wie für die echte Tabelle verwendet, die sie repräsentiert.
* Eine **virtuelle Tabelle** für jede Spalte mit komplexen Typen (Erweiterung der geschachtelten Daten). Die virtuellen Tabellen werden mit dem Namen der echten Tabelle benannt und erhalten zusätzlich das Trennzeichen „_“ und den Namen der des Arrays oder Objekts.

Virtuelle Tabellen beziehen sich auf die Daten in der echten Tabelle, sodass der Treiber auf die denormalisierten Daten zugreifen kann. Details finden Sie unten im Abschnitt mit dem Beispiel. Sie können auf den Inhalt von MongoDB-Arrays zugreifen, indem Sie die virtuellen Tabellen abfragen und verknüpfen.

Sie können den [Assistenten zum Kopieren](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) verwenden, um die Liste mit den Tabellen in der MongoDB-Datenbank, einschließlich der virtuellen Tabellen, intuitiv anzuzeigen. Sie können im Kopier-Assistenten auch eine Abfrage erstellen und eine Überprüfung durchführen, um das Ergebnis anzuzeigen.

### <a name="example"></a>Beispiel
Beispielsweise ist „ExampleTable“ unten eine MongoDB-Tabelle, die eine Spalte (Invoices) mit einem Array von Objekten in jeder Zelle enthält sowie eine Spalte (Ratings) mit einem Array von skalaren Typen.

| _id | Customer Name | Invoices | Dienstebene | Ratings |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:”123”, item:”toaster”, price:”456”, discount:”0.2”}, {invoice_id:”124”, item:”oven”, price: ”1235”, discount: ”0.2”}] |Silber |[5,6] |
| 2222 |XYZ |[{invoice_id:”135”, item:”fridge”,price: ”12543”,discount: ”0.0”}] |Gold |[1,2] |

Der Treiber erzeugt mehrere virtuelle Tabellen, um diese einzelne Tabelle zu repräsentieren. Die erste virtuelle Tabelle ist die unten dargestellte Basistabelle mit dem Namen „ExampleTable“. Die Basistabelle enthält alle Daten der ursprünglichen Tabelle, aber die Daten aus den Arrays wurden ausgelassen und werden in den virtuellen Tabellen erweitert.

| _id | Customer Name | Dienstebene |
| --- | --- | --- |
| 1111 |ABC |Silber |
| 2222 |XYZ |Gold |

Die folgenden Tabellen enthalten die virtuellen Tabellen, die die ursprünglichen Arrays im Beispiel darstellen. Diese Tabellen enthalten Folgendes:

* Einen Verweis zurück auf die ursprüngliche Primärschlüsselspalte, die der Zeile des ursprünglichen Arrays entspricht (über die Spalte „_id“)
* Einen Hinweis auf die Position der Daten im ursprünglichen Array
* Die erweiterten Daten für jedes Element innerhalb des Arrays

Tabelle „ExampleTable_Invoices“:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | Preis | Discount |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |toaster |456 |0.2 |
| 1111 |1 |124 |oven |1235 |0.2 |
| 2222 |0 |135 |fridge |12543 |0,0 |

Tabelle „ExampleTable_Ratings“:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.

## <a name="next-steps"></a>Nächste Schritte
Im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) finden Sie schrittweise Anleitungen zum Erstellen einer Datenpipeline für das Verschieben von Daten aus einem lokalen Datenspeicher in einen Azure-Datenspeicher.
