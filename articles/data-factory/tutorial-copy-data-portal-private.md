---
title: Erstellen einer Azure Data Factory-Pipeline mithilfe von privaten Endpunkten
description: Dieses Tutorial enthält detaillierte Anweisungen zur Erstellung einer Data Factory mit Pipeline mithilfe des Azure-Portals. Die Pipeline verwendet die Kopieraktivität, um Daten aus Azure Blob Storage in eine Azure SQL-Datenbank zu kopieren.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.author: jingwang
ms.openlocfilehash: 002ceba60853908e381f084e95ec1ee58e8580cb
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375815"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Sicheres Kopieren von Daten aus Azure Blob Storage in eine SQL-Datenbank mithilfe von privaten Endpunkten

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Data Factory über die Azure Data Factory-Benutzeroberfläche (User Interface, UI). *Die Pipeline in dieser Data Factory kopiert Daten sicher aus Azure Blob Storage in eine Azure SQL-Datenbank (beide lassen nur den Zugriff auf ausgewählte Netzwerke zu). Dazu werden private Endpunkte in einem [verwalteten virtuellen Azure Data Factory-Netzwerk](managed-virtual-network-private-endpoint.md) verwendet.* Das Konfigurationsmuster in diesem Tutorial gilt für Kopiervorgänge aus einem dateibasierten Datenspeicher in einen relationalen Datenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher und Formate](./copy-activity-overview.md).

> [!NOTE]
> Falls Sie noch nicht mit Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](./introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:

* Erstellen einer Data Factory.
* Erstellen einer Pipeline mit einer Copy-Aktivität


## <a name="prerequisites"></a>Voraussetzungen
* **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure-Speicherkonto**. Sie verwenden Blob Storage als *Quelldatenspeicher*. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-account-create.md?tabs=azure-portal) Schritte zum Erstellen eines solchen Kontos. *Stellen Sie sicher, dass das Speicherkonto nur den Zugriff über ausgewählte Netzwerke zulässt.* 
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als *Senkendatenspeicher*. Wenn Sie keine Azure SQL-Datenbank besitzen, finden Sie Schritte zum Erstellen einer solchen Datenbank unter [Erstellen einer SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). *Stellen Sie sicher, dass das SQL-Datenbank-Konto nur den Zugriff über ausgewählte Netzwerke zulässt.* 

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Bereiten Sie nun Ihren Blobspeicher und Ihre SQL-Datenbank-Instanz durch Ausführen der folgenden Schritte auf das Tutorial vor.

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

1. Öffnen Sie Notepad. Kopieren Sie den folgenden Text, und speichern Sie ihn als **emp.txt**-Datei auf einem Datenträger:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Erstellen Sie in Ihrem Blobspeicher einen Container mit dem Namen **adftutorial**. Erstellen Sie einen Ordner namens **input** in diesem Container. Laden Sie anschließend die Datei **emp.txt** in den Ordner **input** hoch. Verwenden Sie für diese Aufgaben das Azure-Portal oder Tools wie [Azure Storage-Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

Verwenden Sie das folgende SQL-Skript, um die Tabelle **dbo.emp** in Ihrer SQL-Datenbank zu erstellen:

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

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie eine Data Factory und starten die Data Factory-Benutzeroberfläche, um eine Pipeline in der Data Factory zu erstellen.

1. Öffnen Sie Microsoft Edge oder Google Chrome. Die Data Factory-Benutzeroberfläche wird derzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.

1. Klicken Sie im Menü auf der linken Seite auf **Ressource erstellen** > **Analytics** > **Data Factory**.

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

   Der Name der Azure Data Factory muss *global eindeutig* sein. Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein (z. B. yournameADFTutorialDataFactory). Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Azure Data Factory – Benennungsregeln](./naming-rules.md).

1. Wählen Sie das **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.

1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
    - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein. 
     
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md). 

1. Wählen Sie unter **Version** die Option **V2**.

1. Wählen Sie unter **Standort** einen Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.

1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird der Hinweis im Benachrichtigungscenter angezeigt. Wählen Sie **Zu Ressource wechseln** aus, um zur Seite **Data Factory** zu navigieren.

1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Erstellen einer Azure Integration Runtime-Instanz in einem verwalteten virtuellen Data Factory-Netzwerk
In diesem Schritt erstellen Sie eine Azure Integration Runtime-Instanz und aktivieren ein verwaltetes virtuelles Data Factory-Netzwerk.

