---
title: Data Factory – .NET-API-Änderungsprotokoll
description: Beschreibt Änderungen, hinzugefügte Features und Fehlerbehebungen usw. in einer bestimmten Version der .NET-API für Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 70df35409d1c84efb996bb40f4e39bde6ad7d5a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496499"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – .NET-API-Änderungsprotokoll
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. 

Dieser Artikel enthält Informationen zu Änderungen am Azure Data Factory SDK einer bestimmten Version. [Hier](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Version 4.11.0
Hinzugefügte Features:

* Die folgenden verknüpften Diensttypen wurden hinzugefügt:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Die folgenden Dataset-Typen wurden hinzugefügt:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Die folgenden Typen von Kopierquellen wurden hinzugefügt:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>Version 4.10.0
* Die folgenden optionalen Eigenschaften wurden zu TextFormat hinzugefügt:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Die folgenden verknüpften Diensttypen wurden hinzugefügt:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Die folgenden Dataset-Typen wurden hinzugefügt:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Die folgenden Typen von Kopierquellen wurden hinzugefügt:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Hinzufügen der Eigenschaft [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) zu AzureMLBatchExecutionActivity
  * Ermöglicht das Übergeben mehrerer Webdiensteingaben an ein Azure Machine Learning-Experiment

## <a name="version-491"></a>Version 4.9.1
### <a name="bug-fix"></a>Fehlerbehebung
* Ausmusterung der WebApi-basierten Authentifizierung für [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)

## <a name="version-490"></a>Version 4.9.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Hinzufügen der Eigenschaften [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) und [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) zu „CopyActivity“. Ausführliche Informationen zu diesem Feature finden Sie unter [Gestaffeltes Kopieren](data-factory-copy-activity-performance.md#staged-copy).

### <a name="bug-fix"></a>Fehlerbehebung
* Führen Sie eine Überladung der [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)-Methode hinzu, die eine [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters)-Instanz akzeptiert.
* Markieren Sie [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) und [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) in „CopySink“ als optional.

## <a name="version-480"></a>Version 4.8.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Dem Kopieraktivitätstyp wurden die folgenden optionalen Eigenschaften hinzugefügt, um die Optimierung der Kopierleistung zu ermöglichen:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>Version 4.7.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Ein neuer StorageFormat-Typ vom Typ [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) wurde hinzugefügt, um Dateien im einspaltig optimierten Zeilenformat (ORC) zu kopieren.
* Hinzufügen von [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) - und PolyBaseSettings-Eigenschaften zu „SqlDWSink“.
  * Ermöglicht das Kopieren von Daten in Azure Synapse Analytics mithilfe von PolyBase.

## <a name="version-461"></a>Version 4.6.1
### <a name="bug-fixes"></a>Fehlerbehebungen
* Repariert die HTTP-Anforderung zum Auflisten von Aktivitätsfenstern.
  * Entfernt den Ressourcengruppennamen und den Data Factory-Namen von der Anforderungsnutzlast.

## <a name="version-460"></a>Version 4.6.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Die folgenden Eigenschaften wurden zu [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)hinzugefügt:
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Datasets](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* Die folgenden Eigenschaften wurden zu [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)hinzugefügt:
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Ein neuer [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat)-Typ vom Typ [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) wurde hinzugefügt, um Datasets zu definieren, deren Daten im JSON-Format vorliegen.

## <a name="version-450"></a>Version 4.5.0
### <a name="feature-additions"></a>Hinzugefügte Features
* [Listenvorgänge für Aktivitätsfenster](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)wurden hinzugefügt.
  * Methoden zum Abrufen von Aktivitätsfenstern mit auf den Entitätstypen basierenden Filtern wurden hinzugefügt (d.h. Data Factorys, Datasets, Pipelines und Aktivitäten).
* Die folgenden verknüpften Diensttypen wurden hinzugefügt:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Die folgenden Dataset-Typen wurden hinzugefügt:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Die folgenden Typen von Kopierquellen wurden hinzugefügt:     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>Version 4.4.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Der folgende Typ für verknüpfte Dienste wurde als Datenquellen und Senken für Kopieraktivitäten hinzugefügt:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Grundlegende Informationen und Beispiele finden Sie unter [Mit Azure Storage SAS verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>Version 4.3.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Die folgenden Typen für verknüpfte Dienste wurden als Datenquellen und Senken für Kopieraktivitäten hinzugefügt:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Grundlegende Informationen und Beispiele finden Sie unter [Verschieben von Daten aus einem lokalen HDFS mithilfe von Azure Data Factory](data-factory-hdfs-connector.md) .
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Grundlegende Informationen und Beispiele finden Sie unter [Verschieben von Daten aus ODBC-Datenspeichern mithilfe von Azure Data Factory](data-factory-odbc-connector.md) .

## <a name="version-420"></a>Version 4.2.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Der folgende neue Aktivitätstyp wurde hinzugefügt: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Einzelheiten zu dieser Aktivität finden Sie unter [Aktualisieren von Azure ML-Modellen mithilfe der Ressourcenaktualisierungsaktivität](data-factory-azure-ml-batch-execution-activity.md).
* Die neue optionale Eigenschaft [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) wurde der Klasse [AzureMLLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) hinzugefügt.
* Die Eigenschaften [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) und [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) wurden der Klasse [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) hinzugefügt.
* Erlauben Sie die Konfiguration von Timeouts bei Clientaufrufen für den Data Factory-Dienst.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>Hinzugefügte Features
* Die folgenden verknüpften Diensttypen wurden hinzugefügt:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Die folgenden Aktivitätstypen wurden hinzugefügt:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Die folgenden Dataset-Typen wurden hinzugefügt:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Die folgenden Quellen- und Senkentypen wurden für die Kopieraktivität hinzugefügt:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>Version 4.0.1
### <a name="breaking-changes"></a>Aktuelle Änderungen
Die folgenden Klassen wurden umbenannt. Die neuen Namen entsprechen den ursprünglichen Namen der Klassen vor Version 4.0.0.

| Name in 4.0.0 | Name in 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Version 4.0.0
### <a name="breaking-changes"></a>Aktuelle Änderungen
* Die folgenden Klassen/Schnittstellen wurden umbenannt.

| Alter Name | Neuer Name |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabelle |[Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* Die **List-** -Methoden geben jetzt ausgelagerte Ergebnisse zurück. Wenn die Antwort eine nicht leere **NextLink** -Eigenschaft  enthält, muss die Clientanwendung das Abrufen der nächsten Seite fortsetzen, bis alle Seiten zurückgegeben wurden.  Beispiel:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List--** -Pipeline-API gibt nur die Zusammenfassung einer Pipeline anstatt alle Details zurück. Beispielsweise enthalten Aktivitäten in einer Pipelinezusammenfassung nur Name und Typ.

### <a name="feature-additions"></a>Hinzugefügte Features
* Die Klasse [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) unterstützt die beiden neuen Eigenschaften **SliceIdentifierColumnName** und **SqlWriterCleanupScript**, um idempotentes Kopieren in Azure Synapse Analytics zu ermöglichen. Ausführliche Informationen zu diesen Eigenschaften finden Sie im [Artikel zu Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md).
* Wir unterstützen jetzt im Rahmen der Kopieraktivität das Ausführen einer gespeicherten Prozedur für Azure SQL-Datenbank- und Azure Synapse Analytics-Quellen. Die Klassen [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) und [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) verfügen über die folgenden Eigenschaften: **SqlReaderStoredProcedureName** und **StoredProcedureParameters**. Ausführliche Informationen zu diesen Eigenschaften finden Sie auf „Azure.com“ in den Artikeln zu [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#sqlsource) und [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource).