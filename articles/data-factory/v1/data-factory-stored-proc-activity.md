---
title: SQL Server-Aktivität "Gespeicherte Prozedur"
description: Hier erfahren Sie, wie Sie die SQL Server-Aktivität „Gespeicherte Prozedur“ in einer Data Factory-Pipeline zum Aufrufen einer gespeicherten Prozedur in einer Azure SQL-Datenbank- oder Azure Synapse Analytics-Instanz verwenden.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: f702633f6311d4ae4a79452e9b1c9176125c56f9
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508398"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server-Aktivität "Gespeicherte Prozedur"
> [!div class="op_single_selector" title1="Transformationsaktivitäten"]
> * [Hive-Aktivität](data-factory-hive-activity.md)
> * [Pig-Aktivität](data-factory-pig-activity.md)
> * [MapReduce-Aktivität](data-factory-map-reduce.md)
> * [Hadoop-Streamingaktivität](data-factory-hadoop-streaming-activity.md)
> * [Spark-Aktivität](data-factory-spark.md)
> * [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-batch-execution-activity.md)
> * [Ressourcenaktualisierungsaktivität für Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md)
> * [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md)
> * [Benutzerdefinierte .NET-Aktivität](data-factory-use-custom-activities.md)

> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Transformieren von Daten mithilfe der Aktivität für gespeicherte Prozeduren in Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Übersicht
Sie verwenden Transformationsaktivitäten in einer Data Factory-[Pipeline](data-factory-create-pipelines.md), um Rohdaten in Vorhersagen und Erkenntnisse umzuwandeln und zu verarbeiten. Die Aktivität der „Gespeicherte Prozedur“ ist eine der Transformationsaktivitäten, die Data Factory unterstützt. Dieser Artikel baut auf dem Artikel [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten in Data Factory bietet.

Sie können die Aktivität „Gespeicherte Prozedur“ verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer (VM) aufzurufen:

- Azure SQL-Datenbank
- Azure Synapse Analytics
- SQL Server-Datenbank. Wenn Sie SQL Server verwenden, müssen Sie das Datenverwaltungsgateway auf dem Computer installieren, der die Datenbank hostet, oder auf einem separaten Computer, der Zugriff auf die Datenbank hat. Das Datenverwaltungsgateway ist eine Komponente, die lokale Datenquellen/Datenquellen auf virtuellen Azure Computern mit Clouddiensten auf sichere und geschickte Weise verbindet. Ausführliche Informationen finden Sie im Artikel [Datenverwaltungsgateway](data-factory-data-management-gateway.md).

> [!IMPORTANT]
> Beim Kopieren von Daten nach Azure SQL-Datenbank oder SQL Server können Sie **SqlSink** in der Kopieraktivität für das Aufrufen einer gespeicherten Prozedur mit der **sqlWriterStoredProcedureName**-Eigenschaft konfigurieren. Weitere Informationen finden Sie unter [Aufrufen von gespeicherten Prozeduren aus der Kopieraktivität](data-factory-invoke-stored-procedure-from-copy-activity.md). Ausführliche Informationen zur Eigenschaft finden Sie in folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Das Aufrufen einer gespeicherten Prozedur im Rahmen des Kopierens von Daten in Azure Synapse Analytics mithilfe einer Kopieraktivität wird nicht unterstützt. Sie können jedoch mithilfe der Aktivität „Gespeicherte Prozedur“ eine gespeicherte Prozedur in Azure Synapse Analytics aufrufen.
>
> Beim Kopieren von Daten aus Azure SQL-Datenbank, SQL Server oder Azure Synapse Analytics können Sie **SqlSource** in der Kopieraktivität mit der Eigenschaft **sqlReaderStoredProcedureName** konfigurieren, um eine gespeicherte Prozedur zum Lesen von Daten aus der Quelldatenbank aufzurufen. Weitere Informationen finden Sie in den folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

In der folgenden exemplarischen Vorgehensweise wird die Aktivität einer gespeicherten Prozedur in einer Pipeline zum Aufrufen einer gespeicherten Prozedur in Azure SQL-Datenbank verwendet.

## <a name="walkthrough"></a>Exemplarische Vorgehensweise
### <a name="sample-table-and-stored-procedure"></a>Beispiel für eine Tabelle und eine gespeicherte Prozedur
1. Erstellen Sie die folgende **Tabelle** in der Azure SQL-Datenbank mithilfe von SQL Server Management Studio oder anderen Tools, mit denen Sie vertraut sind. Die Spalte "datetimestamp" enthält das Datum und die Uhrzeit, zu der die entsprechende ID generiert wird.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    "Id" ist der eindeutige Bezeichner, und die Spalte "datetimestamp" enthält das Datum und die Uhrzeit, zu der die entsprechende ID generiert wird.
    
    ![Beispieldaten](./media/data-factory-stored-proc-activity/sample-data.png)

    In diesem Beispiel befindet sich die gespeicherte Prozedur in einer Azure SQL-Datenbank. Bei einer gespeicherten Prozedur in Azure Synapse Analytics und SQL Server-Datenbank ist der Ansatz ähnlich. Für eine SQL Server-Datenbank müssen Sie ein [Datenverwaltungsgateway](data-factory-data-management-gateway.md) installieren.
2. Erstellen Sie die folgende **gespeicherte Prozedur**, die Daten in **sampletable** einfügt.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Name** und **Groß-/Kleinschreibung** des Parameters („DateTime“ in diesem Beispiel) müssen mit dem Parameter übereinstimmen, der im JSON-Code der Pipeline/Aktivität angegeben ist. Stellen Sie bei der Definition der gespeicherten Prozedur sicher, dass **\@** als Präfix für den Parameter verwendet wird.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im Menü auf der linken Seite auf **NEU**, und klicken Sie auf **Intelligence + Analyse** und dann auf **Data Factory**.

    ![Neue Data Factory 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Geben Sie auf dem Blatt **Neue Data Factory** unter „Name“ die Zeichenfolge **SProcDF** ein. Azure Data Factory-Namen sind **global eindeutig**. Sie müssen dem Namen der Data Factory Ihren Namen voranstellen, damit die Factory erfolgreich erstellt werden kann.

   ![Neue Data Factory 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Wählen Sie Ihr Azure-**Abonnement** aus.
5. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
   1. Klicken Sie auf **Neu erstellen**, und geben Sie einen Namen für die Ressourcengruppe ein.
   2. Wählen Sie **Vorhandene verwenden**, um eine vorhandene Ressourcengruppe auszuwählen.
6. Wählen Sie den **Standort** für die Data Factory aus.
7. Wählen Sie **An Dashboard anheften**, damit Sie die Data Factory auf dem Dashboard sehen, wenn Sie sich das nächste Mal anmelden.
8. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
9. Im **Dashboard** des Azure-Portals sehen Sie, dass die Data Factory erstellt wird. Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory.

   ![Data Factory-Startseite](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Erstellen eines mit Azure SQL verknüpften Diensts
Nach dem Erstellen der Data Factory können Sie einen verknüpften Azure SQL-Dienst erstellen, der Ihre Datenbank in Azure SQL-Datenbank – mit der Beispieltabelle „sampletable“ und der gespeicherten Prozedur „usp_sample“ – mit Ihrer Data Factory verknüpft.

1. Klicken Sie auf dem Blatt **Data Factory** für **SProcDF** auf **Verfassen und bereitstellen**, um den Data Factory-Editor zu starten.
2. Klicken Sie in der Befehlsleiste auf **Neuer Datenspeicher**, und wählen Sie **Azure SQL-Datenbank**. Das JSON-Skript zum Erstellen eines mit Azure SQL verknüpften Diensts wird im Editor angezeigt.

   ![Neuer Datenspeicher 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. Nehmen Sie die folgenden Änderungen am JSON-Skript vor:

   1. Ersetzen Sie `<servername>` durch den Namen Ihres Servers.
   2. Ersetzen Sie `<databasename>` durch die Datenbank, in der Sie die Tabelle und die gespeicherte Prozedur erstellt haben.
   3. Ersetzen Sie `<username@servername>` durch das Benutzerkonto, das über Zugriff auf die Datenbank verfügt.
   4. Ersetzen Sie `<password>` durch das Kennwort für das Benutzerkonto.

      ![Neuer Datenspeicher 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Klicken Sie in der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen. Vergewissern Sie sich, dass AzureSqlLinkedService in der Strukturansicht links angezeigt wird.

    ![Strukturansicht mit verknüpftem Dienst 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Erstellen eines Ausgabedatasets
Sie müssen auch dann ein Ausgabedataset für eine Aktivität der gespeicherten Prozedur angeben, wenn die gespeicherte Prozedur keine Daten erzeugt. Der Grund dafür ist, dass das Ausgabedataset den Zeitplan der Aktivität steuert (also wie oft die Aktivität ausgeführt wird – stündlich, täglich usw.). Das Ausgabedataset muss einen **verknüpften Dienst** verwenden, der auf eine Azure SQL-Datenbank, eine Azure Synapse Analytics-Instanz oder eine SQL Server-Datenbank verweist, in der die gespeicherte Prozedur ausgeführt werden soll. Das Ausgabedataset kann verwendet werden, um das Ergebnis der gespeicherten Prozedur für die nachfolgende Verarbeitung durch eine andere Aktivität in der Pipeline zu übergeben ([Verketten von Aktivitäten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)). Data Factory schreibt die Ausgabe einer gespeicherten Prozedur jedoch nicht automatisch in dieses Dataset. Die gespeicherte Prozedur schreibt die Ausgabe in eine SQL-Tabelle, auf die das Ausgabedataset verweist. In einigen Fällen kann das Ausgabedataset ein **Dummydataset** sein (ein Dataset, das auf eine Tabelle verweist, die keine Ausgabe der gespeicherten Prozedur enthält). Dieses Dummydataset wird nur verwendet, um den Zeitplan für die Ausführung der Aktivität der gespeicherten Prozedur anzugeben.

1. Klicken Sie in der Symbolleiste auf **... Weitere**, klicken Sie auf **Neues Dataset**, und klicken Sie auf **Azure SQL**. **Neues Dataset**, und wählen Sie **Azure SQL**.

    ![Strukturansicht mit verknüpftem Dienst 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopieren Sie das folgende JSON-Skript, und fügen Sie es in den JSON-Editor ein.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Klicken Sie in der Befehlsleiste auf **Bereitstellen**, um das Dataset bereitzustellen. Vergewissern Sie sich, dass das Dataset in der Strukturansicht angezeigt wird.

    ![Strukturansicht mit verknüpften Diensten](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Erstellen einer Pipeline mit der Aktivität "SqlServerStoredProcedure"
Wir erstellen jetzt eine Pipeline mit einer Aktivität der gespeicherten Prozedur.

Beachten Sie die folgenden Eigenschaften:

- Die **type**-Eigenschaft ist auf **SqlServerStoredProcedure** festgelegt.
- **storedProcedureName** in Typeigenschaften ist auf **usp_sample** (Name der gespeicherten Prozedur) festgelegt.
- Der Abschnitt **storedProcedureParameters** enthält einen Parameter mit dem Namen **DateTime**. Name und Schreibweise des Parameters im JSON-Format müssen mit dem Namen und der Schreibweise des Parameters in der Definition der gespeicherten Prozedur übereinstimmen. Wenn Sie für einen Parameter Null übergeben müssen, verwenden Sie die folgende Syntax: `"param1": null` (nur Kleinbuchstaben).

1. Klicken Sie in der Symbolleiste auf **... Weitere** und dann auf **Neue Pipeline**.
2. Kopieren Sie folgenden JSON-Codeausschnitt, und fügen Sie ihn ein:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

### <a name="monitor-the-pipeline"></a>Überwachen der Pipeline
1. Klicken Sie auf **X**, um die Data Factory-Editor-Blätter zu schließen und zum Blatt „Data Factory“ zurückzukehren, und klicken Sie auf **Diagramm**.

    ![Diagrammkachel 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. In der **Diagrammansicht** sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.

    ![Diagrammkachel 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Doppelklicken Sie in der Diagrammansicht auf das Dataset `sprocsampleout`. Die Slices werden im Zustand „Bereit“ angezeigt. Es sollten fünf Slices vorhanden sein, da ein Slice für jede Stunde zwischen der Startzeit und Endzeit aus der JSON erstellt wird.

    ![Diagrammkachel 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Wenn sich ein Slice im Zustand **Bereit** befindet, führen Sie eine `select * from sampletable`-Abfrage in der Datenbank aus, um sicherzustellen, dass die Daten durch die gespeicherte Prozedur in die Tabelle eingefügt wurden.

   ![Ausgabedaten](./media/data-factory-stored-proc-activity/output.png)

   Unter [Überwachen der Pipeline](data-factory-monitor-manage-pipelines.md) finden Sie ausführliche Informationen zur Überwachung von Azure Data Factory-Pipelines.

## <a name="specify-an-input-dataset"></a>Angeben eines Eingabedatasets
In der exemplarischen Vorgehensweise weist die Aktivität der gespeicherten Prozedur keine Eingabedatasets auf. Wenn Sie ein Eingabedataset angeben, wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn das Slice des Eingabedatasets verfügbar ist (sich im Status „Bereit“ befindet). Das Dataset kann ein externes Dataset (das nicht von einer anderen Aktivität in der gleichen Pipeline erzeugt wird) oder ein internes Dataset sein, das von einer Upstreamaktivität (der Aktivität, die vor dieser Aktivität ausgeführt wird) erzeugt wird. Sie können mehrere Eingabedatasets für die Aktivität der gespeicherten Prozedur angeben. Wenn Sie dies tun, wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn alle Slices der Eingabedatasets verfügbar sind (sich im Status „Bereit“ befinden). Das Eingabedataset kann nicht als Parameter in der gespeicherten Prozedur genutzt werden. Es wird nur verwendet, um vor dem Start der Aktivität „Gespeicherte Prozedur“ die Abhängigkeit zu überprüfen.

## <a name="chaining-with-other-activities"></a>Verketten mit anderen Aktivitäten
Wenn Sie eine Upstreamaktivität mit dieser Aktivität verketten möchten, geben Sie die Ausgabe der Upstreamaktivität als Eingabe dieser Aktivität an. Wenn Sie dies tun, wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn die Upstreamaktivität abgeschlossen wurde und das Ausgabedataset der Upstreamaktivität verfügbar ist (sich im Status „Bereit“ befindet). Sie können Ausgabedatasets mehrerer Upstreamaktivitäten als Eingabedatasets der Aktivität der gespeicherten Prozedur angeben. Wenn Sie dies tun, wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn alle Slices der Eingabedatasets verfügbar sind.

Im folgenden Beispiel lautet die Ausgabe der Kopieraktivität wie folgt: „OutputDataset“. Dies ist eine Eingabe der Aktivität der gespeicherten Prozedur. Aus diesem Grund wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn die Kopieraktivität abgeschlossen wurde und das OutputDataset-Slice verfügbar ist (sich im Status „Bereit“ befindet). Wenn Sie mehrere Eingabedatasets angeben, wird die Aktivität der gespeicherten Prozedur erst ausgeführt, wenn alle Slices der Eingabedatasets verfügbar sind (sich im Status „Bereit“ befinden). Die Eingabedatasets können nicht direkt als Parameter für die Aktivität der gespeicherten Prozedur verwendet werden.

Weitere Informationen zum Verketten von Aktivitäten finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline).

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Auf ähnliche Weise geben Sie das Ausgabedataset der Aktivität der gespeicherten Prozedur als Eingabe der Downstreamaktivität in der Pipeline an, um die Aktivität der gespeicherten Prozedur mit **Downstreamaktivitäten** (Aktivitäten, die nach Abschluss der Aktivität der gespeicherten Prozedur ausgeführt werden) zu verknüpfen.

> [!IMPORTANT]
> Beim Kopieren von Daten nach Azure SQL-Datenbank oder SQL Server können Sie **SqlSink** in der Kopieraktivität für das Aufrufen einer gespeicherten Prozedur mit der **sqlWriterStoredProcedureName**-Eigenschaft konfigurieren. Weitere Informationen finden Sie unter [Aufrufen von gespeicherten Prozeduren aus der Kopieraktivität](data-factory-invoke-stored-procedure-from-copy-activity.md). Ausführliche Informationen zur Eigenschaft finden Sie in den folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Beim Kopieren von Daten aus Azure SQL-Datenbank, SQL Server oder Azure Synapse Analytics können Sie **SqlSource** in der Kopieraktivität mit der Eigenschaft **sqlReaderStoredProcedureName** konfigurieren, um eine gespeicherte Prozedur zum Lesen von Daten aus der Quelldatenbank aufzurufen. Weitere Informationen finden Sie in den folgenden Artikeln zu Connectors: [Azure SQL-Datenbank](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-Format
So sieht das JSON-Format zum Definieren der Aktivität „Gespeicherte Prozedur“ aus:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

In der folgenden Tabelle werden diese JSON-Eigenschaften beschrieben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| name | Der Name der Aktivität |Ja |
| description |Ein Text, der beschreibt, wofür die Aktivität verwendet wird. |Nein |
| type | Muss festgelegt werden auf: **SqlServerStoredProcedure** | Ja |
| inputs | Optional. Wenn Sie ein Eingabedataset angeben, muss es (im Status „Bereit“) verfügbar sein, damit die Aktivität „Gespeicherte Prozedur“ ausgeführt wird. Das Eingabedataset kann nicht als Parameter in der gespeicherten Prozedur genutzt werden. Es wird nur verwendet, um vor dem Start der Aktivität „Gespeicherte Prozedur“ die Abhängigkeit zu überprüfen. |Nein |
| outputs | Sie müssen ein Ausgabedataset für eine Aktivität „Gespeicherte Prozedur“ angeben. Das Ausgabedataset gibt den **Zeitplan** für die Aktivität „Gespeicherte Prozedur“ an (stündlich, wöchentlich, monatlich usw.). <br/><br/>Das Ausgabedataset muss einen **verknüpften Dienst** verwenden, der auf eine Azure SQL-Datenbank, eine Azure Synapse Analytics-Instanz oder eine SQL Server-Datenbank verweist, in der die gespeicherte Prozedur ausgeführt werden soll. <br/><br/>Das Ausgabedataset kann verwendet werden, um das Ergebnis der gespeicherten Prozedur für die nachfolgende Verarbeitung durch eine andere Aktivität in der Pipeline zu übergeben ([Verketten von Aktivitäten](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)). Data Factory schreibt die Ausgabe einer gespeicherten Prozedur jedoch nicht automatisch in dieses Dataset. Die gespeicherte Prozedur schreibt die Ausgabe in eine SQL-Tabelle, auf die das Ausgabedataset verweist. <br/><br/>In einigen Fällen kann das Ausgabedataset ein **Dummy-Dataset** sein, das nur dazu dient, den Zeitplan für die Ausführung der Aktivität „Gespeicherte Prozedur“ anzugeben. |Ja |
| storedProcedureName |Geben Sie den Namen der gespeicherten Prozedur in der Azure SQL-Datenbank-, Azure Synapse Analytics- oder SQL Server-Instanz an, die durch den von der Ausgabetabelle verwendeten verknüpften Dienst dargestellt wird. |Ja |
| storedProcedureParameters |Geben Sie Werte für Parameter der gespeicherten Prozedur an. Wenn Sie für einen Parameter Null übergeben müssen, verwenden Sie die folgende Syntax: "param1": null (nur Kleinbuchstaben). Das folgende Beispiel veranschaulicht die Verwendung dieser Eigenschaft. |Nein |

## <a name="passing-a-static-value"></a>Übergeben eines statischen Werts
Lassen Sie uns nun der Tabelle eine weitere Spalte mit dem Namen "Scenario" hinzufügen, die den statischen Wert "Document sample" enthält.

![Beispieldaten 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tabelle:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Gespeicherte Prozedur:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Übergeben Sie nun den Parameter **Scenario** und den Wert aus der Aktivität „Gespeicherte Prozedur“. Der Abschnitt **typeProperties** im obigen Beispiel sieht wie der folgende Codeausschnitt aus:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory-Dataset:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory-Pipeline**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
