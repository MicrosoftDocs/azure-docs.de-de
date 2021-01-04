---
title: Inkrementelles Kopieren von Daten mithilfe der Änderungsnachverfolgung und mit PowerShell
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline, bei der Deltadaten inkrementell aus mehreren Tabellen einer SQL Server-Datenbank in Azure SQL-Datenbank kopiert werden.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 01/22/2018
ms.openlocfilehash: 85fabb540180adb1848285f4c40f944225db2760
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508576"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-powershell"></a>Inkrementelles Laden von Daten aus Azure SQL-Datenbank in Azure Blob Storage mit Informationen der Änderungsnachverfolgung und PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Azure Data Factory mit einer Pipeline, die Deltadaten basierend auf Informationen der **Änderungsnachverfolgung** in der Quelldatenbank in Azure SQL-Datenbank als Quelle in Azure Blob Storage lädt.  

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten des Quelldatenspeichers
> * Erstellen einer Data Factory.
> * Erstellen Sie verknüpfte Dienste.
> * Erstellen von Datasets für Quelle, Senke und Änderungsnachverfolgung
> * Erstellen, Ausführen und Überwachen der vollständigen Kopierpipeline
> * Hinzufügen oder Aktualisieren von Daten in der Quelltabelle
> * Erstellen, Ausführen und Überwachen der inkrementellen Kopierpipeline

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
In einer Datenintegrationslösung ist das inkrementelle Laden von Daten nach anfänglichen vollständigen Ladevorgängen ein häufig verwendetes Szenario. In einigen Fällen können die geänderten Daten für einen Zeitraum Ihres Quelldatenspeichers leicht aufgeteilt werden (z.B. LastModifyTime, CreationTime). Manchmal gibt es keine explizite Möglichkeit, die Deltadaten seit der letzten Verarbeitung der Daten zu identifizieren. Die Technologie für die Änderungsnachverfolgung, die von Datenspeichern unterstützt wird, z.B. Azure SQL-Datenbank und SQL Server, kann zum Identifizieren der Deltadaten verwendet werden.  In diesem Tutorial wird beschrieben, wie Sie Azure Data Factory zusammen mit der SQL-Technologie für die Änderungsnachverfolgung nutzen, um Deltadaten inkrementell aus Azure SQL-Datenbank in Azure Blob Storage zu laden.  Konkretere Informationen zur SQL-Technologie für die Änderungsnachverfolgung finden Sie unter [Informationen zur Änderungsnachverfolgung (SQL Server)](/sql/relational-databases/track-changes/about-change-tracking-sql-server).

## <a name="end-to-end-workflow"></a>Kompletter Workflow
Hier sind die Schritte des typischen End-to-End-Workflows zum inkrementellen Laden von Daten per Technologie für die Änderungsnachverfolgung angegeben.

> [!NOTE]
> Diese Technologie wird sowohl von Azure SQL-Datenbank als auch von SQL Server unterstützt. In diesem Tutorial wird Azure SQL-Datenbank als Quelldatenspeicher verwendet. Sie können auch eine SQL Server-Instanz verwenden.

1. **Initial loading of historical data** (Erstes Laden von Verlaufsdaten) (einmalige Ausführung):
    1. Aktivieren Sie die Technologie für die Änderungsnachverfolgung in der Quelldatenbank in Azure SQL-Datenbank.
    2. Rufen Sie den Anfangswert von SYS_CHANGE_VERSION in der Datenbank als Baseline zum Erfassen von geänderten Daten ab.
    3. Laden Sie die vollständigen Daten aus der Quelldatenbank in Azure Blob Storage.
2. **Incremental loading of delta data on a schedule** (Inkrementelles Laden von Deltadaten nach einem Zeitplan) (regelmäßige Ausführung nach dem ersten Laden der Daten):
    1. Rufen Sie die alten und neuen SYS_CHANGE_VERSION-Werte ab.
    2. Laden Sie die Deltadaten, indem Sie die Primärschlüssel von geänderten Zeilen (zwischen zwei SYS_CHANGE_VERSION-Werten) aus **sys.change_tracking_tables** mit Daten in der **Quelltabelle** verknüpfen, und verschieben Sie die Deltadaten dann auf das Ziel.
    3. Aktualisieren Sie SYS_CHANGE_VERSION für das nächste Deltaladen.

