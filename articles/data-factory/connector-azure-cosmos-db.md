---
title: Kopieren und Transformieren von Daten in Azure Cosmos DB (SQL-API)
description: Hier erfahren Sie, wie Sie mithilfe von Data Factory Daten in und aus Azure Cosmos DB (SQL-API) kopieren sowie Daten in Azure Cosmos DB (SQL-API) transformieren.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: bb9f2673eb080ee2919297fcbb5199f99d176bce
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013682"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Cosmos DB (SQL-API) mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-azure-documentdb-connector.md)
> * [Aktuelle Version](connector-azure-cosmos-db.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Kopieraktivität in Azure Data Factory aus und in Azure Cosmos DB (SQL-API) kopieren sowie Daten mithilfe von Datenfluss in Azure Cosmos DB (SQL API) transformieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).



>[!NOTE]
>Dieser Connector unterstützt nur Cosmos DB SQL-API. Informationen zur MongoDB-API finden Sie unter [Connector für Azure Cosmos DB-API für MongoDB](connector-azure-cosmos-db-mongodb-api.md). Andere API-Typen werden derzeit nicht unterstützt.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Connector für Azure Cosmos DB (SQL API) wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Im Rahmen der Kopieraktivität unterstützt dieser Azure Cosmos DB (SQL-API)-Connector folgende Aktivitäten:

