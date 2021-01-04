---
title: Inkrementelles Kopieren von Daten mithilfe von Change Data Capture
description: In diesem Tutorial wird eine Azure Data Factory-Pipeline erstellt, um Deltadaten aus einer Tabelle einer Azure SQL Managed Instance-Datenbank inkrementell in Azure Storage zu kopieren.
services: data-factory
ms.author: nihurt
author: hurtn
manager: ''
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: ''
ms.date: 05/04/2020
ms.openlocfilehash: fd9e78b6bc3513f79b05c9522e891d346e3d31a0
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637512"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Inkrementelles Laden von Daten aus Azure SQL Managed Instance in Azure Storage mithilfe von Change Data Capture (CDC)

In diesem Tutorial wird eine Azure Data Factory-Instanz mit einer Pipeline erstellt, die Deltadaten basierend auf Informationen von **Change Data Capture (CDC)** aus der Azure SQL Managed Instance-Quelldatenbank in Azure Blob Storage lädt.  

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten des Quelldatenspeichers
> * Erstellen einer Data Factory.
> * Erstellen Sie verknüpfte Dienste.
> * Erstellen von Quell- und Senkendatasets.
> * Erstellen, Debuggen und Ausführen der Pipeline, um eine Überprüfung auf geänderte Daten durchzuführen
> * Ändern von Daten in der Quelltabelle
> * Abschließen, Ausführen und Überwachen der gesamten Pipeline für inkrementelles Kopieren