## <a name="high-level-solution"></a>Allgemeine Lösung
In diesem Tutorial erstellen Sie zwei Pipelines, mit denen die folgenden beiden Vorgänge durchgeführt werden:  

1. **Erstes Laden:** : Sie erstellen eine Pipeline mit einer Kopieraktivität, bei der die gesamten Daten aus dem Quelldatenspeicher (Azure SQL-Datenbank) in den Zieldatenspeicher (Azure Blob Storage) kopiert werden.

    ![Vollständiges Laden von Daten](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Inkrementell laden:** Sie erstellen eine Pipeline mit den folgenden Aktivitäten und führen sie regelmäßig aus.
    1. Erstellen Sie **zwei Lookup-Aktivitäten**, um die alte und neue SYS_CHANGE_VERSION aus Azure SQL-Datenbank abzurufen und an die Kopieraktivität zu übergeben.
    2. Erstellen Sie **eine Kopieraktivität**, um die eingefügten/aktualisierten/gelöschten Daten zwischen den beiden SYS_CHANGE_VERSION-Werten aus Azure SQL-Datenbank nach Azure Blob Storage zu kopieren.
    3. Erstellen Sie **eine Aktivität „Gespeicherte Prozedur“** , um den Wert von SYS_CHANGE_VERSION für die nächste Pipelineausführung zu aktualisieren.

    ![Flussdiagramm für inkrementelles Laden](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure PowerShell. Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) befolgen.
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als den **Quell**-Datenspeicher. Wenn Sie in Azure SQL-Datenbank noch keine Datenbank haben, lesen Sie den Artikel [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank.
* **Azure Storage-Konto**. Sie verwenden den Blob Storage als den **Senken**-Datenspeicher. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos. Erstellen Sie einen Container mit dem Namen **Adftutorial**. 

### <a name="create-a-data-source-table-in-your-database"></a>Erstellen einer Datenquellentabelle in Ihrer Datenbank

1. Starten Sie **SQL Server Management Studio**, und stellen Sie eine Verbindung mit SQL-Datenbank her.
2. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre **Datenbank**, und wählen Sie **Neue Abfrage**.
3. Führen Sie den folgenden SQL-Befehl für Ihre Datenbank aus, um eine Tabelle mit dem Namen `data_source_table` als Datenquellenspeicher zu erstellen.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Aktivieren Sie den Mechanismus für die **Änderungsnachverfolgung** in Ihrer Datenbank und der Quelltabelle (data_source_table), indem Sie die folgende SQL-Abfrage ausführen:

    > [!NOTE]
    > - Ersetzen Sie &lt;your database name&gt; durch den Namen Ihrer Datenbank, in der „data_source_table“ enthalten ist.
    > - Im aktuellen Beispiel werden die geänderten Daten zwei Tage lang aufbewahrt. Wenn Sie die geänderten Daten für drei oder mehr Tage laden, sind einige geänderte Daten nicht enthalten.  Eine Möglichkeit besteht darin, den Wert von CHANGE_RETENTION zu erhöhen. Alternativ dazu können Sie sicherstellen, dass Ihr Zeitraum für das Laden der geänderten Daten nicht mehr als zwei Tage beträgt. Weitere Informationen finden Sie unter [Aktivieren der Änderungsnachverfolgung für eine Datenbank](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database).

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Erstellen Sie eine neue Tabelle, und speichern Sie „ChangeTracking_version“ mit einem Standardwert, indem Sie die folgende Abfrage ausführen:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Wenn sich die Daten nach dem Aktivieren der Änderungsnachverfolgung für SQL-Datenbank nicht geändert haben, lautet der Wert für die Version der Änderungsnachverfolgung „0“.
6. Führen Sie die folgende Abfrage zum Erstellen einer gespeicherten Prozedur in Ihrer Datenbank aus. Die Pipeline ruft diese gespeicherte Prozedur auf, um die Version der Änderungsnachverfolgung in der Tabelle zu aktualisieren, die Sie im vorherigen Schritt erstellt haben.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) befolgen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
1. Definieren Sie eine Variable für den Ressourcengruppennamen zur späteren Verwendung in PowerShell-Befehlen. Kopieren Sie den folgenden Befehlstext nach PowerShell, geben Sie einen Namen für die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) in doppelten Anführungszeichen an, und führen Sie dann den Befehl aus. Beispiel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$resourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.
2. Definieren Sie eine Variable für den Speicherort der Data Factory:

    ```powershell
    $location = "East US"
    ```
