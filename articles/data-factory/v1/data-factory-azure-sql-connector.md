---
title: Kopieren von Daten in und aus Azure SQL-Datenbank
description: Erfahren Sie, wie Daten mithilfe von Azure Data Factory in und aus Azure SQL-Datenbank kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 83ab9e212e71ad53007a84ad8c10979bfea4516b
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637393"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Kopieren von Daten in und aus Azure SQL-Datenbank mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-azure-sql-connector.md)
> * [Version 2 (aktuelle Version)](../connector-azure-sql-database.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Azure SQL-Datenbank-Connector in V2](../connector-azure-sql-database.md).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in und aus der Azure SQL-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

## <a name="supported-scenarios"></a>Unterstützte Szenarios
Sie können Daten **aus der Azure SQL-Datenbank** in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern **in der Azure SQL-Datenbank** kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>Unterstützter Authentifizierungstyp
Der Azure SQL-Datenbank-Connector unterstützt Standardauthentifizierung.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs in und aus einer Azure SQL-Datenbank verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten** . Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager-Vorlage** , **.NET-API** und **REST-API** . Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory** . Eine Data Factory kann eine oder mehrere Pipelines enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus Azure Blob Storage in Azure SQL-Datenbank kopieren, erstellen Sie zwei verknüpfte Dienste, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für die Azure SQL-Datenbank sind, finden Sie im Abschnitt [Eigenschaften des verknüpften Dienstes](#linked-service-properties).
3. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im letzten Schritt erwähnt wurde, erstellen Sie ein Dataset, um den Blobcontainer und den Ordner mit den Eingabedaten anzugeben. Außerdem erstellen Sie ein weiteres Dataset zum Angeben der SQL-Tabelle in Azure SQL-Datenbank, in der die aus dem Blobspeicher kopierten Daten enthalten sind. Informationen zu Dataset-Eigenschaften, die spezifisch für Azure Data Lake Store sind, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).
4. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im oben erwähnten Beispiel verwenden Sie „BlobSource“ als Quelle und „SqlSink“ als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang von Azure SQL-Datenbank zu Azure Blob Storage durchführen, verwenden Sie entsprechend SqlSource und BlobSink in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für die Azure SQL-Datenbank sind, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties). Ausführliche Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke erhalten Sie, indem Sie im vorherigen Abschnitt auf den Link für Ihren Datenspeicher klicken.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in und aus Azure SQL-Datenbank finden Sie in diesem Artikel im Abschnitt [JSON-Beispiele](#json-examples-for-copying-data-to-and-from-sql-database).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Azure SQL-Datenbank verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Ein verknüpfter Azure SQL-Dienst verknüpft Azure SQL-Datenbank mit Ihrer Data Factory. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure SQL verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureSqlDatabase** . |Ja |
| connectionString |Geben Sie Informationen, die zur Verbindung mit der Azure SQL-Datenbankinstanz erforderlich sind, für die Eigenschaft „connectionString“ ein. Es wird nur Standardauthentifizierung unterstützt. |Ja |

> [!IMPORTANT]
> Konfigurieren Sie die [Azure SQL-Datenbank-Firewall](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) und den Datenbankserver, um [Azure-Diensten den Zugriff auf den Server zu ermöglichen](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Gehen Sie wie folgt vor, wenn Sie nicht aus Azure stammende Daten nach Azure SQL-Datenbank kopieren, inklusive Daten aus lokalen Datenquellen mit Data Factory-Gateway: Konfigurieren Sie den entsprechenden IP-Adressbereich für den Computer, der Daten an Azure SQL-Datenbank sendet.

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Um ein Dataset zur Darstellung von Eingabe- oder Ausgabedaten in Azure SQL-Datenbank anzugeben, legen Sie die Typeigenschaft des Datasets auf **AzureSqlTable** fest. Legen Sie die **linkedServiceName** -Eigenschaft des Datasets auf den Namen des mit Azure SQL verknüpften Diensts fest.

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset des Typs **AzureSqlTable** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle oder Sicht in der Azure SQL-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Ja |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

> [!NOTE]
> Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn Sie Daten aus Azure SQL-Datenbank verschieben, legen Sie den Quelltyp in der Kopieraktivität auf **SqlSource** fest. Wenn Sie Daten in Azure SQL-Datenbank verschieben, legen Sie den Senkentyp in der Kopieraktivität auf **SqlSink** fest. Dieser Abschnitt enthält eine Liste der Eigenschaften, die von „SqlSource“ und „SqlSink“ unterstützt werden.

### <a name="sqlsource"></a>SqlSource
Wenn die Quelle bei der Kopieraktivität den Typ **SqlSource** hat, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: `select * from MyTable`. |Nein |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. |Name der gespeicherten Prozedur. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |

Wenn **sqlReaderQuery** für SqlSource angegeben ist, führt die Kopieraktivität diese Abfrage für die Azure SQL-Quelldatenbank aus, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Wenn Sie sqlReaderQuery oder sqlReaderStoredProcedureName nicht angeben, werden die im Strukturabschnitt des Dataset-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage (`select column1, column2 from mytable`) zur Ausführung für Azure SQL-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

> [!NOTE]
> Bei Verwendung von **sqlReaderStoredProcedureName** müssen Sie trotzdem einen Wert für die **tableName** -Eigenschaft in der Dataset-JSON angeben. Es finden jedoch keine Überprüfungen dieser Tabelle statt.
>
>

### <a name="sqlsource-example"></a>Beispiel für SqlSource

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**Die Definition der gespeicherten Prozedur:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>SqlSink
**SqlSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| sqlWriterCleanupScript |Geben Sie eine Abfrage für die Kopieraktivität an, bei deren Ausführung die Daten eines bestimmten Slice bereinigt werden. Weitere Informationen finden Sie unter [Wiederholbare Kopiervorgänge](#repeatable-copy). |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Geben Sie einen Spaltennamen an, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen der Daten eines bestimmten Slice verwendet wird. Weitere Informationen finden Sie unter [Wiederholbare Kopiervorgänge](#repeatable-copy). |Spaltenname einer Spalte mit binärem Datentyp (32). |Nein |
| sqlWriterStoredProcedureName |Der Name der gespeicherten Prozedur, die definiert, wie Quelldaten in der Zieltabelle angewendet werden (etwa durch Ausführen von Upserts oder Transformationen mit eigener Geschäftslogik). <br/><br/>Beachten Sie, dass diese gespeicherte Prozedur **pro Batch aufgerufen wird** . Verwenden Sie bei einem Vorgang, der nur einmal ausgeführt wird und nicht mit Quelldaten in Zusammenhang steht (etwa Löschen/Kürzen), die `sqlWriterCleanupScript`-Eigenschaft. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |
| sqlWriterTableType |Geben Sie einen Tabellentypnamen an, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Ein Tabellentypname. |Nein |

#### <a name="sqlsink-example"></a>Beispiel für SqlSink

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>JSON-Beispiele zum Kopieren von Daten in die bzw. aus der SQL-Datenbank
Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Sie zeigen Ihnen das Kopieren von Daten in und aus Azure SQL-Datenbank und Azure Blob Storage. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>Beispiel: Kopieren von Daten aus Azure SQL-Datenbank in ein Azure-Blob
Das Beispiel enthält die folgenden Data Factory-Entitäten:

1. Einen verknüpften Dienst des Typs [AzureSqlDatabase](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) vom Typ [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Eine [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [SqlSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet.

Im Beispiel werden Zeitreihendaten (stündlich, täglich usw.) aus einer Tabelle in Azure SQL-Datenbank stündlich in ein Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL-Datenbank verknüpfter Dienst:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Im Abschnitt „Mit Azure SQL verknüpfter Dienst“ finden Sie eine Liste der von diesem verknüpften Dienst unterstützten Eigenschaften.

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
Der Artikel [Azure-Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) enthält eine Liste mit den Eigenschaften, die von diesem verknüpften Dienst unterstützt werden.


**Azure SQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Azure SQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch das Festlegen von „external“ auf „true“ wird dem Azure Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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

Im Abschnitt „Eigenschaften des Dataset-Typs ‚Azure SQL‘“ finden Sie eine Liste der von diesem Dataset-Typ unterstützten Eigenschaften.

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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
Eine Liste mit den Eigenschaften, die von diesem Dataset-Typ unterstützt werden, finden Sie im Abschnitt [Eigenschaften des Dataset-Typs „Azure-Blob“](data-factory-azure-blob-connector.md#dataset-properties) .

**Eine Kopieraktivität in einer Pipeline mit einer SQL-Quelle und einer Blobsenke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery** -Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
Im Beispiel ist **sqlReaderQuery** für SqlSource angegeben. Mit der Kopieraktivität wird diese Abfrage für die Azure SQL-Quelldatenbank ausgeführt, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Ohne Angabe von „sqlReaderQuery“ oder „sqlReaderStoredProcedureName“ werden die im Strukturabschnitt des DataSet-JSON-Bereichs definierten Spalten verwendet, um eine Abfrage für Azure SQL-Datenbank zu erstellen. Beispiel: `select column1, column2 from mytable`. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

Eine Liste mit den Eigenschaften, die von SqlSource und BlobSink unterstützt werden, finden Sie im Abschnitt [Sql Source](#sqlsource) und unter [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure SQL-Datenbank
Im Beispiel werden folgende Data Factory-Entitäten definiert:

1. Einen verknüpften Dienst des Typs [AzureSqlDatabase](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) und [SqlSink](#copy-activity-properties) verwendet

In dem Beispiel werden jede Stunde Zeitreihendaten (stündlich, täglich usw.) aus einem Azure-Blob in eine Tabelle in Azure SQL-Datenbank kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL verknüpfter Dienst:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
Im Abschnitt „Mit Azure SQL verknüpfter Dienst“ finden Sie eine Liste der von diesem verknüpften Dienst unterstützten Eigenschaften.

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
Der Artikel [Azure-Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) enthält eine Liste mit den Eigenschaften, die von diesem verknüpften Dienst unterstützt werden.


**Azure-Blob-Eingabedataset:**

Daten werden stündlich aus einem neuen Blob übernommen (frequency: hour, interval: 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von „external“ auf „true“ teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
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
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
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
Eine Liste mit den Eigenschaften, die von diesem Dataset-Typ unterstützt werden, finden Sie im Abschnitt [Eigenschaften des Dataset-Typs „Azure-Blob“](data-factory-azure-blob-connector.md#dataset-properties) .

**Azure SQL-Datenbank-Ausgabedataset:**

Das Beispiel kopiert Daten in eine Tabelle namens "MyTable" in Azure SQL. Erstellen Sie die Tabelle in Azure SQL mit der gleichen Anzahl von Spalten, die Sie in der Blob-CSV-Datei erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Im Abschnitt „Eigenschaften des Dataset-Typs ‚Azure SQL‘“ finden Sie eine Liste der von diesem Dataset-Typ unterstützten Eigenschaften.

**Eine Kopieraktivität in einer Pipeline mit Blobquelle und SQL-Senke:**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
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
Eine Liste mit den Eigenschaften, die von SqlSink und BlobSource unterstützt werden, finden Sie im Abschnitt [Sql Sink](#sqlsink) und unter [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties).

## <a name="identity-columns-in-the-target-database"></a>Identitätsspalten in der Zieldatenbank
Dieser Abschnitt enthält ein Beispiel zum Kopieren von Daten aus einer Quelltabelle ohne eine Identitätsspalte in eine Zieltabelle mit einer Identitätsspalte.

**Quelltabelle:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Zieltabelle:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
Beachten Sie, dass die Zieltabelle über eine Identitätsspalte verfügt.

**Definition der Quell-Dataset-JSON**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**Definition der Ziel-Dataset-JSON**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

Beachten Sie, dass die Quell- und die Zieltabelle unterschiedliche Schemas besitzen (das Ziel verfügt über eine zusätzliche Spalte mit der Identität). In diesem Szenario müssen Sie eine **structure** -Eigenschaft in der Definition des Zieldatasets angeben, die nicht die Identitätsspalte enthält.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Aufrufen der gespeicherten Prozedur von der SQL-Senke
Ein Beispiel für den Aufruf einer gespeicherten Prozedur von der SQL-Senke in einer Kopieraktivität einer Pipeline finden Sie im Artikel [Aufrufen der gespeicherten Prozedur für die SQL-Senke in einer Kopieraktivität](data-factory-invoke-stored-procedure-from-copy-activity.md).

## <a name="type-mapping-for-azure-sql-database"></a>Typzuordnung für Azure SQL-Datenbank
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität mithilfe der folgenden beiden Schritte automatische Typkonvertierungen von Quelltypen in Senkentypen durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in und aus Azure SQL-Datenbank werden die folgenden Zuordnungen zwischen SQL-Typ und .NET-Typ und umgekehrt verwendet. Die Zuordnung ist mit der SQL Server-Datentypzuordnung für ADO.NET identisch.

| Typ „SQL Server-Datenbank-Engine“ | .NET Framework-Typ |
| --- | --- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Wiederholbare Kopiervorgänge
Beim Kopieren von Daten in SQL Server-Datenbank fügt die Kopieraktivität standardmäßig Daten an die Senkentabelle an. Wenn Sie stattdessen ein UPSERT (aktualisieren und einfügen) durchführen möchten, lesen Sie den Artikel [Wiederholbare Schreibvorgänge in SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink).

Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.