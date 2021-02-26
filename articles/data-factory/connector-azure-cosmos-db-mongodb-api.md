---
title: Kopieren von Daten aus der Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie mithilfe von Data Factory Daten aus unterstützten Quelldatenspeichern in die oder aus der Azure Cosmos DB-API für MongoDB in unterstützte Senkenspeicher kopieren können.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 6f1e865daf9ba42126c0f8a341a54d87ac7f374a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393087"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopieren von Daten in die oder aus der Azure Cosmos DB-API für MongoDB mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus der und in die Azure Cosmos DB-API für MongoDB zu kopieren. Dieser Artikel baut auf dem Artikel zur [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!NOTE]
>Dieser Connector unterstützt lediglich das Kopieren von Daten in die bzw. aus der Azure Cosmos DB-API für MongoDB. Informationen zur SQL-API finden Sie unter [SQL-API-Connector von Cosmos DB](connector-azure-cosmos-db.md). Andere API-Typen werden derzeit nicht unterstützt.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus der Azure Cosmos DB-API für MongoDB in einen beliebigen unterstützten Senkendatenspeicher oder Daten aus einem beliebigen unterstützten Quelldatenspeicher in die Azure Cosmos DB-API für MongoDB kopieren. Eine Liste der Datenspeicher, die die Kopieraktivität als Quellen und Senken unterstützt, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Sie können den Connector für Azure Cosmos DB-API für MongoDB für die folgenden Aufgaben verwenden:

- Kopieren von Daten aus der oder in die [Azure Cosmos DB-API für MongoDB](../cosmos-db/mongodb-introduction.md).
- Schreiben in Azure Cosmos DB als **insert** oder **upsert**.
- Importieren und Exportieren von JSON-Dokumenten in ihrem jeweiligen Zustand oder Kopieren von Daten aus einem tabellarischen Dataset oder in ein tabellarisches Dataset. Die Beispiele zeigen eine SQL-Datenbank und eine CSV-Datei. Informationen zum Kopieren von Dokumenten in ihrem jeweiligen Zustand in bzw. aus JSON-Dateien oder in eine andere bzw. aus einer anderen Azure Cosmos DB-Sammlung finden Sie unter „Importieren oder Exportieren von JSON-Dokumenten“.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die Sie zum Definieren von Data Factory-Entitäten verwenden können, die es speziell für die Azure Cosmos DB-API für MongoDB gibt.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Azure Cosmos DB-API für MongoDB verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **CosmosDbMongoDbApi** festgelegt werden. | Ja |
| connectionString |Geben Sie die Verbindungszeichenfolge für Ihre Azure Cosmos DB-API für MongoDB an. Diese ist im Azure-Portal auf dem Blatt für Ihre Cosmos DB-Instanz unter der primären oder sekundären Verbindungszeichenfolge mit dem folgenden Muster aufgeführt: `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` <br/><br />Sie können auch ein Kennwort in Azure Key Vault speichern und die `password`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md).|Ja |
| database | Der Name der Datenbank, auf die Sie zugreifen möchten. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md). Folgende Eigenschaften werden für den Azure Cosmos DB-API für MongoDB-Dataset unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **CosmosDbMongoDbApiCollection** festgelegt werden. |Ja |
| collectionName |Der Name der Azure Cosmos DB-Sammlung. |Ja |