3. Führen Sie den folgenden Befehl aus, um die Azure-Ressourcengruppe zu erstellen:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$resourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.
3. Definieren Sie eine Variable für den Namen der Data Factory.

    > [!IMPORTANT]
    >  Aktualisieren Sie den Data Factory-Namen, damit er global eindeutig ist.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Führen Sie zum Erstellen der Data Factory das Cmdlet **Set-AzDataFactoryV2** wie folgt aus:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle **Mitwirkender** oder **Besitzer** oder ein **Administrator** des Azure-Abonnements sein.
* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.


## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Abschnitt erstellen Sie verknüpfte Dienste mit Ihrem Azure Storage-Konto und mit Ihrer Datenbank in Azure SQL-Datenbank.

### <a name="create-azure-storage-linked-service"></a>Erstellen des verknüpften Azure Storage-Diensts.
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit der Data Factory.

1. Erstellen Sie eine JSON-Datei mit dem Namen **AzureStorageLinkedService.json** im Ordner **C:\ADFTutorials\IncCopyChangeTrackingTutorial** und dem folgenden Inhalt: (Erstellen Sie den Ordner, falls er noch nicht vorhanden ist.) Ersetzen Sie `<accountName>` und `<accountKey>` durch den Namen bzw. Schlüssel Ihres Azure Storage-Kontos, bevor Sie die Datei speichern.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. Wechseln Sie in **Azure PowerShell** zum Ordner **C:\ADFTutorials\IncCopyChangeTrackingTutorial**.
3. Führen Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureStorageLinkedService**. Im folgenden Beispiel, übergeben Sie Werte für die **ResourceGroupName**- und **DataFactoryName**-Parameter.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Erstellen Sie einen Azure SQL-Datenbank -verknüpften Dienst.
In diesem Schritt verknüpfen Sie Ihre Datenbank mit der Data Factory.

1. Erstellen Sie eine JSON-Datei mit dem Namen **AzureSQLDatabaseLinkedService.json** im Ordner **C:\ADFTutorials\IncCopyChangeTrackingTutorial** und dem folgenden Inhalt: Ersetzen Sie **&lt;server&gt;, &lt;database name&gt;, &lt;user id&gt; und &lt;password&gt;** durch den Namen Ihres Servers, den Namen Ihrer Datenbank, die Benutzer-ID und das Kennwort, bevor Sie die Datei speichern.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. Führen Sie in **Azure PowerShell** das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureSQLDatabaseLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, die für die Datenquelle, das Datenziel und den Ort zum Speichern der SYS_CHANGE_VERSION stehen.

### <a name="create-a-source-dataset"></a>Erstellen eines Quelldatasets
In diesem Schritt erstellen Sie ein Dataset, das für die Quelldaten steht.

1. Erstellen Sie eine JSON-Datei mit dem Namen „SourceDataset.json“ im selben Ordner und dem folgenden Inhalt:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  Führen Sie das Cmdlet „Set-AzDataFactoryV2Dataset“ aus, um das Dataset zu erstellen: SourceDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Erstellen Sie ein Senkendataset
In diesem Schritt erstellen Sie ein Dataset, das für die Daten steht, die aus dem Quelldatenspeicher kopiert werden.

