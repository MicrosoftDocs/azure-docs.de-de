---
title: Verschieben von Daten aus Cassandra mit Data Factory
description: Enthält Informationen zum Verschieben von Daten aus einer lokalen Cassandra-Datenbank mit Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f96680f1ea91434c84d6606e3637c68c1cb5a84
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019632"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Verschieben von Daten aus einer lokalen Cassandra-Datenbank mit Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-onprem-cassandra-connector.md)
> * [Version 2 (aktuelle Version)](../connector-cassandra.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Cassandra-Connector in V2](../connector-cassandra.md).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer lokalen Cassandra-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einem lokalen Cassandra-Datenspeicher in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem Cassandra-Datenspeicher in andere Datenspeicher, aber nicht das Verschieben von Daten aus anderen Datenspeichern in einen Cassandra-Datenspeicher.

## <a name="supported-versions"></a>Unterstützte Versionen
Der Cassandra-Connector unterstützt die folgenden Versionen von Cassandra: 2.x und 3.x. Für Aktivitäten auf einer selbst gehosteten Integration Runtime wird seit IR-Version 3.7 Cassandra 3.x unterstützt.

## <a name="prerequisites"></a>Voraussetzungen
Damit der Azure Data Factory-Dienst imstande ist, eine Verbindung mit Ihrer lokalen Cassandra-Datenbank herzustellen, müssen Sie das Datenverwaltungsgateway auf dem Computer installieren, der die Datenbank hostet, oder auf einem separaten Computer, um zu vermeiden, dass der Computer mit der Datenbank um Ressourcen konkurriert. Das Datenverwaltungsgateway ist eine Clientkomponente, die lokale Datenquellen sicher und verwaltet mit Clouddiensten verbindet. Im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md) finden Sie Einzelheiten zum Datenverwaltungsgateway. Schritt-für-Schritt-Anweisungen zum Einrichten des Gateways für eine Datenpipeline zum Verschieben von Daten finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md).

Sie müssen das Gateway verwenden, um Verbindungen mit einer Cassandra-Datenbank herzustellen, sogar wenn die Datenbank in der Cloud gehostet ist, beispielsweise auf einem virtuellen Azure IaaS-Computers. Das Gateway kann auf dem gleichen virtuellen Computer wie die Datenbank oder auf einem anderen virtuellen Computer installiert sein, solange das Gateway eine Verbindung mit der Datenbank herstellen kann.

Beim Installieren des Gateways wird automatisch ein Microsoft Cassandra-ODBC-Treiber installiert, der zum Herstellen einer Verbindung mit der Cassandra-Datenbank verwendet wird. Aus diesem Grund müssen Sie beim Kopieren von Daten aus der Cassandra-Datenbank manuell keine Treiber auf dem Gatewaycomputer installieren.

> [!NOTE]
> Unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem lokalen Teradata-Datenspeicher verschiebt.

- Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.
- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem lokalen Cassandra-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus Cassandra in ein Azure-Blob](#json-example-copy-data-from-cassandra-to-azure-blob) in diesem Artikel.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Cassandra-Datenspeicher verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die speziell für den verknüpften Cassandra-Dienst gelten.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **OnPremisesCassandra**. |Ja |
| host |Mindestens eine IP-Adresse oder ein Hostname von Cassandra-Servern.<br/><br/>Geben Sie eine durch Trennzeichen getrennte Liste mit IP-Adressen oder Hostnamen an, um gleichzeitig mit allen Servern Verbindungen herzustellen. |Ja |
| port |Der TCP-Port, den der Cassandra-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein, Standardwert: 9042 |
| authenticationType |Basic (Standard) oder Anonymous (Anonym) |Ja |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| password |Geben Sie ein Kennwort für das Benutzerkonto an. |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| gatewayName |Der Name des Gateways, das zum Herstellen der Verbindung mit der lokalen Cassandra-Datenbank verwendet wird. |Ja |
| encryptedCredential |Anmeldeinformationen, die vom Gateway verschlüsselt werden. |Nein |

>[!NOTE]
>Verbindungen mit Cassandra über TLS werden derzeit nicht unterstützt.

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset des Typs **CassandraTable** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| keyspace |Name des Keyspace oder Schemas in der Cassandra-Datenbank. |Ja (wenn **query** für **CassandraSource** nicht definiert ist). |
| tableName |Name der Tabelle in der Cassandra-Datenbank. |Ja (wenn **query** für **CassandraSource** nicht definiert ist). |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt typeProperties der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Bei einer Quelle des Typs **SqlDWSource** sind im Abschnitt „typeProperties“ folgende Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-92-Abfrage oder CQL-Abfrage. Weitere Informationen finden Sie in der [Referenz zu CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Geben Sie beim Verwenden der SQL-Abfrage **keyspace name.table name** für die Tabelle an, die Sie abfragen möchten. |Nein (wenn tableName und keyspace im Dataset definiert sind) |
| consistencyLevel |Mit der Konsistenzebene (consistencyLevel) wird angegeben, wie viele Replikate auf eine Leseanforderung reagieren müssen, bevor Daten an die Clientanwendung zurückgegeben werden. Cassandra überprüft die angegebene Anzahl von Replikaten auf Daten, um die Leseanforderung zu erfüllen. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Ausführliche Informationen finden Sie unter [Configuring data consistency](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) (Konfigurieren der Datenkonsistenz). |Nein. Der Standardwert ist ONE. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus Cassandra in ein Azure-Blob
Dieses Beispiel zeigt JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Es wird gezeigt, wie Sie Daten aus einer lokalen Cassandra-Datenbank in eine Azure Blob Storage-Instanz kopieren. Daten können jedoch auch mithilfe der Kopieraktivität in Azure Data Factory in eine beliebige der [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

> [!IMPORTANT]
> Dieses Beispiel enthält JSON-Codeausschnitte. Eine schrittweise Anleitung zum Erstellen der Data Factory ist nicht enthalten. Einen Artikel mit schrittweisen Anleitungen finden Sie unter [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

Das Beispiel enthält die folgenden Data Factory-Entitäten:

* Einen verknüpften Dienst des Typs [OnPremisesCassandra](#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [CassandraTable](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [CassandraSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

**Mit Cassandra verknüpfter Dienst:**

In diesem Beispiel wird der verknüpfte **Cassandra** -Dienst verwendet. Der Abschnitt [Verknüpfter Cassandra-Dienst](#linked-service-properties) enthält eine Liste mit den Eigenschaften, die von diesem verknüpften Dienst unterstützt werden.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra-Eingabedataset:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
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

Durch Festlegen von **external** auf **true** wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieraktivität in einer Pipeline mit einer Cassandra-Quelle und einer Blobsenke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **CassandraSource** und der Typ **sink** auf **BlobSink** festgelegt.

Unter [RelationalSource-Typeigenschaften](#copy-activity-properties) finden Sie die Liste der Eigenschaften, die von RelationalSource unterstützt werden.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Typzuordnung für Cassandra
| Cassandra-Typ | .NET-basierter Typ |
| --- | --- |
| ASCII |String |
| bigint |Int64 |
| BLOB |Byte[] |
| BOOLEAN |Boolean |
| DECIMAL |Decimal |
| Double |Double |
| GLEITKOMMAZAHL |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| timestamp |Datetime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |Decimal |

> [!NOTE]
> Informationen zu Sammlungstypen (map, set, list usw.) finden Sie im Abschnitt [Verwenden von Cassandra-Sammlungstypen mit einer virtuellen Tabelle](#work-with-collections-using-virtual-table) .
>
> Benutzerdefinierte Typen werden nicht unterstützt.
>
> Die Länge von „Binary Column“ und „String Column“ darf 4.000 nicht übersteigen.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Verwenden von Cassandra-Sammlungstypen mit einer virtuellen Tabelle
Azure Data Factory verwendet einen integrierten ODBC-Treiber, um eine Verbindung mit der Cassandra-Datenbank herzustellen und Daten daraus zu kopieren. Für Sammlungstypen, z.B. „map“, „set“ und „list“, normalisiert der Treiber die Daten erneut in die entsprechenden virtuellen Tabellen. Wenn eine Tabelle Sammlungsspalten enthält, generiert der Treiber die folgenden virtuellen Tabellen:

* Eine **Basistabelle**, die die gleichen Daten wie die tatsächliche Tabelle enthält, mit Ausnahme der Sammlungsspalten. Für die Basistabelle wird der gleiche Name wie für die echte Tabelle verwendet, die sie repräsentiert.
* Eine **virtuelle Tabelle** für jede Sammlungsspalte (Erweiterung der geschachtelten Daten). Die virtuellen Tabellen, die Sammlungen repräsentieren, werden mit dem Namen der tatsächlichen Tabelle benannt und erhalten zusätzlich das Trennzeichen „*vt*“ und den Namen der Spalte.

Virtuelle Tabellen beziehen sich auf die Daten in der echten Tabelle, sodass der Treiber auf die denormalisierten Daten zugreifen kann. Details finden Sie unten im Abschnitt mit dem Beispiel. Sie können auf den Inhalt von Cassandra-Sammlungen zugreifen, indem Sie die virtuellen Tabellen abfragen und verknüpfen.

Sie können den [Kopier-Assistenten](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) verwenden, um die Liste mit den Tabellen in der Cassandra-Datenbank anzuzeigen, einschließlich der virtuellen Tabellen. Sie können im Kopier-Assistenten auch eine Abfrage erstellen und eine Überprüfung durchführen, um das Ergebnis anzuzeigen.

### <a name="example"></a>Beispiel
Die folgende Beispieltabelle „ExampleTable“ ist beispielsweise eine Cassandra-Datenbanktabelle, die eine Spalte mit dem Namen „pk_int“ für ganzzahlige Primärschlüssel, eine Textspalte mit dem Namen „value“ und die Spalten „list“, „map“ und „set“ („StringSet“) enthält.

| pk_int | Wert | List | Karte | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"sample value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"sample value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Der Treiber erzeugt mehrere virtuelle Tabellen, um diese einzelne Tabelle zu repräsentieren. Die Fremdschlüsselspalten in den virtuellen Tabellen verweisen auf die Primärschlüsselspalten in der echten Tabelle und geben an, welcher Zeile der echten Tabelle die Zeile der virtuellen Tabelle entspricht.

Die erste virtuelle Tabelle ist die unten dargestellte Basistabelle mit dem Namen „ExampleTable“. Die Basistabelle enthält die gleichen Daten wie die ursprüngliche Datenbanktabelle, mit Ausnahme der Sammlungen, die in dieser Tabelle fehlen und in anderen virtuellen Tabellen enthalten sind.

| pk_int | Wert |
| --- | --- |
| 1 |"sample value 1" |
| 3 |"sample value 3" |

Die folgenden Tabellen enthalten die virtuellen Tabellen, in denen die Daten aus den Spalten „List“, „Map“ und „StringSet“ erneut normalisiert werden. Die Spalten mit Namen, die auf „_index“ oder „_key“ enden, geben die Position der Daten in der Originalliste (list) bzw. -zuordnung (map) an. Die Spalten mit Namen, die auf „_value“ enden, enthalten die erweiterten Daten aus der Sammlung.

#### <a name="table-exampletable_vt_list"></a>Tabelle „ExampleTable_vt_List“:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>Tabelle „ExampleTable_vt_Map“:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Ein |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>Tabelle „ExampleTable_vt_StringSet“:
| pk_int | StringSet_value |
| --- | --- |
| 1 |Ein |
| 1 |B |
| 1 |C |
| 3 |Ein |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
