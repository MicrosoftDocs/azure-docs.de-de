---
title: Verwenden des Azure-Portals zum Erstellen einer Data Factory-Pipeline
description: Dieses Tutorial enthält detaillierte Anweisungen zur Erstellung einer Data Factory mit Pipeline mithilfe des Azure-Portals. Die Pipeline verwendet die Kopieraktivität zum Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/11/2020
ms.author: jingwang
ms.openlocfilehash: ef9ac29735289d5c7a60ff0fca3b9e9f360f6e08
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005128"
---
# <a name="copy-data-from-azure-blob-storage-to-a-database-in-azure-sql-database-by-using-azure-data-factory"></a>Kopieren von Daten aus Azure Blob Storage in eine Datenbank in Azure SQL-Datenbank mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Data Factory über die Azure Data Factory-Benutzeroberfläche (User Interface, UI). Die Pipeline in dieser Data Factory kopiert Daten aus Azure Blob Storage in eine Datenbank in Azure SQL-Datenbank. Das Konfigurationsmuster in diesem Tutorial gilt für Kopiervorgänge aus einem dateibasierten Datenspeicher in einen relationalen Datenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

> [!NOTE]
> - Falls Sie noch nicht mit Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mit einer Copy-Aktivität
> * Ausführen eines Testlaufs für die Pipeline
> * Manuelles Auslösen der Pipeline
> * Auslösen der Pipeline nach einem Zeitplan
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**. Sie verwenden Blob Storage als *Quelldatenspeicher*. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos.
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als *Senkendatenspeicher*. Wenn Sie in Azure SQL-Datenbank noch keine Datenbank haben, lesen Sie [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank.

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Nun bereiten Sie Ihre Blob Storage-Instanz und SQL-Datenbank durch Ausführen der folgenden Schritte auf das Tutorial vor:

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

1. Starten Sie den Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn als **emp.txt**-Datei auf einem Datenträger:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Erstellen Sie in Blob Storage einen Container mit dem Namen **adftutorial**. Erstellen Sie einen Ordner namens **input** in diesem Container. Laden Sie anschließend die Datei **emp.txt** in den Ordner **input** hoch. Verwenden Sie für diese Aufgaben das Azure-Portal oder Tools wie [Azure Storage-Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

1. Verwenden Sie das folgende SQL-Skript zum Erstellen der Tabelle **dbo.emp** in Ihrer Datenbank:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

1. Gewähren Sie Azure-Diensten den Zugriff auf SQL Server. Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihre SQL Server-Instanz **aktiviert** ist, damit Data Factory Daten in diese SQL Server-Instanz schreiben kann. Wenn Sie diese Einstellung überprüfen und aktivieren möchten, navigieren Sie auf dem logischen SQL-Server zu „Übersicht“ > „Serverfirewall festlegen“, und legen Sie die Option **Zugriff auf Azure-Dienste zulassen** auf **EIN** fest.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie eine Data Factory und starten die Data Factory-Benutzeroberfläche, um eine Pipeline in der Data Factory zu erstellen.

1. Öffnen Sie **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
2. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus.
3. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Azure Data Factory muss *global eindeutig* sein. Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. (Verwenden Sie beispielsweise „IhrNameADFTutorialDataFactory“.) Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Azure Data Factory – Benennungsregeln](naming-rules.md).

     ![Neue Data Factory](./media/doc-common-process/name-not-available-error.png)
4. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
5. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
         
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md). 
6. Wählen Sie unter **Version** die Option **V2**.
7. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.
8. Klicken Sie auf **Erstellen**.
9. Nach Abschluss der Erstellung wird der Hinweis im Benachrichtigungscenter angezeigt. Wählen Sie **Zu Ressource wechseln** aus, um zur Data Factory-Seite zu navigieren.
10. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.


## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer Copy-Aktivität in der Data Factory. Die Copy-Aktivität kopiert Daten aus Blob Storage in SQL-Datenbank. Im [Schnellstarttutorial](quickstart-create-data-factory-portal.md) haben Sie anhand der folgenden Schritte eine Pipeline erstellt:

1. 1\. Erstellen des verknüpften Diensts
1. Erstellen von Eingabe- und Ausgabedatasets
1. Erstellen einer Pipeline.

In diesem Tutorial beginnen Sie mit dem Erstellen der Pipeline. Verknüpfte Dienste und Datasets erstellen Sie, wenn Sie sie zum Konfigurieren der Pipeline benötigen.

1. Wählen Sie auf der Seite **Erste Schritte** die Option **Pipeline erstellen** aus.

   ![Erstellen der Pipeline](./media/doc-common-process/get-started-page.png)