1. Erstellen Sie eine JSON-Datei mit dem Namen „SinkDataset.json“ im selben Ordner und dem folgenden Inhalt:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Sie erstellen den Container „adftutorial“ in Ihrer Azure Blob Storage-Instanz im Rahmen der Erfüllung der Voraussetzungen. Erstellen Sie den Container, wenn er noch nicht vorhanden ist (oder) geben Sie den Namen eines bereits vorhandenen ein. In diesem Tutorial wird der Name der Ausgabedatei dynamisch generiert, indem der folgende Ausdruck verwendet wird: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Führen Sie das Cmdlet „Set-AzDataFactoryV2Dataset“ aus, um das Dataset zu erstellen: SinkDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Erstellen eines Datasets für die Änderungsnachverfolgung
In diesem Schritt erstellen Sie ein Dataset zum Speichern der Version für die Änderungsnachverfolgung.  

1. Erstellen Sie in demselben Ordner eine JSON-Datei mit dem Namen „ChangeTrackingDataset.json“ und folgendem Inhalt:

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Sie erstellen die Tabelle „table_store_ChangeTracking_version“ im Rahmen der Erfüllung der Voraussetzungen.
2.  Führen Sie das Cmdlet „Set-AzDataFactoryV2Dataset“ aus, um das Dataset zu erstellen: ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Erstellen einer Pipeline für den vollständigen Kopiervorgang
In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität, bei der die gesamten Daten aus dem Quelldatenspeicher (Azure SQL-Datenbank) in den Zieldatenspeicher (Azure Blob Storage) kopiert werden.