- Kopieren von Daten in die oder aus der [SQL-API](../cosmos-db/introduction.md) von Azure Cosmos DB.
- Schreiben in Azure Cosmos DB als **insert** oder **upsert**.
- Importieren und Exportieren von JSON-Dokumenten in ihrem jeweiligen Zustand oder Kopieren von Daten aus einem tabellarischen Dataset oder in ein tabellarisches Dataset. Die Beispiele zeigen eine SQL-Datenbank und eine CSV-Datei. Informationen zum Kopieren von Dokumenten in ihrem jeweiligen Zustand in bzw. aus JSON-Dateien oder in eine andere bzw. aus einer anderen Azure Cosmos DB-Sammlung finden Sie unter [Importieren und Exportieren von JSON-Dokumenten](#import-and-export-json-documents).

Data Factory wird in die [Azure Cosmos DB-BuklExecutor-Bibliothek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) integriert, um beim Schreiben in Azure Cosmos DB die beste Leistung zu erzielen.

> [!TIP]
> Das Video [Datenmigration](https://youtu.be/5-SRNiC_qOU) führt Sie schrittweise durch das Kopieren von Daten aus Azure Blob Storage in Azure Cosmos DB. Das Video beschreibt auch Überlegungen zur Leistungsoptimierung beim Erfassen von Daten in Azure Cosmos DB im Allgemeinen.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die Sie zum Definieren von Data Factory-Entitäten verwenden können, die spezifisch für Azure Cosmos DB (SQL-API) sind.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den verknüpften Azure Cosmos DB-Dienst (SQL-API) unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **CosmosDb** festgelegt werden. | Ja |
| connectionString |Geben Sie die zum Herstellen einer Verbinden mit der Azure Cosmos DB-Datenbank erforderlichen Informationen an.<br />**Hinweis**: Sie müssen Datenbankinformationen in der Verbindungszeichenfolge angeben, wie in den folgenden Beispielen gezeigt. <br/> Sie können auch den Kontoschlüssel in Azure Key Vault speichern und die `accountKey`-Konfiguration aus der Verbindungszeichenfolge pullen. Ausführlichere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kontoschlüssels in Azure Key Vault**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets und verknüpfte Dienste](concepts-datasets-linked-services.md).

Folgende Eigenschaften werden für das Azure Cosmos DB (SQL-API)-Dataset unterstützt: 

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** des Datasets muss auf **CosmosDbSqlApiCollection** festgelegt werden. |Ja |
| collectionName |Der Name der Azure Cosmos DB-Dokumentsammlung. |Ja |

Wenn Sie ein Dataset vom Typ „DocumentDbCollection“ verwenden, wird es aus Gründen der Abwärtskompatibilität für die Kopier- und Lookup-Aktivität weiterhin unverändert unterstützt. Für Datenfluss wird es nicht unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden.

**Beispiel**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Azure Cosmos DB-Quelle und -Senke (SQL-API) unterstützt werden. Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB (SQL API) als Quelle

Legen Sie zum Kopieren von Daten aus Azure Cosmos DB (SQL-API) den **Quelltyp** in der Copy-Aktivität auf **DocumentDbCollectionSource** fest. 

Die folgenden Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** der Quelle für die Kopieraktivität muss auf **CosmosDbSqlApiSource** festgelegt werden. |Ja |
| Abfrage |Geben Sie die Azure Cosmos DB-Abfrage an, um Daten zu lesen.<br/><br/>Beispiel:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nein <br/><br/>Falls nicht angegeben, wird die folgende SQL-Anweisung ausgeführt: `select <columns defined in structure> from mycollection` |
| preferredRegions | Die bevorzugte Liste der Regionen, mit denen beim Abrufen von Daten aus Cosmos DB eine Verbindung hergestellt werden soll. | Nein |
| pageSize | Die Anzahl der Dokumente pro Seite des Abfrageergebnisses. Der Standardwert ist „-1“. Dies bedeutet, dass im Ergebnis die dienstseitige dynamische Seitengröße bis zu 1000 verwendet wird. | Nein |

Wenn Sie eine Quelle vom Typ „DocumentDbCollectionSource“ verwenden, wird sie aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, in Zukunft das neue Modell zu verwenden, das umfangreichere Funktionen zum Kopieren von Daten aus Cosmos DB enthält.

**Beispiel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als Methode zum Kopieren von Daten aus Cosmos DB hat es sich bewährt, die Zuordnung in der Kopieraktivität anzugeben, es sei denn, Sie möchten [JSON-Dokumente in ihrem jetzigen Zustand exportieren](#import-and-export-json-documents). Data Factory beachtet die Zuordnung, die Sie für die Aktivität angegeben haben. Wenn eine Zeile einen Wert für eine Spalte nicht enthält, wird für die Spalte ein NULL-Wert zurückgegeben. Wenn Sie keine Zuordnung angeben, leitet Data Factory das Schema anhand der ersten Zeile in den Daten ab. Wenn die erste Zeile nicht das vollständige Schema enthält, fehlen im Ergebnis der Aktivität einige Spalten.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB (SQL API) als Senke

Legen Sie zum Kopieren von Daten in Azure Cosmos DB (SQL-API) den **Senkentyp** in der Copy-Aktivität auf **DocumentDbCollectionSink** fest. 

Die folgenden Eigenschaften werden im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die Eigenschaft **type** der Senke für die Kopieraktivität muss auf **CosmosDbSqlApiSink** festgelegt werden. |Ja |
| writeBehavior |Beschreibt, wie Daten in Azure Cosmos DB geschrieben werden. Zulässige Werte: **insert** und **upsert**.<br/><br/>Das Verhalten von **upsert** besteht darin, das Dokument zu ersetzen, wenn ein Dokument mit der gleichen ID bereits vorhanden ist. Andernfalls wird das Dokument eingefügt.<br /><br />**Hinweis**: Data Factory generiert automatisch eine ID für ein Dokument, wenn eine ID weder im Originaldokument noch durch Spaltenzuordnung angegeben wird. Dies bedeutet, dass Sie sicherstellen müssen, dass Ihr Dokument eine ID besitzt, damit **upsert** wie erwartet funktioniert. |Nein<br />(der Standardwert ist **insert**) |
| writeBatchSize | Data Factory verwendet die [Azure Cosmos DB-BulkExecutor-Bibliothek](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started) zum Schreiben von Daten in Azure Cosmos DB. Die Eigenschaft **writeBatchSize** steuert die Größe der von Azure Data Factory in der Bibliothek bereitgestellten Dokumente. Sie können versuchen, den Wert für **writeBatchSize** zu erhöhen, um die Leistung zu verbessern, oder den Wert verringern, falls Ihre Dokumente groß sind. Weitere Tipps finden Sie weiter unten. |Nein<br />(der Standardwert ist **10.000**) |
| disableMetricsCollection | Data Factory sammelt Metriken wie Cosmos DB-Anforderungseinheiten für die Leistungsoptimierung von Kopiervorgängen und Empfehlungen. Wenn Sie sich wegen dieses Verhaltens Gedanken machen, geben Sie `true` an, um es zu deaktivieren. | Nein (Standard = `false`) |

>[!TIP]
>Informationen zum Importieren von JSON-Dokumenten in ihrem jetzige Zustand finden Sie im Abschnitt [Importieren oder Exportieren von JSON-Dokumenten](#import-and-export-json-documents). Informationen zum Kopieren aus tabellarisch strukturierten Daten finden Sie unter [Migrieren von relationalen Datenbanken in Cosmos DB](#migrate-from-relational-database-to-cosmos-db).

>[!TIP]
>Cosmos DB begrenzt die Größe der einzelnen Anforderung auf 2 MB. Die Formel lautet: Anforderungsgröße = Einzeldokumentgröße * Schreibbatchgröße. Sollte ein Fehler mit dem Hinweis auftreten, dass die **Anforderung zu groß ist**, **verringern Sie den `writeBatchSize`-Wert** in der Kopiersenkenkonfiguration.

Wenn Sie eine Quelle vom Typ „DocumentDbCollectionSink“ verwenden, wird sie aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, in Zukunft das neue Modell zu verwenden, das umfangreichere Funktionen zum Kopieren von Daten aus Cosmos DB enthält.

**Beispiel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>Schemazuordnung

Informationen zum Kopieren von Daten aus Azure Cosmos DB in eine tabellarische Senke oder umgekehrt finden Sie unter [Schemazuordnung](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Beim Transformieren von Daten im Zuordnungsdatenfluss können Sie Sammlungen in Cosmos DB lesen und in diese schreiben. Weitere Informationen finden Sie unter [Quelltransformation für Zuordnungsdatenflüsse](data-flow-source.md) und [Senkentransformation für einen Datenfluss](data-flow-sink.md).

### <a name="source-transformation"></a>Quellentransformation

Spezifische Einstellungen für Azure Cosmos DB sind auf der Registerkarte **Quellenoptionen** der Quellentransformation verfügbar. 

**Include system columns** (Systemspalten einschließen): Wenn für diese Einstellung TRUE gilt, werden ```id```, ```_ts``` und andere Systemspalten in die Datenflussmetadaten von Cosmos DB eingeschlossen. Beim Aktualisieren von Sammlungen ist es wichtig, dass diese eingeschlossen sind, damit Sie die vorhandene Zeilen-ID erfassen können.

**Seitengröße:** Die Anzahl der Dokumente pro Seite des Abfrageergebnisses. Der Standardwert ist „-1“, bei dem die dynamische Seite des Diensts mit bis zu 1000 verwendet wird.

**Durchsatz:** Legen Sie einen optionalen Wert für die Anzahl von RUs fest, die Sie für jede Ausführung dieses Datenflusses während des Lesevorgangs auf die CosmosDB-Sammlung anwenden möchten. Der Mindestwert ist 400.

**Bevorzugte Region:** Sie können die bevorzugten Leseregionen für diesen Prozess auswählen.

#### <a name="json-settings"></a>JSON-Einstellungen

**Einzelnes Dokument:** Wählen Sie diese Option aus, wenn die gesamte Datei von ADF wie ein einzelnes JSON-Dokument behandelt werden soll.

**Spaltennamen ohne Anführungszeichen:** Wählen Sie diese Option aus, wenn die Spaltennamen im JSON-Code nicht in Anführungszeichen gesetzt sind.

**Mit Kommentaren:** Verwenden Sie diese Auswahl, wenn Ihre JSON-Dokumente über Kommentare in den Daten verfügen.

**Einfache Anführungszeichen:** Diese Option sollte ausgewählt werden, wenn die Spalten und Werte in Ihrem Dokument in einfache Anführungszeichen gesetzt sind.

**Umgekehrter Schrägstrich mit Escapezeichen:** Wählen Sie diese Option aus, wenn Sie in Ihrem JSON-Code umgekehrte Schrägstriche als Escapezeichen verwenden.

### <a name="sink-transformation"></a>Senkentransformation

Spezifische Einstellungen für Azure Cosmos DB sind auf der Registerkarte **Einstellungen** der Senkentransformation verfügbar.

**Updatemethode:** Bestimmt, welche Vorgänge für das Datenbankziel zulässig sind. Standardmäßig sind lediglich Einfügevorgänge zulässig. Wenn Sie für Zeilen Update-, Upsert- oder Löschvorgänge verwenden möchten, fügen Sie zunächst eine Transformation zur Änderung von Zeilen hinzu, um Zeilen für diese Aktionen zu kennzeichnen. Für Update-, Upsert- und Löschvorgänge muss mindestens eine Schlüsselspalte festgelegt werden, um die Zeile zu bestimmen, die geändert werden soll.

**Collection action** (Aktion für die Sammlung): Bestimmt, ob die Zielsammlung vor dem Schreibvorgang neu erstellt werden soll.
* Keine: Es wird keine Aktion an der Sammlung vorgenommen.
* Neu erstellen: Die Sammlung wird gelöscht und neu erstellt.

**Batchgröße**: Steuert, wie viele Zeilen in die einzelnen Buckets geschrieben werden. Durch größere Batches werden zwar Komprimierung und Arbeitsspeicheroptimierung verbessert, beim Zwischenspeichern von Daten besteht aber die Gefahr, dass Ausnahmen wegen unzureichenden Arbeitsspeichers auftreten.

**Partitionsschlüssel:** Geben Sie eine Zeichenfolge ein, die den Partitionsschlüssel für Ihre Sammlung darstellt. Beispiel: ```/movies/title```

**Durchsatz:** Legen Sie einen optionalen Wert für die Anzahl von RUs fest, die Sie für jede Ausführung dieses Datenflusses auf die CosmosDB-Sammlung anwenden möchten. Der Mindestwert ist 400.

**Write throughput budget** (Schreibdurchsatz): Ein Integer, der die Anzahl RUs darstellt, die Sie dem Spark-Sammelerfassungsauftrag zuordnen möchten. Dieser Wert ist unabhängig vom gesamten der Sammlung zugeordneten Durchsatz.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="import-and-export-json-documents"></a>Importieren und Exportieren von JSON-Dokumenten

Sie können diesen Azure Cosmos DB-Connector (SQL-API) verwenden, um die folgenden Aufgaben auf einfache Weise zu erledigen:

* Kopieren von Dokumenten in ihrem jeweiligen Zustand zwischen zwei Azure Cosmos DB-Sammlungen.
* Importieren von JSON-Dokumenten aus verschiedenen Quellen in Azure Cosmos DB, z.B. aus Azure Blob Storage, Azure Data Lake Storage und anderen dateibasierten Speichern, die von Azure Data Factory unterstützt werden.
* Exportieren von JSON-Dokumenten aus einer Azure Cosmos DB-Sammlung in verschiedene dateibasierte Speicher.

So erhalten Sie eine vom Schema unabhängige Kopie:

* Aktivieren Sie bei Verwendung des Tools zum Kopieren von Daten die Option **Export as-is to JSON files or Cosmos DB collection** (Wie vorhanden in JSON-Dateien oder Cosmos DB-Sammlung exportieren).
* Wenn Sie die Aktivität „Dokumenterstellung“ verwenden, wählen Sie das JSON-Format mit dem entsprechenden Dateispeicher für Quelle oder Senke aus.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>Migrieren aus relationalen Datenbanken in Cosmos DB

Beim Migrieren aus einer relationalen Datenbank, z. B. von SQL Server zu Azure Cosmos DB, kann die Kopieraktivität Tabellendaten aus der Quelle einfach zuordnen, um JSON-Dokumente in Cosmos DB zu vereinfachen. In manchen Fällen sollten Sie das Datenmodell umgestalten, um es für Anwendungsfälle außerhalb von SQL zu optimieren. Weitere Informationen dazu finden Sie im Artikel [Datenmodellierung in Azure Cosmos DB](../cosmos-db/modeling-data.md). Sie können die Daten beispielsweise denormalisieren, indem Sie alle verbundenen untergeordneten Elemente innerhalb eines JSON-Dokuments einbetten. [In diesem Artikel](../cosmos-db/migrate-relational-to-cosmos-db-sql-api.md) finden Sie weitere Informationen für so einen Fall sowie eine exemplarische Vorgehensweise, wie Sie dieses Szenario mithilfe der Azure Data Factory-Kopieraktivität umsetzen.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität als Quellen und Senken in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).