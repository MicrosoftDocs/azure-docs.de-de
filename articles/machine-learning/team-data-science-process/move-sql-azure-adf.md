---
title: 'SQL Server-Daten zu SQL-Datenbank mit Azure Data Factory: Team Data Science-Prozess'
description: Einrichten eine ADF-Pipeline, die aus zwei Datenmigrationsaktivitäten besteht, die zusammen täglich Daten zwischen lokalen Datenbanken und in die Cloud verschieben.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 02fd6c1d4cbd1c2db287a38e086045042b5f220a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309549"
---
# <a name="move-data-from-a-sql-server-database-to-sql-database-with-azure-data-factory"></a>Verschieben von Daten aus einer SQL Server-Datenbank in eine SQL-Datenbank mit Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie Daten aus einer SQL Server-Datenbank mithilfe von Azure Data Factory (ADF) per Azure Blob Storage in eine Azure SQL-Datenbank-Instanz verschieben. Bei dieser Methode handelt es sich um einen unterstützten Legacyansatz, der die Vorteile einer replizierten Stagingkopie nutzt. [Wir empfehlen Ihnen jedoch, sich auf unserer Datenmigrationsseite über die neuesten Optionen zu informieren.](https://datamigration.microsoft.com/scenario/sql-to-azuresqldb?step=1)

Eine Tabelle, in der verschiedene Optionen für das Verschieben von Daten in eine Azure SQL-Datenbank zusammengefasst sind, finden Sie unter [Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning](move-sql-azure.md).

## <a name="introduction-what-is-adf-and-when-should-it-be-used-to-migrate-data"></a><a name="intro"></a>Einführung: Was ist ADF und wann sollte ADF für die Migration von Daten verwendet werden?
Azure Data Factory ist ein vollständig verwalteter, cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten orchestriert und automatisiert. Das Hauptkonzept im ADF-Modell ist die Pipeline. Eine Pipeline ist eine logische Gruppierung von Aktivitäten, von denen jede die Aktionen definiert, die mit den in Datasets enthaltenen Daten auszuführen sind. Verknüpfte Dienste werden verwendet, um die Informationen zu definieren, die Data Factory benötigt, um eine Verbindung mit den Datenressourcen herzustellen.

Mit ADF können vorhandene Datenverarbeitungsdienste zu Datenpipelines zusammengestellt werden, die hoch verfügbar sind und in der Cloud verwaltet werden. Diese Datenpipelines können geplant werden, um Daten zu erfassen, vorzubereiten, zu transformieren, zu analysieren und zu veröffentlichen, und die komplexen Daten und Verarbeitungsabhängigkeiten werden von ADF verwaltet und orchestriert. Lösungen lassen sich schnell in der Cloud erstellen und bereitstellen, wodurch eine wachsende Anzahl von lokalen und Clouddatenquellen miteinander verbunden werden.

Ziehen Sie ADF in Betracht:

* wenn Daten fortlaufend in ein Hybridszenario migriert werden müssen, das auf lokale und Cloudressourcen zugreift
* wenn bei der Migration Transformationen der Daten erforderlich sind oder ihnen Geschäftslogik hinzugefügt werden muss

ADF ermöglicht die Planung und Überwachung von Aufträgen mithilfe einfacher JSON-Skripts, die das Verschieben von Daten in regelmäßigen Abständen verwalten. ADF verfügt außerdem über weitere Funktionen wie Unterstützung für komplexe Vorgänge. Weitere Informationen zu ADF finden Sie in der Dokumentation zu [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="the-scenario"></a><a name="scenario"></a>Das Szenario
Wir richten eine ADF-Pipeline ein, die zwei Aktivitäten für die Migration von Daten aufweist. Gemeinsam verschieben sie täglich Daten zwischen einer SQL Server-Datenbank und Azure SQL-Datenbank. Die zwei Aktivitäten sind:

* Kopieren von Daten aus einer SQL Server-Datenbank in ein Azure Blob Storage-Konto
* Kopieren von Daten aus dem Azure Blob Storage-Konto in Azure SQL-Datenbank

> [!NOTE]
> Die hier gezeigten Schritte wurden aus dem detaillierteren Tutorial übernommen, das vom ADF-Team bereitgestellt wird: [Kopieren von Daten aus einer SQL Server-Datenbank in Azure Blob Storage](../../data-factory/tutorial-hybrid-copy-portal.md). Verweise auf die relevanten Abschnitte dieses Themas werden gegebenenfalls angegeben.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Voraussetzungen
In diesem Tutorial wird Folgendes vorausgesetzt:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* Ein **Azure-Speicherkonto**. Sie nutzen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md) . Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Weitere Informationen finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).
* Zugriff auf eine **Azure SQL-Datenbank**. Wenn Sie eine Azure SQL-Datenbank einrichten müssen, finden Sie im Thema [Erste Schritte mit Microsoft Azure SQL-Datenbank ](../../azure-sql/database/single-database-create-quickstart.md) Informationen dazu, wie Sie eine neue Instanz einer Azure SQL-Datenbank bereitstellen.
* Lokal installierte und konfigurierte **Azure PowerShell** . Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