1. Erstellen Sie die JSON-Datei „FullCopyPipeline.json“ im gleichen Ordner, aber mit folgendem Inhalt:

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Führen Sie das Cmdlet „Set-AzDataFactoryV2Pipeline“ aus, um die Pipeline zu erstellen: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   Hier ist die Beispielausgabe:

   ```console
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>Ausführen der vollständigen Kopierpipeline
Führen Sie die Pipeline **FullCopyPipeline** mithilfe des Cmdlets **Invoke-AzDataFactoryV2Pipeline** aus.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>Überwachen der vollständigen Kopierpipeline

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste**, führen Sie eine Suche mit dem Schlüsselwort `data factories` durch, und wählen Sie **Data Factorys** aus.

    ![Menü „Data Factorys“](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Suchen Sie in der Liste mit den Data Factorys nach **Ihrer Data Factory**, und wählen Sie sie aus, um die Seite „Data Factory“ anzuzeigen.

    ![Suchen nach Ihrer Data Factory](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. Klicken Sie auf der Seite für die Data Factory auf die Kachel **Überwachung und Verwaltung**.

    ![Kachel „Überwachung und Verwaltung“](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. Die **Anwendung für die Datenintegration** wird in einer separaten Registerkarte gestartet. Alle **Pipelineausführungen** mit dem dazugehörigen Status werden angezeigt. Beachten Sie, dass der Status der Pipelineausführung im folgenden Beispiel **Erfolgreich** lautet. Sie können die an die Pipeline übergebenen Parameter überprüfen, indem Sie in der Spalte **Parameter** auf den Link klicken. Wenn ein Fehler auftritt, wird in der Spalte **Fehler** ein Link angezeigt. Klicken Sie in der Spalte **Aktionen** auf den Link.

    ![Screenshot: Pipelineausführungen für eine Data Factory](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. Wenn Sie in der Spalte **Aktionen** auf den Link klicken, wird die folgende Seite mit allen **Aktivitätsausführungen** der Pipeline angezeigt.

    ![Screenshot: Aktivitätsausführungen für eine Data Factory mit hervorgehobenem Link „Pipelines“](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. Klicken Sie wie in der Abbildung dargestellt auf **Pipelines**, um zurück zur Ansicht **Pipelineausführungen** zu wechseln.


### <a name="review-the-results"></a>Überprüfen der Ergebnisse
Im Ordner `incchgtracking` des Containers `adftutorial` wird eine Datei mit dem Namen `incremental-<GUID>.txt` angezeigt.

![Ausgabedatei des vollständigen Kopiervorgangs](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

Die Datei sollte die Daten aus Ihrer Datenbank enthalten:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Hinzufügen von weiteren Daten zur Quelltabelle

Führen Sie die folgende Abfrage für Ihre Datenbank aus, um eine Zeile hinzuzufügen und eine Zeile zu aktualisieren.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Erstellen einer Pipeline für die Deltakopie
In diesem Schritt erstellen Sie eine Pipeline mit den folgenden Aktivitäten und führen sie regelmäßig aus. Mit den **Lookup-Aktivitäten** wird die alte und neue SYS_CHANGE_VERSION aus Azure SQL-Datenbank abgerufen und an die Kopieraktivität übergeben. Die **Kopieraktivität** kopiert die eingefügten/aktualisierten/gelöschten Daten zwischen den beiden SYS_CHANGE_VERSION-Werten aus Azure SQL-Datenbank nach Azure Blob Storage. Die **Aktivität „Gespeicherte Prozedur“** aktualisiert den Wert von SYS_CHANGE_VERSION für die nächste Pipelineausführung.

1. Erstellen Sie die JSON-Datei „IncrementalCopyPipeline.json“ im gleichen Ordner, aber mit folgendem Inhalt:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupLastChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                        },
                        "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupCurrentChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },
                        "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupLastChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupCurrentChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
                {
                    "name": "StoredProceduretoUpdateChangeTrackingActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "Update_ChangeTracking_Version",
                        "storedProcedureParameters": {
                            "CurrentTrackingVersion": {
                                "value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}",
                                "type": "INT64"
                            },
                            "TableName": {
                                "value": "@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}",
                                "type": "String"
                            }
                        }
                    },
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Führen Sie das Cmdlet „Set-AzDataFactoryV2Pipeline“ aus, um die Pipeline zu erstellen: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Hier ist die Beispielausgabe:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Ausführen der inkrementellen Kopierpipeline
Führen Sie die Pipeline **IncrementalCopyPipeline** mithilfe des Cmdlets **Invoke-AzDataFactoryV2Pipeline** aus.

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>Überwachen der inkrementellen Kopierpipeline
1. Aktualisieren Sie in der **Anwendung für die Datenintegration** die Ansicht **Pipelineausführungen**. Vergewissern Sie sich, dass „IncrementalCopyPipeline“ in der Liste enthalten ist. Klicken Sie in der Spalte **Aktionen** auf den Link.  

    ![Screenshot: Pipelineausführungen für eine Data Factory einschließlich Ihrer Pipeline](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. Wenn Sie in der Spalte **Aktionen** auf den Link klicken, wird die folgende Seite mit allen **Aktivitätsausführungen** der Pipeline angezeigt.

    ![Screenshot: Pipelineausführungen für eine Data Factory, von denen mehrere als erfolgreich gekennzeichnet sind](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. Klicken Sie wie in der Abbildung dargestellt auf **Pipelines**, um zurück zur Ansicht **Pipelineausführungen** zu wechseln.

### <a name="review-the-results"></a>Überprüfen der Ergebnisse
Die zweite Datei ist im Ordner `incchgtracking` des Containers `adftutorial` enthalten.

![Ausgabedatei des inkrementellen Kopiervorgangs](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

Die Datei sollte nur die Deltadaten aus Ihrer Datenbank enthalten. Der Datensatz mit der Kennzeichnung `U` ist die aktualisierte Zeile in der Datenbank, und mit `I` wird die hinzugefügte Zeile angegeben.

```
1,update,10,2,U
6,new,50,1,I
```
Die ersten drei Spalten enthalten geänderte Daten aus „data_source_table“. Die letzten beiden Spalten enthalten die Metadaten aus der Systemtabelle für die Änderungsnachverfolgung. Die vierte Spalte enthält die SYS_CHANGE_VERSION für die einzelnen geänderten Zeilen. Die fünfte Spalte enthält den Vorgang:  U = update (aktualisieren), I = insert (einfügen).  Weitere Informationen zu den Informationen zur Änderungsnachverfolgung finden Sie unter [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```


## <a name="next-steps"></a>Nächste Schritte
Im folgenden Tutorial erfahren Sie mehr über das Kopieren von neuen und geänderten Dateien nur auf Grundlage ihres LastModifiedDate-Werts:

> [!div class="nextstepaction"]
>[Kopieren neuer Dateien basierend auf „lastmodifieddate“](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
