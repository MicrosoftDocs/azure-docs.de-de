---
title: Verschieben von Daten in und aus SQL Server
description: Informationen zum Verschieben von Daten in und aus einer SQL Server-Datenbank, die lokal oder mithilfe von Azure Data Factory in einer Azure-VM gehostet wird.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 864ece28-93b5-4309-9873-b095bbe6fedd
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b2d69f9f70861799d941bbeaed7eb8d338fa8a5e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636169"
---
# <a name="move-data-to-and-from-sql-server-using-azure-data-factory"></a>Verschieben von Daten in und aus SQL Server mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-sqlserver-connector.md)
> * [Version 2 (aktuelle Version)](../connector-sql-server.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [SQL Server-Connector in V2](../connector-sql-server.md).

Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in und aus einer SQL Server-Datenbank zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Unterstützte Szenarios
Sie können Daten **aus einer SQL Server-Datenbank** in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern **in eine SQL Server-Datenbank** kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-sql-server-versions"></a>Unterstützte SQL Server-Versionen
Dieser SQL Server-Connector unterstützt das Kopieren von Daten von den bzw. in die folgenden Versionen der lokal oder in Azure IaaS gehosteten Instanz mit SQL-Authentifizierung und Windows-Authentifizierung: SQL Server 2016, SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008, SQL Server 2005.

## <a name="enabling-connectivity"></a>Herstellen der Verbindung
Die Konzepte und Schritte zum Herstellen einer Verbindung mit SQL Server, ob lokal oder in Azure-IaaS-VMs (Infrastructure-as-a-Service) gehostet, sind identisch. In beiden Fällen müssen Sie das Datenverwaltungsgateway zum Herstellen der Verbindung nutzen.

Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways. Das Einrichten einer Gatewayinstanz ist eine Voraussetzung für die Verbindung mit SQL Server.

Wenngleich Sie das Gateway auf demselben lokalen Computer oder virtuellen Cloudcomputer wie SQL Server installieren können, wird aus Leistungsgründen empfohlen, diese Komponenten auf getrennten Computern zu installieren. Wenn sich Gateway und SQL Server auf getrennten Computern befinden, werden Ressourcenkonflikte reduziert.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs in und aus einer SQL Server-Datenbank verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten** . Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager-Vorlage** , **.NET-API** und **REST-API** . Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory** . Eine Data Factory kann eine oder mehrere Pipelines enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus einer SQL Server-Datenbank in Azure Blob Storage kopieren, erstellen Sie zwei verknüpfte Dienste, um Ihre SQL Server-Datenbank und Ihr Azure-Speicherkonto mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für eine SQL Server-Datenbank sind, finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
3. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im letzten Schritt erwähnt wurde, erstellen Sie ein Dataset, um die SQL-Tabelle mit den Eingabedaten in Ihrer SQL Server-Datenbank anzugeben. Außerdem erstellen Sie ein weiteres Dataset zum Angeben eines Blobcontainers und des Ordners, in dem die aus der SQL Server-Datenbank kopierten Daten enthalten sind. Informationen zu Dataset-Eigenschaften, die spezifisch für die SQL Server-Datenbank sind, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).
4. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im oben erwähnten Beispiel verwenden Sie „SqlSource“ als Quelle und „BlobSink“ als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang von Azure Blob Storage zur SQL Server-Datenbank durchführen, verwenden Sie entsprechend „BlobSource“ und „SqlSink“ in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für die SQL Server-Datenbank sind, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties). Ausführliche Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke erhalten Sie, indem Sie im vorherigen Abschnitt auf den Link für Ihren Datenspeicher klicken.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in und aus einer SQL Server-Datenbank finden Sie in diesem Artikel im Abschnitt [JSON-Beispiele](#json-examples-for-copying-data-from-and-to-sql-server).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für SQL Server verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Sie erstellen einen verknüpften Dienst des Typs **OnPremisesSqlServer** , um eine SQL Server-Datenbank mit einer Data Factory zu verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit SQL Server verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Legen Sie die type-Eigenschaft auf **OnPremisesSqlServer** fest. |Ja |
| connectionString |Geben Sie „connectionString“-Informationen an, die zum Herstellen einer Verbindung mit der SQL Server-Datenbank mithilfe der SQL- oder Windows-Authentifizierung benötigt werden. |Ja |
| gatewayName |Name des Gateways, das der Data Factory-Dienst zum Herstellen einer Verbindung mit der SQL Server-Datenbank verwenden soll. |Ja |
| username |Geben Sie den Benutzernamen an, wenn Sie die Windows-Authentifizierung verwenden. Beispiel: **Domainname\\username** . |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. |Nein |

Sie können Anmeldeinformationen mithilfe des Cmdlets **New-AzDataFactoryEncryptValue** verschlüsseln und wie im folgenden Beispiel gezeigt in der Verbindungszeichenfolge verwenden ( **EncryptedCredential** -Eigenschaft):

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

### <a name="samples"></a>Beispiele
**JSON für die SQL Server-Authentifizierung**

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
**JSON für die Windows-Authentifizierung**

Das Datenverwaltungsgateway übernimmt die Identität des angegebenen Benutzerkontos und stellt eine Verbindung mit der SQL Server-Datenbank her.

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties":
    {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
In den Beispielen haben Sie ein Dataset des Typs **SqlServerTable** verwendet, um eine Tabelle in einer SQL Server-Datenbank darzustellen.

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ der JSON eines Datasets sind bei allen Arten von Datasets (SQL Server, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt **typeProperties** für ein Dataset des Typs **SqlServerTable** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Name der Tabelle oder Ansicht in der SQL Server-Datenbankinstanz, auf die der verknüpfte Dienst verweist. |Ja |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Wenn Sie Daten aus einer SQL Server-Datenbank verschieben, legen Sie den Quelltyp in der Kopieraktivität auf **SqlSource** fest. Wenn Sie Daten in eine SQL Server-Datenbank verschieben, legen Sie den Senkentyp in der Kopieraktivität auf **SqlSink** fest. Dieser Abschnitt enthält eine Liste der Eigenschaften, die von „SqlSource“ und „SqlSink“ unterstützt werden.

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

> [!NOTE]
> Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Eigenschaften im Abschnitt typeProperties der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

### <a name="sqlsource"></a>SqlSource
Wenn bei einer Kopieraktivität die Quelle den Typ **SqlSource** aufweist, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| sqlReaderQuery |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. Kann auf mehrere Tabellen aus der Datenbank verweisen, auf die vom Eingabedataset verwiesen wird. Falls nicht angegeben, wird folgende SQL-Anweisung ausgeführt: "select from MyTable". |Nein |
| sqlReaderStoredProcedureName |Der Name der gespeicherten Prozedur, die Daten aus der Quelltabelle liest. |Name der gespeicherten Prozedur. Die letzte SQL-Anweisung muss eine SELECT-Anweisung in der gespeicherten Prozedur sein. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |

Wenn **sqlReaderQuery** für SqlSource angegeben ist, führt die Kopieraktivität diese Abfrage in der SQL Server-Datenbankquelle aus, um die Daten abzurufen.

Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden).

Wenn Sie weder sqlReaderQuery noch sqlReaderStoredProcedureName angeben, werden die im Strukturabschnitt definierten Spalten verwendet, um eine SELECT-Abfrage zur Ausführung in der SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

> [!NOTE]
> Bei Verwendung von **sqlReaderStoredProcedureName** müssen Sie trotzdem einen Wert für die **tableName** -Eigenschaft in der Dataset-JSON angeben. Es finden jedoch keine Überprüfungen dieser Tabelle statt.

### <a name="sqlsink"></a>SqlSink
**SqlSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| writeBatchTimeout |Die Wartezeit für den Abschluss der Batcheinfügung, bis das Timeout wirksam wird. |Zeitraum<br/><br/> Beispiel: „00:30:00“ (30 Minuten). |Nein |
| writeBatchSize |Fügt Daten in die SQL-Tabelle ein, wenn die Puffergröße "writeBatchSize" erreicht. |Integer (Gesamtanzahl von Zeilen) |Nein (Standardwert: 10.000) |
| sqlWriterCleanupScript |Geben Sie die Abfrage für die Kopieraktivität so an, dass bei Ausführung die Daten eines bestimmten Slices bereinigt werden. Weitere Informationen finden Sie im Abschnitt [Wiederholbare Kopiervorgänge](#repeatable-copy). |Eine Abfrageanweisung. |Nein |
| sliceIdentifierColumnName |Geben Sie einen Spaltennamen an, den die Kopieraktivität mit einem automatisch generierten Slicebezeichner füllen soll, der bei erneuter Ausführung zum Bereinigen der Daten eines bestimmten Slices verwendet wird. Weitere Informationen finden Sie im Abschnitt [Wiederholbare Kopiervorgänge](#repeatable-copy). |Spaltenname einer Spalte mit binärem Datentyp (32). |Nein |
| sqlWriterStoredProcedureName |Der Name der gespeicherten Prozedur, die definiert, wie Quelldaten in der Zieltabelle angewendet werden (etwa durch Ausführen von Upserts oder Transformationen mit eigener Geschäftslogik). <br/><br/>Beachten Sie, dass diese gespeicherte Prozedur **pro Batch aufgerufen wird** . Verwenden Sie bei einem Vorgang, der nur einmal ausgeführt wird und nicht mit Quelldaten in Zusammenhang steht (etwa Löschen/Kürzen), die `sqlWriterCleanupScript`-Eigenschaft. |Name der gespeicherten Prozedur. |Nein |
| storedProcedureParameters |Parameter für die gespeicherte Prozedur. |Name-Wert-Paare. Die Namen und die Groß-/Kleinschreibung von Parametern müssen denen der Parameter der gespeicherten Prozedur entsprechen. |Nein |
| sqlWriterTableType |Geben Sie einen Tabellentypnamen an, der in der gespeicherten Prozedur verwendet werden soll. Die Kopieraktivität macht die verschobenen Daten in einer temporären Tabelle mit diesem Tabellentyp verfügbar. Der gespeicherte Prozedurcode kann dann die kopierten Daten mit vorhandenen Daten zusammenführen. |Ein Tabellentypname. |Nein |


## <a name="json-examples-for-copying-data-from-and-to-sql-server"></a>JSON-Beispiele zum Kopieren von Daten aus und in SQL Server
Die folgenden Beispiele zeigen JSON-Beispieldefinitionen, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. In den folgenden Beispielen wird veranschaulicht, wie Sie Daten in und aus SQL Server und Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

## <a name="example-copy-data-from-sql-server-to-azure-blob"></a>Beispiel: Kopieren von Daten aus SQL Server in ein Azure-Blob
Dieses Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [OnPremisesSqlServer](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [SqlServerTable](#dataset-properties).
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden Zeitreihendaten aus einer SQL Server-Tabelle stündlich in einen Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als Erstes richten Sie das Datenverwaltungsgateway ein. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Mit SQL Server verknüpfter Dienst**
```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**SQL Server-Eingabedataset**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in SQL Server erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält. Sie können mehrere Tabellen in derselben Datenbank mithilfe eines einzigen Datasets abfragen, aber für die typeProperty-Eigenschaft tableName des Datasets muss eine einzelne Tabelle verwendet werden.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
  "name": "SqlServerInput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery** -Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2016-06-01T18:00:00",
    "end":"2016-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
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
In diesem Beispiel ist **sqlReaderQuery** für SqlSource angegeben. Mit der Kopieraktivität wird diese Abfrage in der SQL Server-Datenbankquelle ausgeführt, um die Daten abzurufen. Alternativ dazu können Sie eine gespeicherte Prozedur angeben, indem Sie **sqlReaderStoredProcedureName** und **storedProcedureParameters** angeben (sofern die gespeicherten Prozeduren Parameter verwenden). sqlReaderQuery kann auf mehrere Tabellen in der Datenbank verweisen, die vom Eingabedataset referenziert wird. Die Eigenschaft ist im Gegensatz zur typeProperty-Eigenschaft tableName des Datasets nicht auf den Tabellensatz beschränkt.

Wenn Sie sqlReaderQuery oder sqlReaderStoredProcedureName nicht angeben, werden die im Strukturabschnitt definierten Spalten verwendet, um eine SELECT-Abfrage zur Ausführung in der SQL Server-Datenbank zu erstellen. Falls die DataSet-Definition nicht über die Struktur verfügt, werden alle Spalten der Tabelle ausgewählt.

Eine Liste mit den Eigenschaften, die von SqlSource und BlobSink unterstützt werden, finden Sie im Abschnitt [Sql Source](#sqlsource) und unter [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

## <a name="example-copy-data-from-azure-blob-to-sql-server"></a>Beispiel: Kopieren von Daten aus einem Azure-Blob in SQL Server
Dieses Beispiel zeigt Folgendes:

1. Den verknüpften Dienst des Typs [OnPremisesSqlServer](#linked-service-properties)
2. Den verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties)
5. Die [Pipeline](data-factory-create-pipelines.md) mit einer Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) und SqlSink verwendet.

Im Beispiel werden Zeitreihendaten aus einem Azure-Blob stündlich in eine SQL Server-Tabelle kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit SQL Server verknüpfter Dienst**

```json
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
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
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure-Blob-Eingabedataset**

Daten werden stündlich aus einem neuen Blob übernommen (frequency: hour, interval: 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von „external“ auf „true“ teilt dem Data Factory-Dienst mit, dass dieses Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**SQL Server-Ausgabedataset**

Das Beispiel kopiert Daten in eine Tabelle namens "MyTable" in SQL Server. Erstellen Sie die Tabelle in SQL Server mit der gleichen Anzahl von Spalten, die Sie in der CSV-Datei im Blob erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

```json
{
  "name": "SqlServerOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
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
**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

```json
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
            "name": " SqlServerOutput "
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

## <a name="troubleshooting-connection-issues"></a>Beheben von Verbindungsproblemen
1. Konfigurieren Sie Ihren SQL-Server, um Remoteverbindungen zu akzeptieren. Starten Sie **SQL Server Management Studio** , klicken Sie mit der rechten Maustaste auf **Server** , und klicken Sie dann auf **Eigenschaften** . Wählen Sie in der Liste den Eintrag **Verbindungen** aus, und aktivieren Sie das Kontrollkästchen **Remoteverbindungen mit diesem Server zulassen** .

    ![Aktivieren von Remoteverbindungen](./media/data-factory-sqlserver-connector/AllowRemoteConnections.png)

    Ausführliche Schritte finden Sie im Abschnitt zum [Konfigurieren der Serverkonfigurationsoption für den Remotezugriff](/sql/database-engine/configure-windows/configure-the-remote-access-server-configuration-option) .
2. Starten Sie den **SQL Server-Konfigurations-Manager** . Erweitern Sie **SQL Server-Netzwerkkonfiguration** für die gewünschte Instanz, und wählen Sie **Protokolle für MSSQLSERVER** aus. Im rechten Bereich werden Protokolle angezeigt. Aktivieren Sie TCP/IP, indem Sie mit der rechten Maustaste auf **TCP/IP** klicken und anschließend auf **Aktivieren** klicken.

    ![Aktivieren von TCP/IP](./media/data-factory-sqlserver-connector/EnableTCPProptocol.png)

    Weitere Methoden zum Aktivieren des TCP/IP-Protokolls finden Sie unter [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol) .
3. Doppelklicken Sie im gleichen Fenster auf **TCP/IP** , um das Fenster **TCP/IP-Eigenschaften** zu öffnen.
4. Wechseln Sie zur Registerkarte **IP-Adressen** . Scrollen Sie nach unten zum Abschnitt **IPAll** . Notieren Sie sich den **TCP-Port** (Standardwert ist **1433** ).
5. Erstellen Sie auf dem Computer eine **Regel für die Windows-Firewall** , um eingehenden Datenverkehr über diesen Port zuzulassen.
6. **Überprüfen der Verbindung** : Verwenden Sie SQL Server Management Studio auf einem anderen Computer, um mit dem vollqualifizierten Namen eine Verbindung mit der SQL Server-Instanz herzustellen. Beispiel: "\<machine\>\<domain\>.corp\<company\>.com,1433."

   > [!IMPORTANT]
   > 
   > Ausführliche Informationen finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud mit dem Datenverwaltungsgateway](data-factory-move-data-between-onprem-and-cloud.md).
   > 
   > Unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.


## <a name="identity-columns-in-the-target-database"></a>Identitätsspalten in der Zieldatenbank
Dieser Abschnitt enthält ein Beispiel zum Kopieren von Daten aus einer Quelltabelle ohne eine Identitätsspalte in eine Zieltabelle mit einer Identitätsspalte.

**Quelltabelle:**

```sql
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**Zieltabelle:**

```sql
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```

Beachten Sie, dass die Zieltabelle über eine Identitätsspalte verfügt.

**Definition der Quell-Dataset-JSON**

```json
{
    "name": "SampleSource",
    "properties": {
        "published": false,
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

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "published": false,
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
Im Artikel [Aufrufen der gespeicherten Prozedur für die SQL-Senke in einer Kopieraktivität](data-factory-invoke-stored-procedure-from-copy-activity.md) finden Sie ein Beispiel für den Aufruf einer gespeicherten Prozedur von der SQL-Senke in einer Kopieraktivität einer Pipeline.

## <a name="type-mapping-for-sql-server"></a>Typzuordnung für SQL Server
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten in und aus SQL werden die folgenden Zuordnungen zwischen SQL-Typ und .NET-Typ und umgekehrt verwendet.

Die Zuordnung ist mit der SQL Server-Datentypzuordnung für ADO.NET identisch.

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

## <a name="mapping-source-to-sink-columns"></a>Zuordnen von Quellspalten zu Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-copy"></a>Wiederholbare Kopiervorgänge
Beim Kopieren von Daten in SQL Server-Datenbank fügt die Kopieraktivität standardmäßig Daten an die Senkentabelle an. Wenn Sie stattdessen einen UPSERT-Vorgang ausführen möchten, lesen Sie den Artikel [Wiederholbare Schreibvorgänge in SqlSink](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink).

Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.