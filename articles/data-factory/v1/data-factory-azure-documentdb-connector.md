---
title: Verschieben von Daten nach/aus Azure Cosmos DB
description: Erfahren Sie, wie Daten mithilfe von Azure Data Factory in eine bzw. aus einer Azure Cosmos DB-Sammlung verschoben werden.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 91f2b338ac9cda96521a5fe1d555de054826e273
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637631"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Verschieben von Daten nach und aus Azure Cosmos DB mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-azure-documentdb-connector.md)
> * [Version 2 (aktuelle Version)](../connector-azure-cosmos-db.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Azure Cosmos DB-Connector in V2](../connector-azure-cosmos-db.md).

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten nach und aus Azure Cosmos DB (SQL-API) zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einem beliebigen unterstützten Quelldatenspeicher nach Azure Cosmos DB oder aus Azure Cosmos DB in einen beliebigen unterstützten Senkendatenspeicher verschieben. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats).

> [!IMPORTANT]
> Der Azure Cosmos DB-Connector unterstützt nur die SQL-API.

Informationen zum Kopieren von Daten in bzw. aus JSON-Dateien oder eine andere Cosmos DB-Sammlung finden Sie unter [Importieren oder Exportieren von JSON-Dokumenten](#importexport-json-documents).

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs nach und aus Azure Cosmos DB verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten** . Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager-Vorlage** , **.NET-API** und **REST-API** . Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in und aus Cosmos DB finden Sie in diesem Artikel im Abschnitt [JSON-Beispiele](#json-examples).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren spezifischer Data Factory-Entitäten für Cosmos DB verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure Cosmos DB verknüpften Dienst spezifisch sind.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **DocumentDb** . |Ja |
| connectionString |Geben Sie die zum Verbinden mit der Azure Cosmos DB-Datenbank erforderlichen Informationen an. |Ja |

Beispiel:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ einer Dataset-JSON sind bei allen Datasettypen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset des Typs **DocumentDbCollection** hat die folgenden Eigenschaften.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| collectionName |Name der Cosmos DB-Dokumentsammlung |Ja |

Beispiel:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Schema per Data Factory
Bei schemafreien Datenspeichern, z.B. Azure Cosmos DB, leitet der Data Factory-Dienst das Schema auf eine der folgenden Weisen ab:

1. Wenn Sie die Struktur der Daten mithilfe der **structure** -Eigenschaft in der Datasetdefinition angeben, berücksichtigt der Data Factory-Dienst diese Struktur als das Schema. Wenn in diesem Fall eine Zeile keinen Wert für eine Spalte enthält, wird ein NULL-Wert für sie angegeben.
2. Wenn Sie die Struktur der Daten nicht mithilfe der **structure** -Eigenschaft in der Datasetdefinition angeben, leitet der Data Factory-Dienst das Schema unter Verwendung der ersten Zeile in den Daten ab. Wenn in diesem Fall die erste Zeile nicht das vollständige Schema enthält, fehlen im Ergebnis des Kopiervorgangs einige Spalten.

Daher empfiehlt es sich bei schemafreien Datenquellen, die Struktur der Daten mithilfe der **structure** -Eigenschaft anzugeben.

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

> [!NOTE]
> Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn „source“ bei der Kopieraktivität den Typ **DocumentDbCollectionSource** hat, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| Abfrage |Geben Sie die Abfrage an, um Daten zu lesen. |Von Azure Cosmos DB unterstützte Abfragezeichenfolge. <br/><br/>Beispiel: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |Nein <br/><br/>Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Sonderzeichen, um anzugeben, dass das Dokument geschachtelt ist. |Beliebiges Zeichen. <br/><br/>Azure Cosmos DB ist ein NoSQL-Speicher für JSON-Dokumente, in denen geschachtelte Strukturen zulässig sind. Azure Data Factory ermöglicht es dem Benutzer, über einen „nestingSeparator“ eine Hierarchie anzugeben. In den obigen Beispielen ist dies „.“. Mit dem Trennzeichen generiert die Kopieraktivität das Objekt "Name" mit den drei untergeordneten Elementen "First", "Middle" und "Last" gemäß "Name.First", "Name.Middle" und "Name.Last" in der Tabellendefinition. |Nein |

**DocumentDbCollectionSink** unterstützt die folgenden Eigenschaften:

| **Eigenschaft** | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| nestingSeparator |Ein Sonderzeichen im Quellspaltennamen, um anzuzeigen, dass das geschachtelte Dokument erforderlich ist. <br/><br/>Für das obige Beispiel gilt Folgendes: `Name.First` in der Ausgabetabelle erzeugt im Cosmos DB-Dokument die folgende JSON-Struktur:<br/><br/>"Name": {<br/>    "First": „John“<br/>}, |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird.<br/><br/>Standardwert ist `.` (Punkt). |Zeichen, das zur Trennung der Schachtelungsebenen verwendet wird. <br/><br/>Standardwert ist `.` (Punkt). |
| writeBatchSize |Gibt die Anzahl von parallelen Anforderungen an den Azure Cosmos DB-Dienst zum Erstellen von Dokumenten an.<br/><br/>Sie können die Leistung beim Kopieren von Daten nach bzw. aus Cosmos DB mit dieser Eigenschaft optimieren. Sie können eine bessere Leistung erzielen, wenn Sie „writeBatchSize“ heraufsetzen, da mehr parallele Anforderungen an Cosmos DB gesendet werden. Sie sollten aber eine Drosselung vermeiden, da dies zur Ausgabe einer Fehlermeldung führen kann: „Anforderungsrate ist hoch.“<br/><br/>Die Drosselung hängt von einer Reihe von Faktoren ab, einschließlich Größe der Dokumente, Anzahl von Begriffen in Dokumenten, Indizierung der Richtlinie der Zielsammlung usw. Für Kopiervorgänge können Sie eine bessere Sammlung (z. B. S3) verwenden, um den optimalen verfügbaren Durchsatz zu erhalten (2.500 Anforderungseinheiten/Sekunde). |Integer |Nein (Standardwert: 5) |
| writeBatchTimeout |Die Wartezeit für den Abschluss des Vorgangs. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |

## <a name="importexport-json-documents"></a>Importieren oder Exportieren von JSON-Dokumenten
Mit diesem Cosmos DB-Connector können Sie Folgendes problemlos durchführen:

* JSON-Dokumente aus verschiedenen Quellen in Cosmos DB importieren, z.B. aus Azure Blob Storage, Azure Data Lake, einem lokalen Dateisystem oder anderen dateibasierten Speichern, die von Azure Data Factory unterstützt werden.
* JSON-Dokumente aus Cosmos DB-Sammlungen in verschiedene dateibasierte Speicher exportieren.
* Daten zwischen zwei Cosmos DB-Sammlungen unverändert migrieren.

Sie erhalten dann wie folgt eine vom Schema unabhängige Kopie:
* Aktivieren Sie bei Verwendung des Kopier-Assistenten die Option **Export as-is to JSON files or Cosmos DB collection** (Wie vorhanden in JSON-Dateien oder Cosmos DB-Sammlung exportieren).
* Geben Sie bei Verwendung der JSON-Bearbeitung auf keinen Fall weder den Abschnitt „Structure“ in Cosmos DB-Datasets noch die Eigenschaft „nestingSeparator“ für Cosmos DB-Quellen/-Senken in der Kopieraktivität an. Zum Importieren aus bzw. Exportieren in JSON-Dateien geben Sie im Dateispeicher-Dataset den Formattyp als „JsonFormat“ an. Konfigurieren Sie „filePattern“, und überspringen Sie die restlichen Formateinstellungen. Details finden Sie im Abschnitt [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format).

## <a name="json-examples"></a>JSON-Beispiele
Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Sie veranschaulichen das Kopieren von Daten nach und aus Azure Cosmos DB und Azure Blob Storage. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>Beispiel: Kopieren von Daten aus Azure Cosmos DB in ein Azure-Blob
Das nachstehende Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [DocumentDb](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [DocumentDbCollection](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [DocumentDbCollectionSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden die Daten aus Azure Cosmos DB in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure Cosmos DB verknüpfter Dienst:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Mit Azure Blob Storage verknüpfter Dienst:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure DocumentDB-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass eine Sammlung mit dem Namen **Person** in einer Azure Cosmos DB-Datenbank vorhanden ist.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Azure Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob kopiert, wobei der Pfad des Blobs jeweils Datum und Uhrzeit mit stündlicher Genauigkeit angibt.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
JSON-Beispieldokument in der Sammlung „Person“ in einer Cosmos DB-Datenbank:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB unterstützt das Abfragen von Dokumenten mittels einer SQL-ähnlichen Syntax über hierarchische JSON-Dokumente.

Beispiel:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

Die folgende Pipeline kopiert Daten aus der Sammlung „Person“ in der Azure Cosmos DB-Datenbank in ein Azure-Blob. Als Teil der Kopieraktivität wurden Eingabe- und Ausgabedataset angegeben.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure Cosmos DB
Das nachstehende Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs „DocumentDb“.
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs „DocumentDbCollection“.
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) und „DocumentDbCollectionSink“ verwendet.

Im Beispiel werden Daten aus dem Azure-Blob nach Azure Cosmos DB kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure Blob Storage verknüpfter Dienst:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Mit Azure Cosmos DB verknüpfter Dienst:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure-Blob-Eingabedataset:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB-Ausgabedataset:**

Im Beispiel werden Daten in eine Sammlung namens "Person" kopiert.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Die folgende Pipeline kopiert Daten aus dem Azure-Blob in die Sammlung „Person“ in Cosmos DB. Als Teil der Kopieraktivität wurden Eingabe- und Ausgabedataset angegeben.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
Wenn die Beispielblobeingabe folgendermaßen lautet:

```
1,John,,Doe
```
Die JSON-Ausgabe in Cosmos DB sieht dann wie folgt aus:

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB ist ein NoSQL-Speicher für JSON-Dokumente, in denen geschachtelte Strukturen zulässig sind. Azure Data Factory ermöglicht es dem Benutzer, über einen **nestingSeparator** eine Hierarchie anzugeben. In diesem Beispiel ist dies „.“. Mit dem Trennzeichen generiert die Kopieraktivität das Objekt "Name" mit den drei untergeordneten Elementen "First", "Middle" und "Last" gemäß "Name.First", "Name.Middle" und "Name.Last" in der Tabellendefinition.

## <a name="appendix"></a>Anhang
1. **Frage:** Unterstützt die Kopieraktivität das Aktualisieren von vorhandenen Datensätzen?

    **Antwort:** Nein.
2. **Frage:** Wie wird bei einer Wiederholung eines Kopiervorgangs nach Azure Cosmos DB mit bereits kopierten Datensätzen umgegangen?

    **Antwort:** Wenn Datensätze über ein ID-Feld verfügen und beim Kopiervorgang versucht wird, einen Datensatz mit der gleichen ID einzufügen, löst der Kopiervorgang einen Fehler aus.
3. **Frage:** Unterstützt Data Factory die [bereichs- oder hashbasierte Datenpartitionierung](../../cosmos-db/partitioning-overview.md)?

    **Antwort:** Nein.
4. **Frage:** Können mehrere Azure Cosmos DB-Sammlungen für eine Tabelle angegeben werden?

    **Antwort:** Nein. Zurzeit kann nur eine Auflistung angegeben werden.

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.