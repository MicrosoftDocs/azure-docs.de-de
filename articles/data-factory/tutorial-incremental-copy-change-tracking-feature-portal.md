---
title: Inkrementelles Kopieren von Daten mithilfe der Änderungsnachverfolgung und des Azure-Portals
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Instanz mit einer Pipeline, die Deltadaten basierend auf Informationen der Änderungsnachverfolgung in der Quelldatenbank in Azure SQL-Datenbank in einen Azure-Blobspeicher lädt.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/12/2018
ms.openlocfilehash: 42ca5b2c58568a6b38ed71bcd54badbac603cf16
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378722"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Inkrementelles Laden von Daten aus Azure SQL-Datenbank in Azure Blob Storage mit Informationen der Änderungsnachverfolgung und dem Azure-Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Azure Data Factory-Instanz mit einer Pipeline, die Deltadaten basierend auf Informationen der **Änderungsnachverfolgung** in der Quelldatenbank in Azure SQL-Datenbank in einen Azure-Blobspeicher lädt.  

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten des Quelldatenspeichers
> * Erstellen einer Data Factory.
> * Erstellen Sie verknüpfte Dienste.
> * Erstellen von Datasets für Quelle, Senke und Änderungsnachverfolgung
> * Erstellen, Ausführen und Überwachen der vollständigen Kopierpipeline
> * Hinzufügen oder Aktualisieren von Daten in der Quelltabelle
> * Erstellen, Ausführen und Überwachen der inkrementellen Kopierpipeline

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
    3. Laden Sie die Deltadaten, indem Sie die Primärschlüssel von geänderten Zeilen (zwischen zwei SYS_CHANGE_VERSION-Werten) aus **sys.change_tracking_tables** mit Daten in der **Quelltabelle** verknüpfen, und verschieben Sie die Deltadaten dann auf das Ziel.
    4. Aktualisieren Sie SYS_CHANGE_VERSION für das nächste Deltaladen.

## <a name="high-level-solution"></a>Allgemeine Lösung
In diesem Tutorial erstellen Sie zwei Pipelines, mit denen die folgenden beiden Vorgänge durchgeführt werden:  