1. Navigieren Sie im Data Factory-Portal zu **Verwalten**, und wählen Sie **Neu** aus, um eine neue Azure Integration Runtime-Instanz zu erstellen.

   ![Screenshot, der das Erstellen einer neuen Azure Integration Runtime-Instanz zeigt.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. Wählen Sie auf der Seite **Integration Runtime-Setup** basierend auf den erforderlichen Funktionen die Integration Runtime aus, die erstellt werden soll. Wählen Sie für dieses Tutorial **Azure, selbstgehostet** aus, und klicken Sie dann auf **Fortsetzen**. 
1. Wählen Sie **Azure** aus, und klicken Sie dann auf **Fortsetzen**, um eine Azure Integration Runtime zu erstellen.

   ![Screenshot, der eine neue Azure Integration Runtime-Instanz zeigt.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. Wählen Sie unter **Konfiguration des virtuellen Netzwerks (Vorschau)** die Option **Aktivieren** aus.

   ![Screenshot, der das Aktivieren einer neuen Azure Integration Runtime-Instanz zeigt.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Klicken Sie auf **Erstellen**.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer Copy-Aktivität in der Data Factory. Die Copy-Aktivität kopiert Daten aus Blob Storage in SQL-Datenbank. Im [Schnellstarttutorial](./quickstart-create-data-factory-portal.md) haben Sie anhand der folgenden Schritte eine Pipeline erstellt:

1. 1\. Erstellen des verknüpften Diensts
1. Erstellen von Eingabe- und Ausgabedatasets
1. Erstellen einer Pipeline.

In diesem Tutorial beginnen Sie mit dem Erstellen einer Pipeline. Verknüpfte Dienste und Datasets erstellen Sie, wenn Sie sie zum Konfigurieren der Pipeline benötigen.

1. Wählen Sie auf der Seite **Erste Schritte** die Option **Pipeline erstellen** aus.

   ![Screenshot, der das Erstellen einer Pipeline zeigt.](./media/doc-common-process/get-started-page.png)
1. Geben Sie im Eigenschaftenbereich der Pipeline als Name der Pipeline **CopyPipeline** ein.

1. Erweitern Sie in der Toolbox **Aktivitäten** die Kategorie **Verschieben und transformieren**, und ziehen Sie die Aktivität **Daten kopieren** aus der Toolbox auf die Oberfläche des Pipeline-Designers. Geben Sie als Namen **CopyFromBlobToSql** ein.

    ![Screenshot, der die Kopieraktivität zeigt.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurieren einer Quelle

>[!TIP]
>In diesem Tutorial verwenden Sie **Kontoschlüssel** als Authentifizierungstyp für Ihren Quelldatenspeicher. Sie können bei Bedarf auch andere unterstützte Authentifizierungsmethoden auswählen, z. B. **SAS-URI**, **Dienstprinzipal** und **Verwaltete Identität**. Weitere Informationen finden Sie in den entsprechenden Abschnitten in [Kopieren und Transformieren von Daten in Azure Blob Storage mithilfe von Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Zum sicheren Speichern von Geheimnissen für Datenspeicher sollten Sie auch Azure Key Vault verwenden. Weitere Informationen und Abbildungen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Erstellen eines Quelldataset und eines verknüpften Diensts

1. Wechseln Sie zur Registerkarte **Quelle**. Klicken Sie auf **+ Neu**, um ein Quelldataset zu erstellen.

1. Wählen Sie im Dialogfeld **Neues Dataset** die Option **Azure Blob Storage** und dann **Weiter** aus. Da sich die Quelldaten in Blob Storage befinden, wählen Sie **Azure Blob Storage** als Quelldataset aus.

1. Wählen Sie im Dialogfeld **Format auswählen** den Formattyp Ihrer Daten und dann **Weiter** aus.

1. Geben Sie im Dialogfeld **Eigenschaften festlegen** als **Name** **SourceBlobDataset** ein. Aktivieren Sie das Kontrollkästchen **Erste Zeile als Kopfzeile**. Klicken Sie unter dem Textfeld **Verknüpfter Dienst** auf **+ Neu**.

1. Geben Sie im Dialogfeld **Neuer verknüpfter Dienst (Azure Blob Storage)** als **Name** **AzureStorageLinkedService** ein, und wählen Sie in der Liste **Speicherkontoname** Ihr Speicherkonto aus. 

1. Aktivieren Sie unbedingt **Interaktive Erstellung**. Die Aktivierung kann ungefähr eine Minute dauern.

    ![Screenshot, der eine interaktive Erstellung zeigt.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Klicken Sie auf **Verbindung testen**. Dabei tritt ein Fehler auf, wenn das Speicherkonto ausschließlich Zugriff über **Ausgewählte Netzwerke** zulässt und Data Factory zur Erstellung eines privaten Endpunkts erforderlich ist, der vor der Verwendung genehmigt werden muss. In der Fehlermeldung sollte ein Link zum Erstellen eines privaten Endpunkts angezeigt werden, dem Sie folgen können, um einen verwalteten privaten Endpunkt zu erstellen. Alternativ dazu können Sie direkt zur Registerkarte **Verwalten** navigieren und die Anweisungen im [nächsten Abschnitt](#create-a-managed-private-endpoint) befolgen, um einen verwalteten privaten Endpunkt zu erstellen.

   > [!NOTE]
   > Die Registerkarte **Verwalten** ist möglicherweise nicht für alle Data Factory-Instanzen verfügbar. Wenn Sie diese Option nicht sehen, können Sie auf private Endpunkte zugreifen, indem Sie **Autor** > **Verbindungen** > **Privater Endpunkt** auswählen.
1. Lassen Sie das Dialogfeld geöffnet, und navigieren Sie dann zu dem Speicherkonto, das Sie oben ausgewählt haben.

1. Befolgen Sie die Anweisungen in [diesem Abschnitt](#approval-of-a-private-link-in-a-storage-account), um die private Verbindung zu genehmigen.

1. Gehen Sie zurück zum Dialogfeld. Wählen Sie erneut **Verbindung testen** und anschließend **Erstellen** aus, um den verknüpften Dienst bereitzustellen.

1. Nach der Erstellung des verknüpften Diensts wird wieder die Seite **Eigenschaften festlegen** angezeigt. Klicken Sie neben **Dateipfad** auf **Durchsuchen**.

1. Navigieren Sie zum Ordner **adftutorial/input**, und wählen Sie die Datei **emp.txt** und dann **OK** aus.

1. Klicken Sie auf **OK**. Die Pipelineseite wird automatisch aufgerufen. Vergewissern Sie sich, dass auf der Registerkarte **Quelle** die Option **SourceBlobDataset** ausgewählt ist. Wenn Sie auf dieser Seite eine Vorschau der Daten anzeigen möchten, klicken Sie auf **Datenvorschau**.

    ![Screenshot, der das Quelldataset zeigt.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Erstellen eines verwalteten privaten Endpunkts

Wenn Sie beim Testen der Verbindung nicht den Hyperlink ausgewählt haben, folgen Sie dem Pfad. Jetzt müssen Sie einen verwalteten privaten Endpunkt erstellen, den Sie mit dem erstellten verknüpften Dienst verbinden werden.

1. Wechseln Sie zur Registerkarte **Verwalten**.

   > [!NOTE]
   > Die Registerkarte **Verwalten** ist möglicherweise nicht für alle Data Factory-Instanzen verfügbar. Wenn Sie diese Option nicht sehen, können Sie auf private Endpunkte zugreifen, indem Sie **Autor** > **Verbindungen** > **Privater Endpunkt** auswählen.

1. Navigieren Sie zum Abschnitt **Verwaltete private Endpunkte**.

1. Wählen Sie unter **Verwaltete private Endpunkte** die Option **+ Neu** aus.

    ![Screenshot, der die Schaltfläche „Verwaltete private Endpunkte -> Neu“ zeigt.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wählen Sie in der Liste die Kachel **Azure Blob Storage** und dann **Weiter** aus.

1. Geben Sie den Namen des von Ihnen erstellten Speicherkontos ein.

1. Klicken Sie auf **Erstellen**.

1. Nach einigen Sekunden sollten Sie sehen, dass für die erstellte Private Link-Instanz eine Genehmigung erforderlich ist.

1. Wählen Sie den zuvor erstellten privaten Endpunkt aus. Ein Hyperlink wird angezeigt, über den Sie den privaten Endpunkt auf Speicherkontoebene genehmigen können.

    ![Screenshot, der den Bereich „Verwalteter privater Endpunkt“ zeigt.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Genehmigung einer Private Link-Instanz in einem Speicherkonto
1. Navigieren Sie im Speicherkonto im Abschnitt **Einstellungen** zu **Verbindungen mit privatem Endpunkt**.

1. Aktivieren Sie das Kontrollkästchen für den privaten Endpunkt, den Sie oben erstellt haben, und wählen Sie **Genehmigen** aus.

    ![Screenshot, der die Schaltfläche „Genehmigen“ für den privaten Endpunkt zeigt.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Fügen Sie eine Beschreibung hinzu, und wählen Sie **Ja** aus.
1. Gehen Sie zurück zum Abschnitt **Verwaltete private Endpunkte** der Registerkarte **Verwalten** in Data Factory.
1. Nach ungefähr ein oder zwei Minuten sollte die Genehmigung Ihres privaten Endpunkts in der Data Factory-Benutzeroberfläche angezeigt werden.


### <a name="configure-a-sink"></a>Konfigurieren einer Senke
>[!TIP]
>In diesem Tutorial verwenden Sie **SQL Authentifizierung** als Authentifizierungstyp für Ihren Senkendatenspeicher. Sie können bei Bedarf auch andere unterstützte Authentifizierungsmethoden auswählen, z. B. **Dienstprinzipal** und **Verwaltete Identität**. Weitere Informationen finden Sie in den entsprechenden Abschnitten in [Kopieren und Transformieren von Daten in Azure SQL-Datenbank mithilfe von Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Zum sicheren Speichern von Geheimnissen für Datenspeicher sollten Sie auch Azure Key Vault verwenden. Weitere Informationen und Abbildungen finden Sie unter [Speichern von Anmeldeinformationen in Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Erstellen eines Senkendatasets und eines verknüpften Diensts
1. Wechseln Sie zur Registerkarte **Senke**, und klicken Sie auf **+ Neu**, um ein Senkendataset zu erstellen.

1. Geben Sie im Dialogfeld **Neues Dataset** **SQL** in das Suchfeld ein, um die Connectors zu filtern. Wählen Sie **Azure SQL-Datenbank** und dann **Weiter** aus. In diesem Tutorial kopieren Sie Daten in eine SQL-Datenbank.

1. Geben Sie im Dialogfeld **Eigenschaften festlegen** als **Name** **OutputSqlDataset** ein. Wählen Sie in der Dropdownliste **Verknüpfter Dienst** die Option **+ Neu** aus. Einem verknüpften Dienst muss ein Dataset zugewiesen werden. Der verknüpfte Dienst enthält die Verbindungszeichenfolge, die Data Factory zum Herstellen einer Verbindung mit der SQL-Datenbank zur Laufzeit verwendet. Das Dataset gibt den Container, den Ordner und (optional) die Datei an, in die die Quelldaten kopiert werden.

1. Führen Sie im Dialogfeld **Neuer verknüpfter Dienst (Azure SQL-Datenbank)** die folgenden Schritte aus:

    1. Geben Sie unter **Name** den Namen **AzureSqlDatabaseLinkedService** ein.
    1. Wählen Sie unter **Servername** Ihre SQL Server-Instanz aus.
    1. Aktivieren Sie unbedingt **Interaktive Erstellung**.
    1. Wählen Sie unter **Datenbankname** Ihre SQL-Datenbank aus.
    1. Geben Sie unter **Benutzername** den Namen des Benutzers ein.
    1. Geben Sie unter **Kennwort** das Kennwort für den Benutzer ein.
    1. Klicken Sie auf **Verbindung testen**. Ein Fehler tritt auf, weil die SQL Server-Instanz nur Zugriff über **Ausgewählte Netzwerke** zulässt und Data Factory für die Erstellung eines privaten Endpunkts erforderlich ist, der vor der Verwendung genehmigt werden muss. In der Fehlermeldung sollte ein Link zum Erstellen eines privaten Endpunkts angezeigt werden, dem Sie folgen können, um einen verwalteten privaten Endpunkt zu erstellen. Alternativ dazu können Sie direkt zur Registerkarte **Verwalten** navigieren und die Anweisungen im nächsten Abschnitt befolgen, um einen verwalteten privaten Endpunkt zu erstellen.
    1. Lassen Sie das Dialogfeld geöffnet, und navigieren Sie dann zur ausgewählten SQL Server-Instanz.
    1. Befolgen Sie die Anweisungen in [diesem Abschnitt](#approval-of-a-private-link-in-sql-server), um die private Verbindung zu genehmigen.
    1. Gehen Sie zurück zum Dialogfeld. Wählen Sie erneut **Verbindung testen** und anschließend **Erstellen** aus, um den verknüpften Dienst bereitzustellen.

1. Das Dialogfeld **Eigenschaften festlegen** wird automatisch geöffnet. Wählen Sie unter **Tabelle** die Option **[dbo].[emp]** aus. Klicken Sie anschließend auf **OK**.

1. Wechseln Sie zur Registerkarte mit der Pipeline, und überprüfen Sie, ob für **Senkendataset** die Option **OutputSqlDataset** ausgewählt ist.

    ![Screenshot, der die Pipeline-Registerkarte zeigt.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Optional können Sie das Schema der Quelle dem entsprechenden Zielschema zuordnen. Befolgen Sie dazu die Anweisungen unter [Schemazuordnung in Kopieraktivität](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Erstellen eines verwalteten privaten Endpunkts

Wenn Sie beim Testen der Verbindung nicht den Hyperlink ausgewählt haben, folgen Sie dem Pfad. Jetzt müssen Sie einen verwalteten privaten Endpunkt erstellen, den Sie mit dem erstellten verknüpften Dienst verbinden werden.

1. Wechseln Sie zur Registerkarte **Verwalten**.
1. Navigieren Sie zum Abschnitt **Verwaltete private Endpunkte**.
1. Wählen Sie unter **Verwaltete private Endpunkte** die Option **+ Neu** aus.

    ![Screenshot, der die Schaltfläche „Verwaltete private Endpunkte -> Neu“ zeigt.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Wählen Sie in der Liste die Kachel **Azure SQL-Datenbank** und dann **Weiter** aus.
1. Geben Sie den Namen der ausgewählten SQL Server-Instanz ein.
1. Klicken Sie auf **Erstellen**.
1. Nach einigen Sekunden sollten Sie sehen, dass für die erstellte Private Link-Instanz eine Genehmigung erforderlich ist.
1. Wählen Sie den zuvor erstellten privaten Endpunkt aus. Es wird ein Hyperlink angezeigt, über den Sie den privaten Endpunkt auf der SQL Server-Ebene genehmigen können.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Genehmigung einer Private Link-Instanz in SQL Server
1. Navigieren Sie in der SQL Server-Instanz im Abschnitt **Einstellungen** zu **Verbindungen mit privatem Endpunkt**.
1. Aktivieren Sie das Kontrollkästchen für den privaten Endpunkt, den Sie oben erstellt haben, und wählen Sie **Genehmigen** aus.
1. Fügen Sie eine Beschreibung hinzu, und wählen Sie **Ja** aus.
1. Gehen Sie zurück zum Abschnitt **Verwaltete private Endpunkte** der Registerkarte **Verwalten** in Data Factory.
1. Es sollte ein oder zwei Minuten dauern, bis die Genehmigung für Ihren privaten Endpunkt angezeigt wird.

#### <a name="debug-and-publish-the-pipeline"></a>Debuggen und Veröffentlichen der Pipeline

Sie können vor dem Veröffentlichen von Artefakten (verknüpfte Dienste, Datasets und Pipeline) in Data Factory oder Ihrem eigenen Azure DevOps-Git-Repository eine Pipeline debuggen.

1. Klicken Sie auf der Symbolleiste auf **Debuggen**, um die Pipeline zu debuggen. Der Status der Pipelineausführung wird unten im Fenster auf der Registerkarte **Ausgabe** angezeigt.
1. Sobald die Pipeline erfolgreich ausgeführt werden kann, wählen Sie in der oberen Symbolleiste **Alle veröffentlichen** aus. Mit dieser Aktion werden erstellte Entitäten (Datasets und Pipelines) in Data Factory veröffentlicht.
1. Warten Sie, bis die Meldung **Erfolgreich veröffentlicht** angezeigt wird. Um Benachrichtigungsmeldungen anzuzeigen, wählen Sie in der oberen rechten Ecke **Benachrichtigungen anzeigen** (Glockenschaltfläche) aus.


#### <a name="summary"></a>Zusammenfassung
Die Pipeline in diesem Beispiel kopiert mithilfe privater Endpunkte in einem verwalteten virtuellen Data Factory-Netzwerk Daten aus Blobspeicher in SQL-Datenbank. Sie haben Folgendes gelernt:

* Erstellen einer Data Factory.
* Erstellen einer Pipeline mit einer Copy-Aktivität