> [!NOTE]
> In diesem Verfahren wird das [Azure-Portal](https://portal.azure.com/)verwendet.
>
>

## <a name="upload-the-data-to-your-sql-server-instance"></a><a name="upload-data"></a> Hochladen der Daten in die SQL Server-Instanz
Wir verwenden das [NYC Taxi-Dataset](https://chriswhong.com/open-data/foil_nyc_taxi/) , um den Migrationsprozess zu demonstrieren. Das NYC Taxi-Dataset ist, wie in diesem Beitrag erwähnt, in Azure Blob Storage unter [NYC Taxi Data](https://www.andresmh.com/nyctaxitrips/)verfügbar. Die Daten umfassen zwei Dateien, die Datei „trip_data.csv“, die Details zu den einzelnen Fahrten enthält, und die Datei „trip_far.csv“, die Details zu den pro Fahrt bezahlten Fahrpreisen enthält. Ein Beispiel und eine Beschreibung dieser Dateien finden Sie unter [Beschreibung des NYC Taxi Trips-Datasets](sql-walkthrough.md#dataset).

Sie können entweder das hier beschriebene Verfahren auf einen Satz Ihrer eigenen Daten anpassen oder die Schritte wie beschrieben unter Verwendung des NYC Taxi-Datasets durchführen. Um das NYC Taxi-Dataset in Ihre SQL Server-Datenbank hochzuladen, befolgen Sie das unter [Massenimport von Daten in eine SQL Server-Datenbank](sql-walkthrough.md#dbload) beschriebene Verfahren.

## <a name="create-an-azure-data-factory"></a><a name="create-adf"></a> Erstellen einer Azure Data Factory
Die Schritte zum Erstellen einer neuen Azure Data Factory und einer Ressourcengruppe im [Azure-Portal](https://portal.azure.com/) finden Sie unter [Erstellen einer Data Factory](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory). Nennen Sie die neue ADF-Instanz *adfdsp* und die erstellte Ressourcengruppe *adfdsprg*.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Installieren und Konfigurieren von Azure Data Factory Integration Runtime
Integration Runtime ist eine vom Kunden verwaltete Datenintegrationsinfrastruktur, die von Azure Data Factory genutzt wird, um Datenintegrationsfunktionen in verschiedenen Netzwerkumgebungen bereitzustellen. Diese Runtime wurde früher als „Datenverwaltungsgateway“ bezeichnet.

Zum Einrichten der Runtime [befolgen Sie die Anweisungen zum Erstellen einer Pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).

## <a name="create-linked-services-to-connect-to-the-data-resources"></a><a name="adflinkedservices"></a>Erstellen von verknüpften Diensten zum Herstellen einer Verbindung mit den Datenressourcen
Ein verknüpfter Dienst definiert die Informationen, die Azure Data Factory für das Herstellen einer Verbindung mit einer Datenquelle benötigt. Wir haben drei Ressourcen in diesem Szenario, für die verknüpfte Dienste erforderlich sind:

1. Lokaler SQL Server
2. Azure Blob Storage
3. Azure SQL-Datenbank

Die schrittweise Anleitung zum Erstellen von verknüpften Diensten finden Sie unter [Erstellen von verknüpften Diensten](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).


## <a name="define-and-create-tables-to-specify-how-to-access-the-datasets"></a><a name="adf-tables"></a>Definieren und Erstellen von Tabellen, um die Art des Zugriffs auf Datasets anzugeben
Erstellen Sie Tabellen, in denen die Struktur, der Speicherort und die Verfügbarkeit der Datasets angegeben werden, mit den folgenden skriptbasierten Verfahren. Zum Definieren der Tabellen werden JSON-Dateien verwendet. Weitere Informationen zur Struktur dieser Dateien finden Sie unter [Datasets](../../data-factory/concepts-datasets-linked-services.md).

> [!NOTE]
> Sie müssen das Cmdlet `Add-AzureAccount` ausführen, bevor Sie das Cmdlet [New-AzureDataFactoryTable](/previous-versions/azure/dn835096(v=azure.100)) ausführen, um sicherzustellen, dass das richtige Azure-Abonnement für die Ausführung der Befehle ausgewählt ist. Die Dokumentation zu diesem Cmdlet finden Sie unter [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-3.7.0).
>
>

Die JSON-basierten Definitionen in den Tabellen verwenden die folgenden Namen:

* Der **Tabellenname** in der SQL Server-Instanz lautet *nyctaxi_data*.
* Der **Containername** im Azure Blob Storage-Konto lautet *containername*

Für diese ADF-Pipeline sind drei Tabellendefinitionen erforderlich:

1. [Lokale SQL-Tabelle](#adf-table-onprem-sql)
2. [Blob-Tabelle](#adf-table-blob-store)
3. [SQL Azure-Tabelle](#adf-table-azure-sql)

> [!NOTE]
> In den folgenden Verfahren wird Azure PowerShell verwendet, um die ADF-Aktivitäten zu definieren und zu erstellen. Diese Aufgaben können Sie allerdings auch über das Azure-Portal ausführen. Weitere Informationen finden Sie unter [Erstellen von Datasets](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

### <a name="sql-on-premises-table"></a><a name="adf-table-onprem-sql"></a>Lokale SQL-Tabelle
Die Tabellendefinition für den SQL Server wird in der folgenden JSON-Datei angegeben:

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Die Spaltennamen sind hier nicht enthalten. Sie können eine Unterauswahl in den Spalten treffen, indem Sie sie hier aufnehmen (Details finden Sie in der [ADF-Dokumentation](../../data-factory/copy-activity-overview.md)).

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *onpremtabledef.json* , und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\temp\onpremtabledef.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json
```


### <a name="blob-table"></a><a name="adf-table-blob-store"></a>Blob-Tabelle
Die Tabellendefinition für den Blob-Speicherort der Ausgabe finden Sie im Folgenden (hierbei werden die erfassten lokalen Daten dem Azure-Blob zugeordnet):

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *bloboutputtabledef.json* , und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\temp\bloboutputtabledef.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json
```

### <a name="sql-azure-table"></a><a name="adf-table-azure-sql"></a>SQL Azure-Tabelle
Die Tabellendefinition für die SQL Azure-Ausgabe finden Sie im Folgenden (dieses Schema ordnet die vom Blob stammenden Daten zu):

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Kopieren Sie die JSON-Definition der Tabelle in eine Datei namens *AzureSqlTable.json* , und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\temp\AzureSqlTable.json* vorausgesetzt). Erstellen Sie die Tabelle in ADF mit dem folgenden Azure PowerShell-Cmdlet:

```azurepowershell
New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json
```


## <a name="define-and-create-the-pipeline"></a><a name="adf-pipeline"></a>Definieren und Erstellen der Pipeline
Geben Sie die Aktivitäten an, die zu der Pipeline gehören, und erstellen Sie die Pipeline mit den folgenden skriptbasierten Verfahren. Zum Definieren der Pipeline-Eigenschaften wird eine JSON-Datei verwendet.

* Das Skript setzt voraus, dass der **Pipelinename** *AMLDSProcessPipeline* lautet.
* Beachten Sie außerdem, dass wir die Häufigkeit auf eine tägliche Ausführung festgelegt haben und die Standardausführungszeit des Auftrags verwenden (12 Uhr UTC).

> [!NOTE]
> Die folgenden Verfahren verwenden Azure PowerShell zum Definieren und Erstellen der ADF-Pipeline. Diese Aufgabe können Sie allerdings auch über das Azure-Portal ausführen. Weitere Informationen finden Sie unter [Erstellen einer Pipeline](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline).
>
>

Unter Verwendung der oben angegebenen Tabellendefinitionen wird die Pipelinedefinition für die ADF wie folgt angegeben:

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has two activities: the first one copies data from SQL Server to Azure Blob, and the second one copies from Azure Blob to Azure Database Table",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from SQL Server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

Kopieren Sie diese JSON-Definition der Pipeline in eine Datei namens *pipelinedef.json* , und speichern Sie sie an einem bekannten Speicherort (hier wird *C:\temp\pipelinedef.json* vorausgesetzt). Erstellen Sie die Pipeline in ADF mit dem folgenden Azure PowerShell-Cmdlet:

```azurepowershell
New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json
```


## <a name="start-the-pipeline"></a><a name="adf-pipeline-start"></a>Starten der Pipeline
Die Pipeline kann jetzt mithilfe der folgenden Befehle ausgeführt werden:

```azurepowershell
Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline
```

Die Parameterwerte *startdate* und *enddate* müssen durch die tatsächlichen Daten ersetzt werden, zwischen denen die Pipeline ausgeführt werden soll.

Sobald die Pipeline ausgeführt wird, sollten Sie die Daten sehen können, die im für den Blob ausgewählten Container angezeigt werden – immer eine Datei pro Tag.

Wir haben nicht die Funktion von ADF zum inkrementellen Übertragen von Daten per Pipe genutzt. Weitere Informationen zur Vorgehensweise sowie zu weiteren von ADF bereitgestellten Funktionen finden Sie in der [ADF-Dokumentation](https://azure.microsoft.com/services/data-factory/).