**Beispiel**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Cosmos DB-API für MongoDB-Quelle und -Senke unterstützt werden.

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB-API für MongoDB als Quelle

Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **CosmosDbMongoDbApiSource** festgelegt werden. |Ja |
| filter | Gibt den Auswahlfilter mit Abfrageoperatoren an. Um alle Dokumente in einer Sammlung zurückzugeben, lassen Sie diesen Parameter aus oder übergeben Sie ein leeres Dokument ({}). | Nein |
| cursorMethods.project | Gibt die Felder an, die in den Dokumenten für die Projektion zurückgegeben werden sollen. Um alle Felder in den entsprechenden Dokumenten zurückzugeben, lassen Sie diesen Parameter aus. | Nein |
| cursorMethods.sort | Gibt die Reihenfolge an, in der übereinstimmende Dokumente von der Abfrage zurückgegeben werden. Siehe [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nein |
| cursorMethods.limit |    Gibt die maximale Anzahl von Dokumenten an, die vom Server zurückgegeben werden. Siehe [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nein | 
| cursorMethods.skip | Gibt die Anzahl von Dokumenten an, die übersprungen werden sollen und ab denen MongoDB mit der Rückgabe von Ergebnissen beginnt. Siehe [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nein |
| batchSize | Gibt die Anzahl von Dokumenten an, die in jedem Batch der Antwort von der MongoDB-Instanz zurückgegeben werden sollen. In den meisten Fällen wirkt sich eine Änderung der Batchgröße nicht auf den Benutzer oder die Anwendung aus. Cosmos DB begrenzt die einzelnen Batches auf eine maximale Größe von 40 MB. Dies entspricht der Summe der „batchSize“-Größen für die Anzahl von Dokumenten. Verringern Sie deshalb diesen Wert, falls Ihre Dokumente groß sind. | Nein<br/>(der Standardwert ist **100**) |

>[!TIP]
>ADF unterstützt die Nutzung eines BSON-Dokuments im **Strict-Modus**. Stellen Sie sicher, dass die Filterabfrage den Strict-Modus und nicht den Shell-Modus aufweist. Eine weitere Beschreibung finden Sie im [MongoDB-Handbuch](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
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
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB-API für MongoDB als Senke

Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **CosmosDbMongoDbApiSink** festgelegt werden. |Ja |
| writeBehavior |Beschreibt, wie Daten in Azure Cosmos DB geschrieben werden. Zulässige Werte: **insert** und **upsert**.<br/><br/>Das Verhalten von **upsert** besteht darin, das Dokument zu ersetzen, wenn ein Dokument mit dem gleichen `_id`-Typ bereits vorhanden ist. Andernfalls wird das Dokument eingefügt.<br /><br />**Hinweis**: Data Factory generiert automatisch einen `_id`-Typ für ein Dokument, wenn ein `_id`-Typ weder im Originaldokument noch durch Spaltenzuordnung angegeben wird. Dies bedeutet, dass Sie sicherstellen müssen, dass Ihr Dokument eine ID besitzt, damit **upsert** wie erwartet funktioniert. |Nein<br />(der Standardwert ist **insert**) |
| writeBatchSize | Die **writeBatchSize**-Eigenschaft steuert die Größe der in jeden Batch zu schreibenden Dokumente. Sie können versuchen, den Wert für **writeBatchSize** zu erhöhen, um die Leistung zu verbessern, oder den Wert verringern, falls Ihre Dokumente groß sind. |Nein<br />(der Standardwert ist **10.000**) |
| writeBatchTimeout | Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. Der zulässige Wert ist „timespan“. | Nein<br/>(der Standardwert ist **00:30:00** – 30 Minuten) |

>[!TIP]
>Informationen zum Importieren von JSON-Dokumenten in unveränderter Form finden Sie im Abschnitt [Importieren oder Exportieren von JSON-Dokumenten](#import-and-export-json-documents). Informationen zum Kopieren aus tabellarisch strukturierten Daten finden Sie unter [Schemazuordnung](#schema-mapping).

**Beispiel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>Importieren und Exportieren von JSON-Dokumenten

Sie können diesen Azure Cosmos DB-Connector verwenden, um die folgenden Aufgaben auf einfache Weise zu erledigen:

* Kopieren von Dokumenten in ihrem jeweiligen Zustand zwischen zwei Azure Cosmos DB-Sammlungen.
* Importieren von JSON-Dokumenten aus verschiedenen Quellen in Azure Cosmos DB, z. B. aus MongoDB, Azure Blob Storage, Azure Data Lake Storage und anderen dateibasierten Speichern, die von Azure Data Factory unterstützt werden.
* Exportieren von JSON-Dokumenten aus einer Azure Cosmos DB-Sammlung in verschiedene dateibasierte Speicher.

So erhalten Sie eine vom Schema unabhängige Kopie:

* Aktivieren Sie bei Verwendung des Tools zum Kopieren von Daten die Option **Export as-is to JSON files or Cosmos DB collection** (Wie vorhanden in JSON-Dateien oder Cosmos DB-Sammlung exportieren).
* Wenn Sie die Aktivität „Dokumenterstellung“ verwenden, wählen Sie das JSON-Format mit dem entsprechenden Dateispeicher für Quelle oder Senke aus.

## <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus der Azure Cosmos DB-API für MongoDB in eine tabellarische Senke oder umgekehrt finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).

Insbesondere beim Schreiben in Cosmos DB müssen Sie sicherstellen, dass Sie Cosmos DB mit der richtigen Objekt-ID aus den Quelldaten füllen. Wenn Sie beispielsweise eine Spalte „id“ in der SQL-Datenbanktabelle haben und diesen Wert als Dokument-ID in MongoDB für Insert/Upsert-Vorgänge verwenden möchten, müssen Sie die richtige Schemazuordnung gemäß der Strict-Modusdefinition von MongoDB (`_id.$oid`) wie folgt festlegen:

![Zuordnungs-ID in der MongoDB-Senke](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Nach Ausführen der Kopieraktivität wird die nachstehende BSON-ObjectId in der Senke generiert:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).