## <a name="overview"></a>Übersicht
Die von Datenspeichern wie Azure SQL Managed Instance (MI) und SQL Server unterstützte Change Data Capture-Technologie kann zur Identifizierung geänderter Daten verwendet werden.  In diesem Tutorial erfahren Sie, wie Sie Azure Data Factory zusammen mit Change Data Capture-Technologie für SQL verwenden, um Deltadaten inkrementell aus Azure SQL Managed Instance in Azure Blob Storage zu laden.  Konkretere Informationen zur Change Data Capture-Technologie für SQL finden Sie unter [Über Change Data Capture (SQL Server)](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Kompletter Workflow
Hier finden Sie die typischen Schritte des End-to-End-Workflows für das inkrementelle Laden von Daten mithilfe der Change Data Capture-Technologie.

> [!NOTE]
> Die Change Data Capture-Technologie wird sowohl von Azure SQL Managed Instance als auch von SQL Server unterstützt. In diesem Tutorial wird Azure SQL Managed Instance als Quelldatenspeicher verwendet. Sie können auch eine lokale SQL Server-Instanz verwenden.

## <a name="high-level-solution"></a>Allgemeine Lösung
In diesem Tutorial wird eine Pipeline für folgende Vorgänge erstellt:  

   1. Erstellen einer **Lookup-Aktivität** , um die Anzahl geänderter Datensätze in der CDC-Tabelle der SQL-Datenbank zu ermitteln und an eine Aktivität vom Typ „If-Bedingung“ zu übergeben
   2. Erstellen einer **If-Bedingung** , um zu überprüfen, ob geänderte Datensätze vorhanden sind, und die Kopieraktivität aufzurufen, wenn dies der Fall ist
   3. Erstellen einer **Kopieraktivität** , um die eingefügten/aktualisierten/gelöschten Daten aus der CDC-Tabelle in Azure Blob Storage zu kopieren

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
* **Verwaltete Azure SQL-Datenbank-Instanz** . Sie verwenden die Datenbank als den **Quell** -Datenspeicher. Falls Sie noch nicht über eine verwaltete Azure SQL-Datenbank-Instanz verfügen, erfahren Sie im Artikel [Schnellstart: Erstellen einer verwalteten Azure SQL-Instanz](../azure-sql/managed-instance/instance-create-quickstart.md), wie Sie eine solche Instanz erstellen.
* **Azure Storage-Konto** . Sie verwenden den Blob Storage als den **Senken** -Datenspeicher. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos. Erstellen Sie einen Container mit dem Namen **raw** . 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Erstellen einer Datenquellentabelle in Azure SQL-Datenbank

1. Starten Sie **SQL Server Management Studio** , und stellen Sie eine Verbindung mit Ihrem Azure SQL Managed Instance-Server her.
2. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre **Datenbank** , und wählen Sie **Neue Abfrage** .
3. Führen Sie den folgenden SQL-Befehl für Ihre Azure SQL Managed Instance-Datenbank aus, um eine Tabelle mit dem Namen `customers` als Quelldatenspeicher zu erstellen:  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Führen Sie die folgende SQL-Abfrage aus, um den Mechanismus **Change Data Capture** für Ihre Datenbank und die Quelltabelle (customers) zu aktivieren:

    > [!NOTE]
    > - Ersetzen Sie „&lt;your source schema name&gt;“ durch das Schema Ihrer Azure SQL Managed Instance-Instanz, die die Tabelle „customers“ enthält.
    > - Change Data Capture ist nicht an den Transaktionen beteiligt, mit denen die überwachte Tabelle geändert wird. Stattdessen werden Einfüge-, Aktualisierungs- und Löschvorgänge in das Transaktionsprotokoll geschrieben. Damit die Datenmenge in Änderungstabellen nicht auf eine unüberschaubare Größe anwächst, müssen die Daten regelmäßig und systematisch gekürzt werden. Weitere Informationen finden Sie unter [Aktivieren und Deaktivieren von Change Data Capture (SQL Server)](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?enable-change-data-capture-for-a-database=&view=sql-server-ver15).

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Führen Sie den folgenden Befehl aus, um Daten in die Tabelle „customers“ einzufügen:

    ```sql
     insert into customers 
        (customer_id, first_name, last_name, email, city) 
     values 
        (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
        (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Änderungen, die vor der Change Data Capture-Aktivierung an der Tabelle vorgenommen wurden, werden nicht erfasst.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome** . Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Daten + Analysen** > **Data Factory** aus:

   ![Auswählen von „Data Factory“ im Bereich „Neu“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

     ![Seite „Neue Data Factory“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Der Name der Azure Data Factory muss **global eindeutig** sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).

    *Der Data Factory-Name „ADFTutorialDataFactory“ ist nicht verfügbar.*
3. Wählen Sie **V2** als **Version** aus.
4. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
5. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:

   1. Wählen Sie die Option **Use existing** (Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
   2. Wählen Sie **Neu erstellen** , und geben Sie den Namen einer Ressourcengruppe ein.   
         
    Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
5. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.
6. Deaktivieren Sie das Kontrollkästchen **GIT aktivieren** .     
7. Klicken Sie auf **Erstellen** .
8. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln** .

   ![Screenshot mit einer Meldung, dass die Bereitstellung abgeschlossen ist, und einer Option zum Wechseln zur Ressource](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.

   ![Screenshot der von Ihnen bereitgestellten Data Factory](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Klicken Sie auf die Kachel **Erstellen und überwachen** , um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.
11. Wechseln Sie im linken Bereich der Seite **Erste Schritte** zur Registerkarte **Bearbeiten** , wie in der folgenden Abbildung gezeigt:

    ![Schaltfläche „Create pipeline“ (Pipeline erstellen)](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Abschnitt werden Dienste erstellt, die mit Ihrem Azure Storage-Konto und mit Azure SQL Managed Instance verknüpft sind.

### <a name="create-azure-storage-linked-service"></a>Erstellen des verknüpften Azure Storage-Diensts.
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit der Data Factory.

1. Klicken Sie auf **Verbindungen** und dann auf **+ Neu** .

   ![Schaltfläche für eine neue Verbindung](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Azure Blob Storage** , und klicken Sie dann auf **Weiter** .

   ![Auswählen von „Azure Blob Storage“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

   1. Geben Sie unter **Name** die Zeichenfolge **AzureStorageLinkedService** ein.
   2. Wählen Sie unter **Speicherkontoname** Ihr Azure Storage-Konto aus.
   3. Klicken Sie auf **Speichern** .

   ![Einstellungen für Azure-Speicherkonto](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Erstellen eines mit der Azure SQL Managed Instance-Datenbank verknüpften Diensts
In diesem Schritt wird Ihre Azure SQL Managed Instance-Datenbank mit der Data Factory verknüpft.

> [!NOTE]
> Bei Verwendung von SQL Managed Instance finden Sie [hier](./connector-azure-sql-managed-instance.md#prerequisites) Informationen zum Zugriff über einen öffentlichen oder über einen privaten Endpunkt. Bei Verwendung eines privaten Endpunkts muss diese Pipeline unter Verwendung einer selbstgehosteten Integration Runtime ausgeführt werden. Gleiches gilt, wenn SQL Server lokal oder in einem VM- oder VNET-Szenario ausgeführt wird.

1. Klicken Sie auf **Verbindungen** und dann auf **+ Neu** .
2. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Verwaltete Azure SQL-Datenbank-Instanz** aus, und klicken Sie anschließend auf **Weiter** .
3. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus:

   1. Geben Sie im Feld **Name** den Namen **AzureSqlMI1** ein.
   2. Wählen Sie im Feld **Servername** Ihre SQL Server-Instanz aus.
   4. Wählen Sie im Feld **Datenbankname** Ihre SQL-Datenbank aus.
   5. Geben Sie im Feld **Benutzername** den Namen des Benutzers ein.
   6. Geben Sie im Feld **Kennwort** das Kennwort für den Benutzer ein.
   7. Klicken Sie auf **Verbindung testen** , um die Verbindung zu testen.
   8. Klicken Sie auf **Speichern** , um den verknüpften Dienst zu speichern.

   ![Mit der Azure SQL Managed Instance-Datenbank verknüpfter Dienst: Einstellungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt werden Datasets erstellt, um die Datenquelle und das Datenziel darzustellen.

### <a name="create-a-dataset-to-represent-source-data"></a>Erstellen eines Datasets zum Darstellen von Quelldaten
In diesem Schritt erstellen Sie ein Dataset, das für die Quelldaten steht.

1. Klicken Sie in der Strukturansicht auf **+** (Pluszeichen) und dann auf **Dataset** .

   ![Menü „Neues Dataset“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Wählen Sie **Verwaltete Azure SQL-Datenbank-Instanz** aus, und klicken Sie anschließend auf **Weiter** .

   ![Quelldatasettyp – Azure SQL-Datenbank](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. Legen Sie auf der Registerkarte **Eigenschaften festlegen** den Datasetnamen und die Verbindungsinformationen fest:
 
   1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureSqlMI1** aus.
   2. Wählen Sie unter **Tabellenname** die Option **[dbo].[dbo_customers_CT]** aus.  Hinweis: Diese Tabelle wurde automatisch erstellt, als CDC für die Tabelle „customers“ aktiviert wurde. Geänderte Daten werden nie direkt aus dieser Tabelle abgefragt, sondern über die [Change Data Capture-Funktionen](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql?view=sql-server-ver15) extrahiert.

   ![Quellverbindung](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Erstellen Sie ein Dataset zum Darstellen von Daten, die in den Senkendatenspeicher kopiert werden.
In diesem Schritt erstellen Sie ein Dataset, das für die Daten steht, die aus dem Quelldatenspeicher kopiert werden. Den Data Lake-Container haben Sie im Rahmen der Vorbereitung in Ihrer Azure Blob Storage-Instanz erstellt. Erstellen Sie den Container, wenn er noch nicht vorhanden ist (oder) geben Sie den Namen eines bereits vorhandenen ein. In diesem Tutorial wird der Name der Ausgabedatei dynamisch unter Verwendung der (später konfigurierten) Auslösungszeit generiert.

1. Klicken Sie in der Strukturansicht auf **+** (Pluszeichen) und dann auf **Dataset** .

   ![Menü „Neues Dataset“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Wählen Sie **Azure Blob Storage** aus, und klicken Sie auf **Weiter** .

   ![Senkendatasettyp – Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Wählen Sie **DelimitedText** aus, und klicken Sie auf **Weiter** .

   ![Format des Senkendatasets: DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. Legen Sie auf der Registerkarte **Eigenschaften festlegen** den Datasetnamen und die Verbindungsinformationen fest:

   1. Wählen Sie unter **Verknüpfter Dienst** die Option **AzureStorageLinkedService** .
   2. Geben Sie unter **Dateipfad** für **Container** die Zeichenfolge **raw** ein.
   3. Aktivieren Sie das Kontrollkästchen **First row as header** (Erste Zeile als Header).
   4. Klicken Sie auf **OK** .

   ![Senkendataset – Verbindung](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Erstellen einer Pipeline zum Kopieren der geänderten Daten
In diesem Schritt wird eine Pipeline erstellt, die zunächst mithilfe einer **Lookup-Aktivität** überprüft, wie viele geänderte Datensätze in der Änderungstabelle vorhanden sind. Durch eine Aktivität vom Typ „If-Bedingung“ wird geprüft, ob die Anzahl geänderter Datensätze größer Null ist, und eine **Kopieraktivität** wird ausgeführt, um die eingefügten/aktualisierten/gelöschten Daten aus Azure SQL-Datenbank in Azure Blob Storage zu kopieren. Abschließend wird ein Trigger für ein rollierendes Fenster konfiguriert, und die Start- und Endzeiten werden als Parameter für den Start und das Ende des Fensters an die Aktivitäten übergeben. 

1. Wechseln Sie auf der Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten** . Klicken Sie im Bereich auf der linken Seite auf **+** (Pluszeichen) und dann auf **Pipeline** .

    ![Menü für neue Pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. Eine neue Registerkarte zum Konfigurieren der Pipeline wird angezeigt. Außerdem wird die Pipeline in der Strukturansicht angezeigt. Ändern Sie im **Eigenschaftenfenster** den Namen der Pipeline in **IncrementalCopyPipeline** .

    ![Pipelinename](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein** , und ziehen Sie die **Lookup** -Aktivität auf die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **GetChangeCount** fest. Durch diese Aktivität wird die Anzahl von Datensätzen in der Änderungstabelle für ein bestimmtes Zeitfenster abgerufen.

    ![Lookup-Aktivität – Name](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Wechseln Sie im Fenster **Eigenschaften** zu **Einstellungen** :
   1. Geben Sie im Feld **Source Dataset** (Quelldataset) den Namen des SQL Managed Instance-Datasets an.
   2. Wählen Sie die Option „Abfrage“ aus, und geben Sie Folgendes in das Abfragefeld ein:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Aktivieren Sie das Kontrollkästchen **First row only** (Nur erste Zeile).

    ![Lookup-Aktivität – Einstellungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Klicken Sie auf die Schaltfläche **Datenvorschau** , um sich zu vergewissern, dass durch die Lookup-Aktivität eine gültige Ausgabe abgerufen wird.

    ![Lookup-Aktivität: Vorschau](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Iteration & Conditionals** (Iteration und Bedingungen), und ziehen Sie die Aktivität **If-Bedingung** auf die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **HasChangedRows** fest. 

    ![Aktivität „If-Bedingung“: Name](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Wechseln Sie im Fenster **Eigenschaften** zu **Aktivitäten** :

   1. Geben Sie unter **Ausdruck** Folgendes ein:
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Klicken Sie auf das Stiftsymbol, um die True-Bedingung zu bearbeiten.

   ![Aktivität „If-Bedingung“: Einstellungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein** , und ziehen Sie eine Aktivität vom Typ **Warten** auf die Oberfläche des Pipeline-Designers. Hierbei handelt es sich um eine temporäre Aktivität zum Debuggen der If-Bedingung; sie wird später in diesem Tutorial noch geändert. 

   ![If-Bedingung „True“: Warten](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Klicken Sie auf der Breadcrumb-Leiste auf „IncrementalCopyPipeline“, um zur Hauptpipeline zurückzukehren.

8. Führen Sie die Pipeline im Modus **Debuggen** aus, um sich zu vergewissern, dass die Ausführung erfolgreich ist. 

   ![Pipeline: Debuggen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Kehren Sie als Nächstes zum Schritt für die True-Bedingung zurück, und löschen Sie die Aktivität **Warten** . Erweitern Sie in der Toolbox **Aktivitäten** die Option **Move & transform** (Verschieben und transformieren), und ziehen Sie eine Aktivität vom Typ **Copy** auf die Oberfläche des Pipeline-Designers. Legen Sie den Namen der Aktivität auf **IncrementalCopyActivity** fest. 

   ![Copy-Aktivität – Name](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Wechseln Sie im **Eigenschaftenfenster** zur Registerkarte **Quelle** , und führen Sie die folgenden Schritte aus:

   1. Geben Sie im Feld **Source Dataset** (Quelldataset) den Namen des SQL Managed Instance-Datasets an. 
   2. Wählen Sie unter **Abfrage verwenden** die Option **Abfrage** .
   3. Geben Sie unter **Abfrage** Folgendes ein:

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Copy-Aktivität – Quelleinstellungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Klicken Sie auf die Vorschauoption, um sich zu vergewissern, dass die geänderten Zeilen durch die Abfrage korrekt zurückgegeben werden.

    ![Screenshot der Vorschau zum Überprüfen der Abfrage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Wechseln Sie zur Registerkarte **Senke** , und geben Sie im Feld **Sink Dataset** (Senkendataset) das Azure Storage-Dataset an.

    ![Screenshot der Registerkarte „Senke“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Kehren Sie zur Canvas der Hauptpipeline zurück, und verbinden Sie nacheinander die Aktivität **Lookup** mit der Aktivität **If-Bedingung** . Ziehen Sie die **grüne** Schaltfläche der Aktivität **Lookup** zur Aktivität **If-Bedingung** .

    ![Verbinden von Lookup- und Copy-Aktivität](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Klicken Sie in der Symbolleiste auf **Überprüfen** . Vergewissern Sie sich, dass keine Validierungsfehler vorliegen. Schließen Sie das Fenster **Pipeline Validation Report** (Pipelineüberprüfungsbericht), indem Sie auf **>>** klicken.

    ![Schaltfläche „Überprüfen“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Klicken Sie auf „Debuggen“, um die Pipeline zu testen und sich zu vergewissern, dass am Speicherort eine Datei generiert wird.

    ![Debuggen der inkrementellen Pipeline: 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Veröffentlichen Sie Entitäten (verknüpfte Dienste, Datasets und Pipelines) für den Data Factory-Dienst, indem Sie auf die Schaltfläche **Alle veröffentlichen** klicken. Warten Sie, bis die Meldung **Veröffentlichung erfolgreich** angezeigt wird.

    ![Schaltfläche "Veröffentlichen"](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Konfigurieren des Triggers für ein rollierendes Fenster und der CDC-Fensterparameter 
In diesem Schritt wird ein Trigger für ein rollierendes Fenster erstellt, um den Auftrag nach einem kurz getakteten Zeitplan auszuführen. Hierbei werden die Systemvariablen „WindowStart“ und „WindowEnd“ des Triggers für das rollierende Fenster als Parameter an die Pipeline übergeben, um sie in der CDC-Abfrage zu verwenden.

1. Navigieren Sie zur Registerkarte **Parameter** der Pipeline **IncrementalCopyPipeline** , und fügen Sie der Pipeline mithilfe der Schaltfläche **+ Neu** zwei Parameter ( **triggerStartTime** und **triggerEndTime** ) hinzu, die die Start- und Endzeit des rollierenden Fensters darstellen. Fügen Sie zu Debuggingzwecken Standardwerte im Format **JJJJ-MM-TT HH24:MI:SS.FFF** hinzu. Achten Sie jedoch darauf, dass die Startzeit des Triggers (triggerStartTime) nicht vor der CDC-Aktivierung für die Tabelle liegt, da sonst ein Fehler auftritt.

    ![Menü „Trigger Now“ (Jetzt auslösen)](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Klicken Sie auf die Registerkarte mit den Einstellungen der Aktivität **Lookup** , und konfigurieren Sie die Abfrage für die Verwendung des Start- und Endparameters. Kopieren Sie Folgendes in die Abfrage:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Navigieren Sie zur Aktivität **Copy** (im Fall „True“ der Aktivität **If-Bedingung** ), und klicken Sie auf die Registerkarte **Quelle** . Kopieren Sie Folgendes in die Abfrage:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Klicken Sie auf die Registerkarte **Senke** der Aktivität **Copy** und anschließend auf **Öffnen** , um die Dataseteigenschaften zu bearbeiten. Klicken Sie auf die Registerkarte **Parameter** , und fügen Sie einen neuen Parameter namens **triggerStart** hinzu.    

    ![Screenshot des Hinzufügens eines neuen Parameters zur Registerkarte „Parameter“](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Konfigurieren Sie als Nächstes die Dataseteigenschaften, um die Daten mit datumsbasierten Partitionen in einem Unterverzeichnis von **customers/incremental** zu speichern.
   1. Klicken Sie in den Dataseteigenschaften auf die Registerkarte **Verbindung** , und fügen Sie dynamische Inhalte für die Abschnitte **Verzeichnis** und **Datei** hinzu. 
   2. Geben Sie im Abschnitt **Verzeichnis** den folgenden Ausdruck ein, indem Sie unter dem Textfeld auf den Link für dynamischen Inhalt klicken:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Geben Sie im Abschnitt **Datei** den folgenden Ausdruck ein. Dadurch werden Dateinamen auf der Grundlage des Startdatums und der Startzeit des Triggers erstellt und mit dem Suffix „.csv“ versehen:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Konfiguration des Senkendatasets: 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Klicken Sie auf die Registerkarte **IncrementalCopyPipeline** , um zu den Einstellungen für die **Senke** in der Aktivität **Copy** zurückzukehren. 
   5. Erweitern Sie die Dataseteigenschaften, und geben Sie im triggerStart-Parameterwert dynamischen Inhalt mit dem folgenden Ausdruck ein:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Konfiguration des Senkendatasets: 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Klicken Sie auf „Debuggen“, um die Pipeline zu testen und sich zu vergewissern, dass die Ordnerstruktur und die Ausgabedatei erwartungsgemäß generiert werden. Laden Sie die Datei herunter, und öffnen Sie sie, um den Inhalt zu überprüfen. 

    ![Debuggen des inkrementellen Kopierens: 3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Vergewissern Sie sich, dass die Parameter in die Abfrage eingefügt werden, indem Sie die Eingabeparameter der Pipelineausführung überprüfen.

    ![Debuggen des inkrementellen Kopierens: 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Veröffentlichen Sie Entitäten (verknüpfte Dienste, Datasets und Pipelines) für den Data Factory-Dienst, indem Sie auf die Schaltfläche **Alle veröffentlichen** klicken. Warten Sie, bis die Meldung **Veröffentlichung erfolgreich** angezeigt wird.
9. Konfigurieren Sie abschließend einen Trigger für ein rollierendes Fenster, um die Pipeline in regelmäßigen Abständen auszuführen, und legen Sie Parameter für Start- und Endzeit fest. 
   1. Klicken Sie auf die Schaltfläche **Trigger hinzufügen** , und wählen Sie **Neu/Bearbeiten** aus.

   ![Hinzufügen eines neuen Triggers](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Geben Sie einen Namen für den Trigger und eine Startzeit an, die der Endzeit des obigen Debugfensters entspricht.

   ![Trigger für ein rollierendes Fenster](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. Geben Sie im nächsten Bildschirm die folgenden Werte für den Start- bzw. Endparameter an:
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Trigger für ein rollierendes Fenster: 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Beachten Sie, dass der Trigger erst ausgeführt wird, nachdem er veröffentlicht wurde. Das erwartete Verhalten des rollierenden Fensters besteht außerdem in der Ausführung aller historischen Intervalle seit dem Startdatum bis zum aktuellen Zeitpunkt. Weitere Informationen zu Triggern für ein rollierendes Fenster finden Sie [hier](./how-to-create-tumbling-window-trigger.md). 
  
10. Nehmen Sie mithilfe von **SQL Server Management Studio** einige weitere Änderungen an der Kundentabelle vor, indem Sie den folgenden SQL-Code ausführen:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Klicken Sie auf die Schaltfläche **Alle veröffentlichen** . Warten Sie, bis die Meldung **Veröffentlichung erfolgreich** angezeigt wird.  
12. Nach ein paar Minuten wurde die Pipeline ausgelöst, und eine neue Datei wurde in Azure Storage geladen.


### <a name="monitor-the-incremental-copy-pipeline"></a>Überwachen der inkrementellen Kopierpipeline
1. Klicken Sie links auf die Registerkarte **Überwachen** . Die Pipelineausführung wird in der Liste mit ihrem Status angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren** . Zeigen Sie auf eine Stelle in der Nähe des Namens der Pipeline, um auf die Aktion „Erneut ausführen“ und auf den Verbrauchsbericht zuzugreifen.

    ![Pipelineausführungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Klicken Sie auf den Namen der Pipeline, um mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzuzeigen. Wurden geänderte Daten erkannt, stehen drei Aktivitäten zur Verfügung (unter anderem die Copy-Aktivität). Andernfalls enthält die Liste nur zwei Einträge. Klicken Sie im oberen Bereich auf den Link **Alle Pipelines** , um zur Ansicht mit den Pipelineausführungen zurückzukehren.

    ![Aktivitätsausführungen](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Überprüfen der Ergebnisse
Die zweite Datei ist im Ordner `customers/incremental/YYYY/MM/DD` des Containers `raw` enthalten.

![Ausgabedatei des inkrementellen Kopiervorgangs](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Tutorial erfahren Sie mehr über das Kopieren von neuen und geänderten Dateien nur auf Grundlage ihres LastModifiedDate-Werts:

> [!div class="nextstepaction"]
>[Kopieren neuer Dateien basierend auf „lastmodifieddate“](tutorial-incremental-copy-lastmodified-copy-data-tool.md)