---
title: Kopieren von Daten aus Cassandra mit Azure Data Factory
description: Erfahren Sie, wie Daten aus Cassandra mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: a3cd3c3ae28ae302e9469a71d00054152a9b5fb5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383703"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Kopieren von Daten aus Cassandra mit Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-onprem-cassandra-connector.md)
> * [Aktuelle Version](connector-cassandra.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einer Cassandra-Datenbank zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Cassandra-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus einer Cassandra-Datenbank in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Der Cassandra-Connector unterstützt insbesondere Folgendes:

- Cassandra **Version 2.x und 3.x**.
- Kopieren von Daten unter Verwendung der **Standard**- oder **anonymen** Authentifizierung

>[!NOTE]
>Für Aktivitäten auf einer selbst gehosteten Integration Runtime wird seit IR-Version 3.7 Cassandra 3.x unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Die Integrationslaufzeit bietet einen integrierten Cassandra-Treiber. Daher müssen beim Kopieren von Daten aus bzw. nach Cassandra keine Treiber manuell installiert werden.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Cassandra-Connector verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Cassandra verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Cassandra** |Ja |
| host |Mindestens eine IP-Adresse oder ein Hostname von Cassandra-Servern.<br/>Geben Sie eine durch Trennzeichen getrennte Liste mit IP-Adressen oder Hostnamen an, um gleichzeitig mit allen Servern Verbindungen herzustellen. |Ja |
| port |Der TCP-Port, den der Cassandra-Server verwendet, um auf Clientverbindungen zu lauschen. |Nein (Standard = 9042) |
| authenticationType | Typ der Authentifizierung für die Verbindung mit der Cassandra-Datenbank.<br/>Zulässige Werte sind: **Standard** und **Anonym**. |Ja |
| username |Geben Sie einen Benutzernamen für das Benutzerkonto an. |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| password |Geben Sie ein Kennwort für das Benutzerkonto an. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja, wenn authenticationType auf „Basic“ (Standard) festgelegt ist. |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

>[!NOTE]
>Verbindungen mit Cassandra über TLS werden derzeit nicht unterstützt.

**Beispiel:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
            "username": "<username>",
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

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Cassandra-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Cassandra die type-Eigenschaft des Datasets auf **CassandraTable** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **CassandraTable** | Ja |
| keyspace |Name des Keyspace oder Schemas in der Cassandra-Datenbank. |Nein (wenn „query“ für „CassandraSource“ nicht angegeben ist) |
| tableName |Name der Tabelle in der Cassandra-Datenbank. |Nein (wenn „query“ für „CassandraSource“ nicht angegeben ist) |

**Beispiel:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität


Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Cassandra-Quelle unterstützt werden.

### <a name="cassandra-as-source"></a>Cassandra als Quelle

Legen Sie zum Kopieren von Daten aus Cassandra den Quelltyp in der Kopieraktivität auf **CassandraSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **CassandraSource** | Ja |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. SQL-92-Abfrage oder CQL-Abfrage. Weitere Informationen finden Sie in der [Referenz zu CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Geben Sie beim Verwenden der SQL-Abfrage **keyspace name.table name** für die Tabelle an, die Sie abfragen möchten. |Nein (wenn „tableName“ und „keyspace“ im Dataset angegeben sind) |
| consistencyLevel |Mit der Konsistenzebene (consistencyLevel) wird angegeben, wie viele Replikate auf eine Leseanforderung reagieren müssen, bevor Daten an die Clientanwendung zurückgegeben werden. Cassandra überprüft die angegebene Anzahl von Replikaten auf Daten, um die Leseanforderung zu erfüllen. Ausführliche Informationen finden Sie unter [Configuring data consistency](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) (Konfigurieren der Datenkonsistenz).<br/><br/>Zulässige Werte sind: **ONE**, **TWO**, **THREE**, **QUORUM**, **ALL**, **LOCAL_QUORUM**, **EACH_QUORUM** und **LOCAL_ONE**. |Nein (Standard = `ONE`) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Datentypzuordnung für Cassandra

Beim Kopieren von Daten aus Cassandra werden die folgenden Zuordnungen von Cassandra-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| Cassandra-Datentyp | Data Factory-Zwischendatentyp |
|:--- |:--- |
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

## <a name="work-with-collections-using-virtual-table"></a>Verwenden von Cassandra-Sammlungstypen mit einer virtuellen Tabelle

Azure Data Factory verwendet einen integrierten ODBC-Treiber, um eine Verbindung mit der Cassandra-Datenbank herzustellen und Daten daraus zu kopieren. Für Sammlungstypen, z.B. „map“, „set“ und „list“, normalisiert der Treiber die Daten erneut in die entsprechenden virtuellen Tabellen. Wenn eine Tabelle Sammlungsspalten enthält, generiert der Treiber die folgenden virtuellen Tabellen:

* Eine **Basistabelle**, die die gleichen Daten wie die tatsächliche Tabelle enthält, mit Ausnahme der Sammlungsspalten. Für die Basistabelle wird der gleiche Name wie für die echte Tabelle verwendet, die sie repräsentiert.
* Eine **virtuelle Tabelle** für jede Sammlungsspalte (Erweiterung der geschachtelten Daten). Die virtuellen Tabellen, die Sammlungen repräsentieren, werden mit dem Namen der tatsächlichen Tabelle benannt und erhalten zusätzlich das Trennzeichen „*vt*“ und den Namen der Spalte.

Virtuelle Tabellen beziehen sich auf die Daten in der echten Tabelle, sodass der Treiber auf die denormalisierten Daten zugreifen kann. Details finden Sie unten im Abschnitt mit dem Beispiel. Sie können auf den Inhalt von Cassandra-Sammlungen zugreifen, indem Sie die virtuellen Tabellen abfragen und verknüpfen.

### <a name="example"></a>Beispiel

Die folgende Beispieltabelle „ExampleTable“ ist beispielsweise eine Cassandra-Datenbanktabelle, die eine Spalte mit dem Namen „pk_int“ für ganzzahlige Primärschlüssel, eine Textspalte mit dem Namen „value“ und die Spalten „list“, „map“ und „set“ („StringSet“) enthält.

| pk_int | Wert | List | Karte | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"sample value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"sample value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Der Treiber erzeugt mehrere virtuelle Tabellen, um diese einzelne Tabelle zu repräsentieren. Die Fremdschlüsselspalten in den virtuellen Tabellen verweisen auf die Primärschlüsselspalten in der echten Tabelle und geben an, welcher Zeile der echten Tabelle die Zeile der virtuellen Tabelle entspricht.

Die erste virtuelle Tabelle ist die unten dargestellte Basistabelle mit dem Namen „ExampleTable“: 

| pk_int | Wert |
| --- | --- |
| 1 |"sample value 1" |
| 3 |"sample value 3" |

Die Basistabelle enthält die gleichen Daten wie die ursprüngliche Datenbanktabelle, mit Ausnahme der Sammlungen, die in dieser Tabelle fehlen und in anderen virtuellen Tabellen enthalten sind.

Die folgenden Tabellen enthalten die virtuellen Tabellen, in denen die Daten aus den Spalten „List“, „Map“ und „StringSet“ erneut normalisiert werden. Die Spalten mit Namen, die auf „_index“ oder „_key“ enden, geben die Position der Daten in der Originalliste (list) bzw. -zuordnung (map) an. Die Spalten mit Namen, die auf „_value“ enden, enthalten die erweiterten Daten aus der Sammlung.

**Tabelle „ExampleTable_vt_List“:**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**Tabelle „ExampleTable_vt_Map“:**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |Ein |
| 1 |S2 |b |
| 3 |S1 |t |

**Tabelle „ExampleTable_vt_StringSet“:**

| pk_int | StringSet_value |
| --- | --- |
| 1 |Ein |
| 1 |B |
| 1 |C |
| 3 |Ein |
| 3 |E |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