1. 1. Geben Sie im Bereich „Allgemein“ unter **Eigenschaften** die Eigenschaft **CopyPipeline** für **Name** an. Reduzieren Sie dann den Bereich, indem Sie in der oberen rechten Ecke auf das Symbol „Eigenschaften“ klicken.

1. Erweitern Sie in der Toolbox **Aktivitäten** die Kategorie **Move and Transform** (Verschieben und transformieren), und verschieben Sie die Aktivität **Daten kopieren** aus der Toolbox auf die Oberfläche des Pipeline-Designers. Geben Sie unter **Name** den Namen **CopyFromBlobToSql** ein.

    ![Copy-Aktivität](./media/tutorial-copy-data-portal/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Konfigurieren der Quelle

>[!TIP]
>In diesem Tutorial verwenden Sie *Kontoschlüssel* als Authentifizierungstyp für Ihren Quelldatenspeicher, Sie können bei Bedarf aber auch andere unterstützte Authentifizierungsmethoden auswählen: *SAS-URI*, *Dienstprinzipal* und *Verwaltete Identität*. Ausführlichere Informationen finden Sie in den entsprechenden Abschnitten [dieses Artikels](./connector-azure-blob-storage.md#linked-service-properties).
>Zum sicheren Speichern von Geheimnissen für Datenspeicher empfiehlt sich darüber hinaus die Verwendung einer Azure Key Vault-Instanz. Eine ausführliche Erläuterung finden Sie in [diesem Artikel](./store-credentials-in-key-vault.md).

1. Wechseln Sie zur Registerkarte **Quelle**. Klicken Sie auf **+ Neu**, um ein Quelldataset zu erstellen.

1. Wählen Sie im Dialogfeld **Neues Dataset** die Option **Azure Blob Storage** und dann **Weiter** aus. Da sich die Quelldaten in Blob Storage befinden, wählen Sie **Azure Blob Storage** als Quelldataset aus.

1. Wählen Sie im Dialogfeld **Format auswählen** den Formattyp Ihrer Daten und dann **Weiter** aus.

1. Geben Sie im Dialogfeld **Eigenschaften festlegen** als Name **SourceBlobDataset** ein. Aktivieren Sie das Kontrollkästchen **Erste Zeile als Kopfzeile**. Klicken Sie unter dem Textfeld **Verknüpfter Dienst** auf **+ Neu**.

1. Geben Sie im Dialogfeld **New Linked Service (Azure Blob Storage)** (Neuer verknüpfter Dienst (Azure Blob Storage)) als Name **AzureStorageLinkedService** ein, und wählen Sie in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und klicken Sie dann auf **Erstellen**, um den verknüpften Dienst bereitzustellen.

1. Nach der Erstellung des verknüpften Diensts wird wieder die Seite **Eigenschaften festlegen** angezeigt. Klicken Sie neben **Dateipfad** auf **Durchsuchen**.

1. Navigieren Sie zum Ordner **adftutorial/input**, wählen Sie die Datei **emp.txt** aus, und klicken Sie auf **OK**.

1. Klicken Sie auf **OK**. Die Pipelineseite wird automatisch aufgerufen. Vergewissern Sie sich, dass auf der Registerkarte **Quelle** die Option **SourceBlobDataset** ausgewählt ist. Wenn Sie auf dieser Seite eine Vorschau der Daten anzeigen möchten, klicken Sie auf **Datenvorschau**.

    ![Quelldataset](./media/tutorial-copy-data-portal/source-dataset-selected.png)

### <a name="configure-sink"></a>Konfigurieren der Senke
>[!TIP]
>In diesem Tutorial verwenden Sie *SQL-Authentifizierung* als Authentifizierungstyp für Ihren Senkendatenspeicher, Sie können bei Bedarf aber auch andere unterstützte Authentifizierungsmethoden auswählen: *Dienstprinzipal* und *Verwaltete Identität*. Ausführlichere Informationen finden Sie in den entsprechenden Abschnitten [dieses Artikels](./connector-azure-sql-database.md#linked-service-properties).
>Zum sicheren Speichern von Geheimnissen für Datenspeicher empfiehlt sich darüber hinaus die Verwendung einer Azure Key Vault-Instanz. Eine ausführliche Erläuterung finden Sie in [diesem Artikel](./store-credentials-in-key-vault.md).

1. Wechseln Sie zur Registerkarte **Senke**, und klicken Sie auf **+ Neu**, um ein Senkendataset zu erstellen.

1. Geben Sie im Dialogfeld **Neues Dataset** in das Suchfeld „SQL“ ein, um die Connectors zu filtern. Wählen Sie anschließend **Azure SQL-Datenbank** und dann **Weiter** aus. In diesem Tutorial kopieren Sie Daten in eine SQL-Datenbank.

1. Geben Sie im Dialogfeld **Eigenschaften festlegen** als Name **OutputSqlDataset** ein. Klicken Sie in der Dropdownliste **Verknüpfter Dienst** auf **+ Neu**. Einem verknüpften Dienst muss ein Dataset zugewiesen werden. Der verknüpfte Dienst enthält die Verbindungszeichenfolge, die Data Factory zum Herstellen einer Verbindung mit Azure SQL-Datenbank zur Laufzeit verwendet. Das Dataset gibt den Container, den Ordner und (optional) die Datei an, in die die Quelldaten kopiert werden.

1. Führen Sie im Dialogfeld **New Linked Service (Azure SQL Database)** (Neuer verknüpfter Dienst (Azure SQL-Datenbank)) die folgenden Schritte aus:

    a. Geben Sie unter **Name** den Namen **AzureSqlDatabaseLinkedService** ein.

    b. Wählen Sie unter **Servername** Ihre SQL Server-Instanz aus.

    c. Wählen Sie unter **Datenbankname** Ihre Datenbank aus.

    d. Geben Sie unter **Benutzername** den Namen des Benutzers ein.

    e. Geben Sie unter **Kennwort** das Kennwort für den Benutzer ein.

    f. Klicken Sie auf **Verbindung testen**, um die Verbindung zu testen.

    g. Wählen Sie **Erstellen** aus, um den verknüpften Dienst bereitzustellen.

    ![Speichern des neuen verknüpften Diensts](./media/tutorial-copy-data-portal/new-azure-sql-linked-service-window.png)

1. Das Dialogfeld **Eigenschaften festlegen** wird automatisch geöffnet. Wählen Sie unter **Tabelle** die Option **[dbo].[emp]** aus. Klicken Sie anschließend auf **OK**.

1. Wechseln Sie zur Registerkarte mit der Pipeline, und überprüfen Sie, ob für **Senkendataset** die Option **OutputSqlDataset** ausgewählt ist.

    ![Pipelineregisterkarte](./media/tutorial-copy-data-portal/pipeline-tab-2.png)       

Optional können Sie das Schema der Quelle dem entsprechenden Zielschema zuordnen. Befolgen Sie dazu die Anweisungen unter [Schemazuordnung in Kopieraktivität](copy-activity-schema-and-type-mapping.md).

## <a name="validate-the-pipeline"></a>Überprüfen der Pipeline
Klicken Sie auf der Symbolleiste auf **Überprüfen**, um die Pipeline zu überprüfen.

Sie können den JSON-Code der Pipeline anzeigen, indem Sie oben rechts auf **Code** klicken.

## <a name="debug-and-publish-the-pipeline"></a>Debuggen und Veröffentlichen der Pipeline
Sie können vor dem Veröffentlichen von Artefakten (verknüpfte Dienste, Datasets und Pipeline) in Data Factory oder Ihrem eigenen Azure DevOps-Git-Repository eine Pipeline debuggen.

1. Klicken Sie auf der Symbolleiste auf **Debuggen**, um die Pipeline zu debuggen. Der Status der Pipelineausführung wird unten im Fenster auf der Registerkarte **Ausgabe** angezeigt.

1. Sobald die Pipeline erfolgreich ausgeführt werden kann, klicken Sie in der oberen Symbolleiste auf **Alle veröffentlichen**. Mit dieser Aktion werden erstellte Entitäten (Datasets und Pipelines) in Data Factory veröffentlicht.

1. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird. Damit Benachrichtigungsmeldungen angezeigt werden, klicken Sie oben rechts auf **Benachrichtigungen anzeigen** (Schaltfläche mit Glocke).

## <a name="trigger-the-pipeline-manually"></a>Manuelles Auslösen der Pipeline
In diesem Schritt lösen Sie die im vorherigen Schritt veröffentlichte Pipeline manuell aus.

1. Wählen Sie in der Symbolleiste die Option **Trigger** und dann **Trigger Now** (Jetzt auslösen). Klicken Sie auf der Seite **Pipelineausführung** auf **OK**.  

1. Wechseln Sie links zur Registerkarte **Überwachen**. Sie sehen eine Pipelineausführung, die von einem manuellen Trigger ausgelöst wird. Über die Links unter der Spalte **PIPELINENAME** können Sie Aktivitätsdetails anzeigen und die Pipeline erneut ausführen.

    [![Überwachen der Pipelineausführungen](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png)](./media/tutorial-copy-data-portal/monitor-pipeline-inline-and-expended.png#lightbox)

1. Um die der Pipelineausführung zugeordneten Aktivitätsausführungen anzuzeigen, wählen Sie unter der Spalte **PIPELINENAME** den Link **CopyPipeline** aus. Da in diesem Beispiel nur eine Aktivität vorhanden ist, wird in der Liste nur ein Eintrag angezeigt. Wenn Sie Details zum Kopiervorgang anzeigen möchten, wählen Sie in der Spalte **ACTIVTIY NAME** (AKTIVITÄTSNAME) den Link **Details** (das Brillensymbol) aus. Wählen Sie oben **Alle Pipelineausführungen** aus, um zurück zur Ansicht mit den Pipelineausführungen zu wechseln. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

    [![Überwachen der Aktivitätsausführungen](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/view-activity-runs-inline-and-expended.png#lightbox)

1. Stellen Sie sicher, dass zwei weitere Zeilen zur Tabelle **emp** in der Datenbank hinzugefügt werden.

## <a name="trigger-the-pipeline-on-a-schedule"></a>Auslösen der Pipeline nach einem Zeitplan
In diesem Zeitplan erstellen Sie einen Zeitplantrigger für die Pipeline. Der Trigger führt die Pipeline nach dem angegebenen Zeitplan aus, etwa stündlich oder täglich. Hier legen Sie fest, dass der Trigger bis zur angegebenen Endzeit (Datum und Uhrzeit) minütlich ausgeführt wird.

1. Navigieren Sie auf der linken Seite zur Registerkarte **Autor** (oberhalb der Registerkarte „Überwachen“).

1. Navigieren Sie zu Ihrer Pipeline, klicken Sie auf der Symbolleiste auf **Trigger**, und wählen Sie **Neu/Bearbeiten**.

1. Wählen Sie im Dialogfeld **Trigger hinzufügen** im Bereich **Trigger auswählen** die Option **+ Neu** aus.

1. Führen Sie im Fenster **Neuer Trigger** die folgenden Schritte aus:

    a. Geben Sie unter **Name** den Namen **RunEveryMinute** ein.

    b. Aktualisieren Sie die Angabe unter **Startdatum** für Ihren Trigger. Liegt das Datum vor dem aktuellen datetime-Wert, ist der Trigger ab Veröffentlichung der Änderung wirksam. 

    c. Wählen Sie unter **Zeitzone** die Dropdownliste aus.

    d. Legen Sie **Serie** auf **Every 1 Minute(s)** (Alle 1 Minute(n)) fest.

    e. Aktivieren Sie das Kontrollkästchen **Enddatum festlegen**, und aktualisieren Sie den Teil **End On** (Ende am), sodass die angegebene Zeit einige Minuten nach dem aktuellen datetime-Wert liegt. Der Trigger wird erst nach dem Veröffentlichen der Änderungen aktiviert. Wenn nur einige Minuten dazwischen liegen und Sie die Änderungen bis zur angegebenen Zeit nicht veröffentlicht haben, wird kein ausgeführter Trigger angezeigt.

    f. Wählen Sie **Ja** für die Option **Aktiviert** aus.

    g. Klicken Sie auf **OK**.

    > [!IMPORTANT]
    > Da für jede Pipelineausführung Gebühren anfallen, legen Sie ein geeignetes Enddatum fest.

1. Überprüfen Sie die Warnung auf der Seite **Trigger bearbeiten**, und klicken Sie auf **Speichern**. Die Pipeline in diesem Beispiel akzeptiert keine Parameter.

1. Klicken Sie auf **Alle veröffentlichen**, um die Änderung zu veröffentlichen.

1. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen**, um die ausgelösten Pipelineausführungen anzuzeigen.

    [![Ausgelöste Pipelineausführungen](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png)](./media/tutorial-copy-data-portal/triggered-pipeline-runs-inline-and-expended.png#lightbox)

1. Um von der Ansicht **Pipelineausführungen** zur Ansicht **Triggerausführungen** zu wechseln, klicken Sie auf der linken Seite des Fensters auf **Triggerausführungen**.

1. Die Triggerausführungen werden in einer Liste angezeigt.

1. Stellen Sie sicher, dass bis zur angegebenen Endzeit zwei Zeilen pro Minute (für jede Pipelineausführung) in die Tabelle **emp** eingefügt werden.

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten in Blob Storage von einem Speicherort in einen anderen. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mit einer Copy-Aktivität
> * Ausführen eines Testlaufs für die Pipeline
> * Manuelles Auslösen der Pipeline
> * Auslösen der Pipeline nach einem Zeitplan
> * Überwachen der Pipeline- und Aktivitätsausführungen.


Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten von einem lokalen Speicherort in die Cloud kopieren:

> [!div class="nextstepaction"]
>[Tutorial: Kopieren von Daten aus einer lokalen SQL Server-Datenbank nach Azure Blob Storage](tutorial-hybrid-copy-portal.md)