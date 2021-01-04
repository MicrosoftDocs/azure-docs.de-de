---
title: Kopieren lokaler Daten mithilfe des Azure-Tools zum Kopieren von Daten
description: Erstellen Sie eine Azure Data Factory-Instanz, und verwenden Sie dann das Tool zum Kopieren von Daten, um Daten aus einer SQL Server-Datenbank in Azure Blob Storage zu kopieren.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: d9cf9729d8be77845572efd9ef6e2486ddceaaaf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002833"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopieren von Daten aus einer SQL Server-Datenbank in Azure Blob Storage mithilfe des Tools zum Kopieren von Daten
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend erstellen Sie mithilfe des Tools zum Kopieren von Daten eine Pipeline, die Daten aus einer SQL Server-Datenbank in Azure Blob Storage kopiert.

> [!NOTE]
> - Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen
### <a name="azure-subscription"></a>Azure-Abonnement
Wenn Sie nicht bereits ein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

### <a name="azure-roles"></a>Azure-Rollen
Damit Sie Data Factory-Instanzen erstellen können, muss dem Benutzerkonto, mit dem Sie sich bei Azure anmelden, die Rolle *Mitwirkender* oder *Besitzer* zugewiesen sein, oder es muss ein *Administrator* des Azure-Abonnements sein.

Die Berechtigungen, über die Sie im Abonnement verfügen, können Sie im Azure-Portal einsehen. Wählen Sie oben rechts Ihren Benutzernamen und dann **Berechtigungen** aus. Wenn Sie Zugriff auf mehrere Abonnements besitzen, wählen Sie das entsprechende Abonnement aus. Beispielanleitungen zum Hinzufügen eines Benutzers zu einer Rolle finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 und 2017
In diesem Tutorial verwenden Sie eine SQL Server-Datenbank als *Quelldatenspeicher*. Die Pipeline in der in diesem Tutorial erstellten Data Factory kopiert Daten aus dieser SQL Server-Datenbank (Quelle) in Blob Storage (Senke). Anschließend erstellen Sie eine Tabelle mit dem Namen **emp** in Ihrer SQL Server-Datenbank und fügen einige Beispieleinträge in die Tabelle ein.

1. Starten Sie SQL Server Management Studio. Falls die Suite auf Ihrem Computer noch nicht installiert wurde, rufen Sie [Herunterladen von SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) auf.

1. Stellen Sie eine Verbindung mit Ihrer SQL Server-Instanz her, indem Sie Ihre Anmeldeinformationen verwenden.

1. Erstellen Sie eine Beispieldatenbank. Klicken Sie in der Strukturansicht mit der rechten Maustaste auf **Datenbanken**, und wählen Sie anschließend **Neue Datenbank**.

1. Geben Sie im Fenster **Neue Datenbank** einen Namen für die Datenbank ein, und wählen Sie dann **OK**.

1. Führen Sie das folgende Abfrageskript für die Datenbank aus, um die Tabelle **emp** zu erstellen und einige Beispieldaten einzufügen. Klicken Sie in der Strukturansicht mit der rechten Maustaste auf die von Ihnen erstellte Datenbank, und wählen Sie anschließend **Neue Abfrage**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Azure-Speicherkonto
Sie verwenden in diesem Tutorial ein Azure Storage-Allzweckkonto (Blob Storage) als Ziel-/Senkendatenspeicher. Falls Sie noch nicht über ein allgemeines Speicherkonto verfügen, erfahren Sie unter [Erstellen Sie ein Speicherkonto.](../storage/common/storage-account-create.md), wie Sie eins erstellen. Die Pipeline in der Data Factory, die Sie in diesem Tutorial erstellen, kopiert Daten aus der SQL Server-Datenbank (Quelle) in diese Blob Storage-Instanz (Senke). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Abrufen des Speicherkontonamens und des Kontoschlüssels
In diesem Tutorial verwenden Sie Name und Schlüssel Ihres Speicherkontos. Den Namen und den Schlüssel Ihres Speicherkontos ermitteln Sie anhand der folgenden Schritte:

1. Melden Sie sich mit Ihrem Azure-Benutzernamen und dem dazugehörigen Kennwort beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Alle Dienste** aus. Filtern Sie nach dem Schlüsselwort **Speicher**, und wählen Sie dann **Speicherkonten** aus.

    ![Speicherkontosuche](media/doc-common-process/search-storage-account.png)

1. Filtern Sie in der Liste mit den Speicherkonten ggf. nach Ihrem Speicherkonto. Wählen Sie dann Ihr Speicherkonto aus.

1. Wählen Sie im Fenster **Speicherkonto** die Option **Zugriffsschlüssel**.


1. Kopieren Sie in den Feldern **Speicherkontoname** und **key1** die Werte, und fügen Sie sie zur späteren Verwendung im Tutorial in einen Editor ein.

