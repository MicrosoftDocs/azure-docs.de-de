---
title: Kopieren von Daten aus Azure Blob Storage nach SQL mithilfe des Tools zum Kopieren von Daten
description: Erstellen Sie eine Azure Data Factory-Instanz, und verwenden Sie dann das Tool zum Kopieren von Daten, um Daten aus Azure Blob Storage in SQL-Datenbank zu kopieren.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 585a325c2a5650510b2138c0d708cebdfd17d034
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375730"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopieren von Daten aus Azure Blob Storage in eine SQL-Datenbank mithilfe des Tools zum Kopieren von Daten

> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Diensts aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend erstellen Sie mithilfe des Tools zum Kopieren von Daten eine Pipeline, die Daten aus Azure Blob Storage in eine SQL-Datenbank kopiert.

> [!NOTE]
> Falls Sie noch nicht mit Azure Data Factory vertraut sind, ist es ratsam, den Artikel [Einführung in Azure Data Factory](introduction.md) zu lesen.

In diesem Tutorial führen Sie die folgenden Schritte aus:
> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* **Azure Storage-Konto**: Verwenden Sie Blob Storage als _Quelldatenspeicher_. Falls Sie noch nicht über ein Azure Storage-Konto verfügen, finden Sie [hier](../storage/common/storage-account-create.md) eine Anleitung zum Erstellen eines Speicherkontos.
* **Azure SQL-Datenbank**: Verwenden Sie eine SQL-Datenbank als _Senkendatenspeicher_. Sollten Sie über keine SQL-Datenbank verfügen, lesen Sie die Anweisungen unter [Erstellen einer Azure SQL-Datenbank im Azure-Portal](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Bereiten Sie Ihre Blob Storage-Instanz und Ihre SQL-Datenbank wie folgt für das Tutorial vor:

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

1. Starten Sie **Editor**. Kopieren Sie den folgenden Text, und speichern Sie ihn in einer Datei namens **inputEmp.txt** auf einem Datenträger:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Erstellen Sie einen Container namens **adfv2tutorial**, und laden Sie die Datei „inputEmp.txt“ in den Container hoch. Sie können für diese Aufgaben das Azure-Portal oder verschiedene Tools verwenden, etwa [Azure Storage-Explorer](https://storageexplorer.com/).

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

1. Verwenden Sie das folgende SQL-Skript, um eine Tabelle namens **dbo.emp** in Ihrer SQL-Datenbank zu erstellen:

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

2. Gewähren Sie Azure-Diensten den Zugriff auf SQL Server. Vergewissern Sie sich, dass für den Server, auf dem SQL-Datenbank ausgeführt wird, die Einstellung **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** aktiviert ist. Diese Einstellung ermöglicht der Data Factory das Schreiben von Daten in Ihre Datenbankinstanz. Navigieren Sie zum Überprüfen und Aktivieren dieser Einstellung zu „Logischer SQL Server“ > „Sicherheit“ > „Firewalls und virtuelle Netzwerke“, und legen Sie die Option **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** auf **EIN** fest.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** > **Integration** > **Data Factory** aus:

    ![Erstellen einer neuen Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein.

    Der Name der Data Factory muss _global eindeutig_ sein. Sie erhalten unter Umständen die folgende Fehlermeldung:

    ![Fehlermeldung zu neuer Data Factory](./media/doc-common-process/name-not-available-error.png)

    Wenn eine Fehlermeldung zum Namenswert angezeigt wird, geben Sie einen anderen Namen für die Data Factory ein. Verwenden Sie beispielsweise den Namen _**IhrName**_**ADFTutorialDataFactory**. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](naming-rules.md).
1. Wählen Sie das **Azure-Abonnement** aus, in dem die neue Data Factory erstellt werden soll.
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:

    a. Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.

    b. Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.
    
    Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).

1. Wählen Sie unter **Version** die Option **V2**.
1. Wählen Sie unter **Standort** den Standort für die Data Factory aus. In der Dropdownliste werden nur unterstützte Standorte angezeigt. Die Datenspeicher (etwa Azure Storage und SQL-Datenbank) und Computeeinheiten (etwa Azure HDInsight), die von der Data Factory genutzt werden, können sich an anderen Standorten und in anderen Regionen befinden.
1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Startseite von **Data Factory** angezeigt.

    ![Data Factory-Startseite](./media/doc-common-process/data-factory-home-page.png)
1. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Erste Schritte** auf die Kachel **Daten kopieren**, um das Tool zum Kopieren von Daten zu starten.

    ![Kachel für das Tool zum Kopieren von Daten](./media/doc-common-process/get-started-page.png)
1. Geben Sie auf der Seite **Eigenschaften** unter **Taskname** den Namen **CopyFromBlobToSqlPipeline** ein. Wählen Sie **Weiter** aus. Über die Data Factory-Benutzeroberfläche wird eine Pipeline mit dem angegebenen Tasknamen erstellt.
    ![Erstellen einer Pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**, um eine Verbindung hinzuzufügen.

    b. Wählen Sie im Katalog **Azure Blob Storage** aus, und klicken Sie dann auf **Fortsetzen**.

    c. Wählen Sie auf der Seite **New Linked Service** (Neuer verknüpfter Dienst) Ihr Azure-Abonnement und anschließend in der Liste **Speicherkontoname** Ihr Speicherkonto aus. Testen Sie die Verbindung, und wählen Sie **Fertig stellen** aus.

    d. Wählen Sie den neu erstellten verknüpften Dienst als Quelle aus, und klicken Sie auf **Weiter**.

    ![Auswählen des verknüpften Quelldiensts](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Führen Sie auf der Seite **Choose the input file or folder** (Eingabedatei oder -ordner auswählen) die folgenden Schritte aus:

    a. Klicken Sie auf **Durchsuchen**, um zum Ordner **adfv2tutorial/input** zu navigieren, wählen Sie die Datei **inputEmp.txt** aus, und klicken Sie dann auf **Auswählen**.

    b. Klicken Sie auf **Weiter**, um mit dem nächsten Schritt fortzufahren.

1. Aktivieren Sie auf der Seite **File format settings** (Dateiformateinstellungen) das Kontrollkästchen *Erste Zeile als Header verwenden*. Wie Sie sehen, erkennt das Tool die Spalten- und Zeilentrennzeichen automatisch. Wählen Sie **Weiter** aus. Auf dieser Seite können Sie außerdem eine Vorschau der Daten und das Schema der Eingabedaten anzeigen.

    ![Dateiformateinstellungen](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Führen Sie auf der Seite **Zieldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**, um eine Verbindung hinzuzufügen.

    b. Wählen Sie im Katalog **Azure SQL-Datenbank** und dann **Weiter** aus.

    c. Wählen Sie auf der Seite **New Linked Service** (Neuer verknüpfter Dienst) in der Dropdownliste Ihren Server- und Datenbanknamen aus, und geben Sie den Benutzernamen und das Kennwort an. Wählen Sie anschließend **Erstellen** aus.

    ![Konfigurieren von Azure SQL-Datenbank](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Wählen Sie den neu erstellten verknüpften Dienst als Senke aus, und klicken Sie auf **Weiter**.

1. Wählen Sie auf der Seite **Tabellenmapping** die Tabelle **[dbo].[emp]** aus, und klicken Sie dann auf **Weiter**.

1. Auf der Seite **Spaltenzuordnung** sehen Sie, dass die zweite und dritte Spalte in der Eingabedatei den Spalten **FirstName** und **LastName** der Tabelle **emp** zugeordnet werden. Passen Sie die Zuordnung an, um sicherzustellen, dass sie keine Fehler enthält, und wählen Sie anschließend **Weiter** aus.

    ![Seite „Spaltenzuordnung“](./media/tutorial-copy-data-tool/column-mapping.png)

1. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**.

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und klicken Sie anschließend auf **Weiter**.

1. Klicken Sie auf der Seite **Bereitstellung** auf **Überwachen**, um die Pipeline (Task) zu überwachen.

    ![Überwachen der Pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)
    
1. Wählen Sie auf der Seite „Pipelineausführungen“ die Option **Aktualisieren** aus, um die Liste zu aktualisieren. Klicken Sie auf den Link unter **PIPELINENAME**, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. 
    ![Pipelineausführung](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Wählen Sie auf der Seite „Aktivitätsausführungen“ unter der Spalte **AKTIVITÄTSNAME** den Link **Details** (Brillensymbol) aus, um weitere Details zum Kopiervorgang anzuzeigen. Wählen Sie im Breadcrumb-Menü den Link **Alle Pipelineausführungen** aus, um zur Ansicht „Pipelineausführungen“ zurückzukehren. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**.

    ![Überwachung der Aktivitätsausführungen](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Vergewissern Sie sich, dass die Daten in die Tabelle **dbo.emp** in Ihrer SQL-Datenbank eingefügt werden.

1. Klicken Sie im linken Bereich auf die Registerkarte **Autor**, um in den Bearbeitungsmodus zu wechseln. Sie können die vom Tool erstellten verknüpften Dienste, Datasets und Pipelines mit dem Editor aktualisieren. Ausführliche Informationen zum Bearbeiten dieser Entitäten über die Data Factory-Benutzeroberfläche finden Sie in der [Azure-Portal-Version dieses Tutorials](tutorial-copy-data-portal.md).

    ![Auswählen der Registerkarte „Autor“](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten aus Blob Storage in eine SQL-Datenbank. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Pipeline mithilfe des Tools zum Kopieren von Daten
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten von einem lokalen Speicherort in die Cloud kopieren:

>[!div class="nextstepaction"]
>[Tutorial: Kopieren von Daten aus einer lokalen SQL Server-Datenbank nach Azure Blob Storage](tutorial-hybrid-copy-data-tool.md)