1. **Erstes Laden:** : Sie erstellen eine Pipeline mit einer Kopieraktivität, bei der die gesamten Daten aus dem Quelldatenspeicher (Azure SQL-Datenbank) in den Zieldatenspeicher (Azure Blob Storage) kopiert werden.

    ![Vollständiges Laden von Daten](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Inkrementell laden:** Sie erstellen eine Pipeline mit den folgenden Aktivitäten und führen sie regelmäßig aus.
    1. Erstellen Sie **zwei Lookup-Aktivitäten**, um die alte und neue SYS_CHANGE_VERSION aus Azure SQL-Datenbank abzurufen und an die Kopieraktivität zu übergeben.
    2. Erstellen Sie **eine Kopieraktivität**, um die eingefügten/aktualisierten/gelöschten Daten zwischen den beiden SYS_CHANGE_VERSION-Werten aus Azure SQL-Datenbank nach Azure Blob Storage zu kopieren.
    3. Erstellen Sie **eine Aktivität „Gespeicherte Prozedur“** , um den Wert von SYS_CHANGE_VERSION für die nächste Pipelineausführung zu aktualisieren.

    ![Flussdiagramm für inkrementelles Laden](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als den **Quell**-Datenspeicher. Wenn Sie in Azure SQL-Datenbank noch keine Datenbank haben, lesen Sie den Artikel [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank.
* **Azure Storage-Konto**. Sie verwenden den Blob Storage als den **Senken**-Datenspeicher. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos. Erstellen Sie einen Container mit dem Namen **Adftutorial**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Erstellen einer Datenquellentabelle in Azure SQL-Datenbank

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
    > - Ersetzen Sie &lt;your database name&gt; durch den Namen der Datenbank in Azure SQL-Datenbank, in der „data_source_table“ enthalten ist.
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) befolgen.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus:

   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

     ![Seite „Neue Data Factory“](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)

   Der Name der Azure Data Factory-Instanz muss **global eindeutig** sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).

   *Der Data Factory-Name „ADFTutorialDataFactory“ ist nicht verfügbar.*
3. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
4. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:

      - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
      - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
         
        Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
4. Wählen Sie **V2 (Vorschau)** als **Version** aus.
5. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.
6. Wählen Sie die Option **An Dashboard anheften** aus.     
7. Klicken Sie auf **Erstellen**.      
8. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...).

    ![Kachel „Die Data Factory wird bereitgestellt“](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.

   ![Data Factory-Startseite](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.
11. Wechseln Sie im linken Bereich der Seite **Erste Schritte** zur Registerkarte **Bearbeiten**, wie in der folgenden Abbildung gezeigt:

    ![Schaltfläche „Create pipeline“ (Pipeline erstellen)](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Abschnitt erstellen Sie verknüpfte Dienste mit Ihrem Azure Storage-Konto und mit Ihrer Datenbank in Azure SQL-Datenbank.

### <a name="create-azure-storage-linked-service"></a>Erstellen des verknüpften Azure Storage-Diensts.
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit der Data Factory.

1. Klicken Sie auf **Verbindungen** und dann auf **+ Neu**.

   ![Schaltfläche für eine neue Verbindung](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Azure Blob Storage**, und klicken Sie dann auf **Weiter**.

   ![Auswählen von „Azure Blob Storage“](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie unter **Name** die Zeichenfolge **AzureStorageLinkedService** ein.
    2. Wählen Sie unter **Speicherkontoname** Ihr Azure Storage-Konto aus.
    3. Klicken Sie auf **Speichern**.

   ![Einstellungen für Azure-Speicherkonto](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Erstellen Sie einen Azure SQL-Datenbank -verknüpften Dienst.
In diesem Schritt verknüpfen Sie Ihre Datenbank mit der Data Factory.

1. Klicken Sie auf **Verbindungen** und dann auf **+ Neu**.
2. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Azure SQL-Datenbank**, und klicken Sie auf **Weiter**.
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **AzureSqlDatabaseLinkedService** ein.
    2. Wählen Sie im Feld **Servername** Ihren Server aus.
    3. Wählen Sie im Feld **Datenbankname** Ihre Datenbank aus.
    4. Geben Sie im Feld **Benutzername** den Namen des Benutzers ein.
    5. Geben Sie im Feld **Kennwort** das Kennwort für den Benutzer ein.
    6. Klicken Sie auf **Verbindung testen**, um die Verbindung zu testen.
    7. Klicken Sie auf **Speichern**, um den verknüpften Dienst zu speichern.

       ![Mit Azure SQL-Datenbank verknüpfter Dienst – Einstellungen](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, die für die Datenquelle, das Datenziel und den Ort zum Speichern der SYS_CHANGE_VERSION stehen.

### <a name="create-a-dataset-to-represent-source-data"></a>Erstellen eines Datasets zum Darstellen von Quelldaten
In diesem Schritt erstellen Sie ein Dataset, das für die Quelldaten steht.

1. Klicken Sie in der Strukturansicht auf **+** (Pluszeichen) und dann auf **Dataset**.

   ![Menü „Neues Dataset“](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Wählen Sie **Azure SQL-Datenbank**, und klicken Sie auf **Fertig stellen**.

   ![Quelldatasettyp – Azure SQL-Datenbank](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. Eine neue Registerkarte zum Konfigurieren des Datasets wird angezeigt. Außerdem wird das Dataset in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen des Datasets in **SourceDataset**.

   ![Name des Quelldatasets](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Wechseln Sie zur Registerkarte **Verbindung**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService**.
    2. Wählen Sie unter **Tabelle** die Option **[dbo].[data_source_table]** .

   ![Quellverbindung](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Erstellen Sie ein Dataset zum Darstellen von Daten, die in den Senkendatenspeicher kopiert werden.
In diesem Schritt erstellen Sie ein Dataset, das für die Daten steht, die aus dem Quelldatenspeicher kopiert werden. Sie haben den Container „adftutorial“ in Ihrer Azure Blob Storage-Instanz erstellt, als Sie die Voraussetzungen erfüllt haben. Erstellen Sie den Container, wenn er noch nicht vorhanden ist (oder) geben Sie den Namen eines bereits vorhandenen ein. In diesem Tutorial wird der Name der Ausgabedatei dynamisch mit dem Ausdruck `@CONCAT('Incremental-', pipeline().RunId, '.txt')` generiert.

1. Klicken Sie in der Strukturansicht auf **+** (Pluszeichen) und dann auf **Dataset**.

   ![Menü „Neues Dataset“](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Wählen Sie **Azure Blob Storage**, und klicken Sie auf **Fertig stellen**.

   ![Senkendatasettyp – Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. Eine neue Registerkarte zum Konfigurieren des Datasets wird angezeigt. Außerdem wird das Dataset in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen des Datasets in **SinkDataset**.

   ![Senkendataset – Name](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Wechseln Sie im Eigenschaftenfenster zur Registerkarte **Verbindung**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureStorageLinkedService**.
    2. Geben Sie für den Teil **folder** von **filePath** die Zeichenfolge **adftutorial/incchgtracking** ein.
    3. Geben Sie für den Teil **file** von **filePath** die Zeichenfolge **\@CONCAT('Incremental-', pipeline().RunId, '.txt')** ein.  

       ![Senkendataset – Verbindung](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Erstellen eines Datasets zum Darstellen von Daten der Änderungsnachverfolgung
In diesem Schritt erstellen Sie ein Dataset zum Speichern der Version für die Änderungsnachverfolgung.  Sie haben die Tabelle „table_store_ChangeTracking_version“ während der Erfüllung der Voraussetzungen erstellt.

1. Klicken Sie in der Strukturansicht auf **+** (Pluszeichen) und dann auf **Dataset**.
2. Wählen Sie **Azure SQL-Datenbank**, und klicken Sie auf **Fertig stellen**.
3. Eine neue Registerkarte zum Konfigurieren des Datasets wird angezeigt. Außerdem wird das Dataset in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen des Datasets in **ChangeTrackingDataset**.
4. Wechseln Sie zur Registerkarte **Verbindung**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService**.
    2. Wählen Sie unter **Tabelle** die Option **[dbo].[table_store_ChangeTracking_version]** .

## <a name="create-a-pipeline-for-the-full-copy"></a>Erstellen einer Pipeline für den vollständigen Kopiervorgang
In diesem Schritt erstellen Sie eine Pipeline mit einer Kopieraktivität, bei der die gesamten Daten aus dem Quelldatenspeicher (Azure SQL-Datenbank) in den Zieldatenspeicher (Azure Blob Storage) kopiert werden.

1. Klicken Sie im Bereich auf der linken Seite auf **+** (Pluszeichen) und dann auf **Pipeline**.

    ![Screenshot der Option „Pipeline“ für eine Data Factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. Eine neue Registerkarte zum Konfigurieren der Pipeline wird angezeigt. Außerdem wird die Pipeline in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen der Pipeline in **FullCopyPipeline**.

    ![Screenshot einer Pipeline mit eingegebenem Namen](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Datenfluss**, und ziehen Sie die **Copy**-Aktivität in die Oberfläche des Pipeline-Designers. Legen Sie den Namen auf **FullCopyActivity** fest.

    ![Name für vollständige Copy-Aktivität](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Wechseln Sie zur Registerkarte **Quelle**, und wählen Sie im Feld **Source Dataset** (Quelldataset) die Option **SourceDataset**.

    ![Copy-Aktivität – Quelle](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Wechseln Sie zur Registerkarte **Senke**, und wählen Sie im Feld **Sink Dataset** (Senkendataset) die Option **SinkDataset**.

    ![Copy-Aktivität – Senke](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Klicken Sie zum Überprüfen der Pipelinedefinition in der Symbolleiste auf **Überprüfen**. Vergewissern Sie sich, dass keine Validierungsfehler vorliegen. Schließen Sie den **Pipeline Validation Report** (Pipelineüberprüfungsbericht), indem Sie auf **>>** klicken.

    ![Überprüfen der Pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Klicken Sie zum Veröffentlichen von Entitäten (verknüpfte Dienste, Datasets und Pipelines) auf **Veröffentlichen**. Warten Sie, bis die Veröffentlichung erfolgreich durchgeführt wurde.

    ![Screenshot der Data Factory mit hervorgehobener Schaltfläche „Alle veröffentlichen“](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird.

    ![Veröffentlichung erfolgreich](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Sie können auch Benachrichtigungen anzeigen, indem Sie links auf die Schaltfläche **Benachrichtigungen anzeigen** klicken. Klicken Sie zum Schließen des Fensters mit den Benachrichtigungen auf **X**.

    ![Anzeigen von Benachrichtigungen](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Ausführen der vollständigen Kopierpipeline
Klicken Sie auf der Symbolleiste der Pipeline auf **Trigger** und dann auf **Trigger Now** (Jetzt auslösen).

![Screenshot der im Menü „Trigger“ ausgewählten Option zum sofortigen Auslösen](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Überwachen der vollständigen Kopierpipeline

1. Klicken Sie links auf die Registerkarte **Überwachen**. Die Pipelineausführung wird in der Liste mit ihrem Status angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. Mit den Links in der Spalte „Aktionen“ können Sie Aktivitätsausführungen anzeigen, die der Pipelineausführung zugeordnet sind, und die Pipeline erneut ausführen.

    ![Screenshot der Pipelineausführungen für eine Data Factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Wenn Sie mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzeigen möchten, klicken Sie in der Spalte **Aktionen** auf den Link **View Activity Runs** (Aktivitätsausführungen anzeigen). Da die Pipeline nur eine Aktivität enthält, wird in der Liste nur ein Eintrag angezeigt. Klicken Sie auf **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln.

    ![Screenshot der Aktivitätsausführungen für eine Data Factory mit hervorgehobenem Link „Pipelines“](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Überprüfen der Ergebnisse
Im Ordner `incchgtracking` des Containers `adftutorial` wird eine Datei mit dem Namen `incremental-<GUID>.txt` angezeigt.

![Ausgabedatei des vollständigen Kopiervorgangs](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

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

1. Wechseln Sie auf der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**. Klicken Sie im Bereich auf der linken Seite auf **+** (Pluszeichen) und dann auf **Pipeline**.

    ![Screenshot der Erstellung einer Pipeline in einer Data Factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. Eine neue Registerkarte zum Konfigurieren der Pipeline wird angezeigt. Außerdem wird die Pipeline in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen der Pipeline in **IncrementalCopyPipeline**.

    ![Pipelinename](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein**, und ziehen Sie die **Lookup**-Aktivität auf die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **LookupLastChangeTrackingVersionActivity** fest. Mit dieser Aktivität wird die Version für die Änderungsnachverfolgung des letzten Kopiervorgangs abgerufen, die in der Tabelle **table_store_ChangeTracking_version** gespeichert ist.

    ![Screenshot einer Pipeline mit einer Lookup-Aktivität](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Wechseln Sie im **Eigenschaftenfenster** zu **Einstellungen**, und wählen Sie im Feld **Source Dataset** (Quelldataset) die Option **ChangeTrackingDataset**.

    ![Screenshot der Registerkarte „Einstellungen“ im Fenster „Eigenschaften“](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Ziehen Sie die **Lookup**-Aktivität aus der Toolbox **Aktivitäten** in die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **LookupCurrentChangeTrackingVersionActivity** fest. Mit dieser Aktivität wird die aktuelle Version der Änderungsnachverfolgung abgerufen.

    ![Screenshot einer Pipeline mit zwei Lookup-Aktivitäten](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Wechseln Sie im **Eigenschaftenfenster** zu **Einstellungen**, und führen Sie die folgenden Schritte aus:

   1. Wählen Sie im Feld **Source Dataset** (Quelldataset) die Option **SourceDataset**.
   2. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage**.
   3. Geben Sie unter **Abfrage** die folgende SQL-Abfrage ein:

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![Screenshot einer auf der Registerkarte „Einstellungen“ im Fenster „Eigenschaften“ hinzugefügten Abfrage](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Datenfluss**, und ziehen Sie die **Copy**-Aktivität in die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **IncrementalCopyActivity** fest. Mit dieser Aktivität werden die Daten, die zwischen der letzten Version und der aktuellen Version der Änderungsnachverfolgung angefallen sind, in den Zieldatenspeicher kopiert.

    ![Copy-Aktivität – Name](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Wechseln Sie im **Eigenschaftenfenster** zur Registerkarte **Quelle**, und führen Sie die folgenden Schritte aus:

   1. Wählen Sie unter **Source Dataset** (Quelldataset) die Option **SourceDataset**.
   2. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage**.
   3. Geben Sie unter **Abfrage** die folgende SQL-Abfrage ein:

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      ![Copy-Aktivität – Quelleinstellungen](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Wechseln Sie zur Registerkarte **Senke**, und wählen Sie im Feld **Sink Dataset** (Senkendataset) die Option **SinkDataset**.

    ![Copy-Aktivität – Senkeneinstellungen](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **Verbinden Sie beide Lookup-Aktivitäten nacheinander mit der Copy-Aktivität**. Ziehen Sie die **grüne** Schaltfläche, die der **Lookup**-Aktivität zugeordnet ist, auf die **Copy**-Aktivität.

    ![Verbinden von Lookup- und Copy-Aktivität](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Ziehen Sie die **Stored Procedure**-Aktivität aus der Toolbox **Aktivitäten** in die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **StoredProceduretoUpdateChangeTrackingActivity** fest. Mit dieser Aktivität wird die Version der Änderungsnachverfolgung in der Tabelle **table_store_ChangeTracking_version** geändert.

    ![Stored Procedure-Aktivität – Name](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Wechseln Sie zur Registerkarte *SQL-Konto*\*, und wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlDatabaseLinkedService**.

    ![Stored Procedure-Aktivität – SQL-Konto](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Wechseln Sie zur Registerkarte **Gespeicherte Prozedur**, und führen Sie die folgenden Schritte aus:

    1. Wählen Sie unter **Name der gespeicherten Prozedur** den Namen **Update_ChangeTracking_Version**.  
    2. Wählen Sie die Option **Import parameter** (Importparameter).
    3. Geben Sie im Abschnitt **Parameter der gespeicherten Prozedur** die folgenden Werte für den Parameter an:

        | Name | type | Wert |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | String | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        ![Stored Procedure-Aktivität – Parameter](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Verbinden Sie die Copy-Aktivität mit der Stored Procedure-Aktivität**. Ziehen Sie die **grüne** Schaltfläche, die der Copy-Aktivität zugeordnet ist, auf die Stored Procedure-Aktivität.

    ![Verbinden von Copy- und Stored Procedure-Aktivitäten](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Klicken Sie in der Symbolleiste auf **Überprüfen**. Vergewissern Sie sich, dass keine Validierungsfehler vorliegen. Schließen Sie das Fenster **Pipeline Validation Report** (Pipelineüberprüfungsbericht), indem Sie auf **>>** klicken.

    ![Schaltfläche „Überprüfen“](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. Veröffentlichen Sie Entitäten (verknüpfte Dienste, Datasets und Pipelines) für den Data Factory-Dienst, indem Sie auf die Schaltfläche **Alle veröffentlichen** klicken. Warten Sie, bis die Meldung **Veröffentlichung erfolgreich** angezeigt wird.

       ![Screenshot der Schaltfläche „Alle veröffentlichen“ für eine Data Factory](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Ausführen der inkrementellen Kopierpipeline
1. Klicken Sie auf der Symbolleiste der Pipeline auf **Trigger** und dann auf **Trigger Now** (Jetzt auslösen).

    ![Screenshot einer Pipeline mit Aktivitäten und der im Menü „Trigger“ ausgewählten Option zum sofortigen Auslösen](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. Wählen Sie im Fenster **Pipelineausführung** die Option **Fertig stellen** aus.

### <a name="monitor-the-incremental-copy-pipeline"></a>Überwachen der inkrementellen Kopierpipeline
1. Klicken Sie links auf die Registerkarte **Überwachen**. Die Pipelineausführung wird in der Liste mit ihrem Status angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. Mit den Links in der Spalte **Aktionen** können Sie Aktivitätsausführungen anzeigen, die der Pipelineausführung zugeordnet sind, und die Pipeline erneut ausführen.

    ![Screenshot der Pipelineausführungen für eine Data Factory einschließlich Ihrer Pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Wenn Sie mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzeigen möchten, klicken Sie in der Spalte **Aktionen** auf den Link **View Activity Runs** (Aktivitätsausführungen anzeigen). Da die Pipeline nur eine Aktivität enthält, wird in der Liste nur ein Eintrag angezeigt. Klicken Sie auf **Pipelines**, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln.

    ![Screenshot der Pipelineausführungen für eine Data Factory, von denen mehrere als erfolgreich gekennzeichnet sind](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Überprüfen der Ergebnisse
Die zweite Datei ist im Ordner `incchgtracking` des Containers `adftutorial` enthalten.

![Ausgabedatei des inkrementellen Kopiervorgangs](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

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
> [Kopieren neuer Dateien basierend auf „lastmodifieddate“](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