#### <a name="create-the-adftutorial-container"></a>Erstellen des Containers „adftutorial“
In diesem Abschnitt erstellen Sie einen Blobcontainer mit dem Namen **adftutorial** in Ihrer Blob Storage-Instanz.

1. Wechseln Sie im Fenster **Speicherkonto** zu **Übersicht**, und wählen Sie die Option **Blobs**.

1. Wählen Sie im Fenster **Blobs** die Option **+ Container** aus.

1. Geben Sie im Fenster **Neuer Container** unter **Name** den Namen **adftutorial** ein, und wählen Sie **OK** aus.

1. Wählen Sie in der Liste mit den Containern die Option **adftutorial**.


1. Lassen Sie das Fenster **Container** für **adftutorial** geöffnet. Sie überprüfen darin am Ende des Tutorials die Ausgabe. Data Factory erstellt den Ausgabeordner automatisch in diesem Container, damit Sie ihn nicht selbst erstellen müssen.


## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

   ![Erstellen einer neuen Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Data Factory muss *global eindeutig* sein. Sollte für das Feld „Name“ die folgende Fehlermeldung angezeigt werden, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“). Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Azure Data Factory – Benennungsregeln](naming-rules.md).

   ![Name der neuen Data Factory](./media/doc-common-process/name-not-available-error.png)
1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

   - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

   - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
        
     Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).
1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt:

     ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)
1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Erste Schritte** auf **Daten kopieren**, um das Tool zum Kopieren von Daten zu starten.

   ![Seite für die ersten Schritte](./media/doc-common-process/get-started-page.png)

1. Geben Sie auf der Seite **Eigenschaften** des Tools zum Kopieren von Daten unter **Aufgabenname** den Namen **CopyFromOnPremSqlToAzureBlobPipeline** ein. Wählen Sie **Weiter** aus. Das Tool zum Kopieren von Daten erstellt eine Pipeline mit dem Namen, den Sie in diesem Feld angeben.
  ![Taskname](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Klicken Sie auf der Seite **Quelldatenspeicher** auf **Neue Verbindung erstellen**.

1. Suchen Sie unter **New Linked Service** (Neuer verknüpfter Dienst) nach **SQL Server**, und wählen Sie dann **Weiter** aus.

1. Geben Sie im Dialogfeld **New Linked Service (SQL Server)** (Neuer verknüpfter Dienst (SQL Server)) unter **Name** den Namen **SqlServerLinkedService** ein. Wählen Sie unter **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) die Option **+Neu**. Sie müssen eine selbstgehostete Integration Runtime erstellen, auf Ihren Computer herunterladen und bei Data Factory registrieren. Die selbstgehostete Integration Runtime kopiert Daten zwischen Ihrer lokalen Umgebung und der Cloud.

1. Wählen Sie im Dialogfeld **Integration Runtime Setup** (Integration Runtime-Setup) die Option **Self-Hosted** (Selbstgehostet) aus. Klicken Sie anschließend auf **Weiter**.

   ![Erstellen von Integrationslaufzeit](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. Geben Sie im Dialogfeld **Integration Runtime Setup** (Integration Runtime-Setup) unter **Name** die Zeichenfolge **TutorialIntegrationRuntime** ein. Klicken Sie anschließend auf **Erstellen**.

1. Wählen Sie im Dialogfeld **Integration Runtime Setup** (Integration Runtime-Setup) die Option **Click here to launch the express setup for this computer** (Klicken Sie hier, um das Express-Setup für diesen Computer zu starten) aus. Dadurch wird die Integration Runtime auf Ihrem Computer installiert und bei Data Factory registriert. Alternativ können Sie die Installationsdatei über die manuelle Setupoption herunterladen, die Datei ausführen und die Integration Runtime mithilfe des Schlüssels registrieren.

1. Führen Sie die heruntergeladene Anwendung aus. Der Status des Express-Setups wird im Fenster angezeigt.

    ![Status des Express-Setups](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Stellen Sie im Dialogfeld **New Linked Service (SQL Server)** (Neuer verknüpfter Dienst (SQL Server)) sicher, dass im Feld „Integration Runtime“ die Option **TutorialIntegrationRuntime** ausgewählt ist. Führen Sie dann die folgenden Schritte aus:

    a. Geben Sie unter **Name** die Zeichenfolge **SqlServerLinkedService** ein.

    b. Geben Sie unter **Servername** den Namen Ihrer SQL Server-Instanz ein.

    c. Geben Sie unter **Datenbankname** den Namen Ihrer lokalen Datenbank ein.

    d. Wählen Sie unter **Authentifizierungstyp** eine geeignete Authentifizierung aus.

    e. Geben Sie unter **Benutzername** den Namen eines Benutzers mit Zugriff auf SQL Server ein.

    f. Geben Sie das **Kennwort** für den Benutzer ein.

    g. Testen Sie die Verbindung, und wählen Sie **Fertig stellen** aus.

      ![Integration Runtime ausgewählt](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Wählen Sie auf der Seite **Quelldatenspeicher** die Option **Weiter** aus.

1. Wählen Sie in der Liste auf der Seite **Select tables from which to copy the data or use a custom query** (Tabellen auswählen, aus denen die Daten kopiert werden sollen, oder benutzerdefinierte Abfrage verwenden) die Tabelle **[dbo].[emp]** aus, und klicken Sie anschließend auf **Weiter**. Basierend auf Ihrer Datenbank können Sie eine beliebige andere Tabelle auswählen.

1. Klicken Sie auf der Seite **Zieldatenspeicher** auf **Neue Verbindung erstellen**.


1. Suchen Sie unter **New Linked Service** (Neuer verknüpfter Dienst) nach **Azure Blob**, wählen Sie das Ergebnis aus, und wählen Sie dann **Weiter** aus.

   ![Auswählen von Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Führen Sie im Dialogfenster **New Linked Service (Azure Blob Storage)** (Neuer verknüpfter Dienst (Azure Blob Storage)) die folgenden Schritte aus:

   a. Geben Sie unter **Name** den Namen **AzureStorageLinkedService** ein.

   b. Wählen Sie unter **Connect via integration runtime** (Verbindung per Integration Runtime herstellen) die Option **TutorialIntegrationRuntime** aus.

   c. Wählen Sie in der Dropdownliste unter **Speicherkontoname** Ihr Speicherkonto aus.

   d. Wählen Sie **Fertig stellen** aus.

1. Stellen Sie im Dialogfeld **Zieldatenspeicher** sicher, dass **Azure Blob Storage** ausgewählt ist. Wählen Sie **Weiter** aus.

1. Geben Sie im Dialogfenster **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) unter **Ordnerpfad** den Pfad **adftutorial/fromonprem** ein. Den Container **adftutorial** haben Sie im Rahmen der Vorbereitung erstellt. Ist der Ausgabeordner (in diesem Fall **fromonprem**) nicht vorhanden, wird er von Data Factory automatisch erstellt. Sie können auch auf die Schaltfläche **Durchsuchen** klicken und zum Blobspeicher und den dazugehörigen Containern/Ordnern navigieren. Wenn Sie unter **Dateiname** keinen Wert angeben, wird standardmäßig der Name aus der Quelle (in diesem Fall **dbo.emp**) verwendet.

   ![Auswählen der Ausgabedatei oder des Ausgabeordners](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Klicken Sie im Dialogfenster **Dateiformateinstellungen** auf **Weiter**.

1. Klicken Sie im Dialogfenster **Einstellungen** auf **Weiter**.

1. Überprüfen Sie im Dialogfenster **Zusammenfassung** die Werte sämtlicher Einstellungen, und klicken Sie anschließend auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen. 

1. Nach Abschluss der Pipelineausführung können Sie den Status der erstellten Pipeline anzeigen. 

1. Wählen Sie auf der Seite „Pipelineausführungen“ die Option **Aktualisieren** aus, um die Liste zu aktualisieren. Klicken Sie auf den Link unter **PIPELINENAME**, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. 

1. Wählen Sie auf der Seite „Aktivitätsausführungen“ unter der Spalte **AKTIVITÄTSNAME** den Link **Details** (Brillensymbol) aus, um weitere Details zum Kopiervorgang anzuzeigen. Wählen Sie im Breadcrumb-Menü den Link **Alle Pipelineausführungen** aus, um zur Ansicht „Pipelineausführungen“ zurückzukehren. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

1. Vergewissern Sie sich, dass die Ausgabedatei im Ordner **fromonprem** des Containers **adftutorial** enthalten ist.

1. Klicken Sie im linken Bereich auf die Registerkarte **Bearbeiten**, um in den Editor-Modus zu wechseln. Sie können die vom Tool erstellten verknüpften Dienste, Datasets und Pipelines mit dem Editor aktualisieren. Klicken Sie auf **Code**, um den JSON-Code für die im Editor geöffnete Entität anzuzeigen. Ausführliche Informationen zum Bearbeiten dieser Entitäten über die Data Factory-Benutzeroberfläche finden Sie in der [Azure-Portal-Version dieses Tutorials](tutorial-copy-data-portal.md).


## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten aus einer SQL Server-Datenbank in Blob Storage. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Eine Liste mit den von Data Factory unterstützten Datenspeichern finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Fahren Sie mit dem folgenden Tutorial fort, um mehr über das Kopieren von Daten per Massenvorgang aus einer Quelle in ein Ziel zu erfahren:

> [!div class="nextstepaction"]
>[Massenkopieren von Daten](tutorial-bulk-copy-portal.md)
