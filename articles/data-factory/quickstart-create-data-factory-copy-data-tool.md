---
title: Kopieren von Daten mithilfe des Azure-Tools zum Kopieren von Daten
description: Erstellen Sie eine Azure Data Factory-Instanz, und verwenden Sie dann das Tool zum Kopieren von Daten, um Daten von einem Speicherort in Azure Blob Storage an einen anderen Speicherort zu kopieren.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 48624e415649be2fb76aed99c5cb0e1b3b12f04b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372857"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Schnellstart: Kopieren von Daten mithilfe des Tools zum Kopieren von Daten

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um eine Data Factory zu erstellen. Anschließend erstellen Sie mithilfe des Tools zum Kopieren von Daten eine Pipeline, die Daten aus einem Ordner in Azure Blob Storage in einen anderen Ordner kopiert. 

> [!NOTE]
> Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Schnellstarts die Informationen unter [Einführung in Azure Data Factory](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). 
1. Wählen Sie im Azure-Portal im Menü die Option **Ressource erstellen** > **Integration** > **Data Factory** aus:

    ![Erstellen einer neuen Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein. 
 
   Der Name der Azure Data Factory-Instanz muss *global eindeutig* sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in **&lt;IhrName&gt;ADFTutorialDataFactory**), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).
  
   ![Fehler, wenn ein Name nicht verfügbar ist](./media/doc-common-process/name-not-available-error.png)
1. Wählen Sie unter **Abonnement** Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
     
   - Wählen Sie die Option **Use existing** (Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
   - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
         
   Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
1. Wählen Sie **V2** als **Version** aus.
1. Wählen Sie unter **Standort** den Standort für die Data Factory aus.

   In der Liste werden nur Standorte angezeigt, die von Data Factory unterstützt werden und an denen Ihre Azure Data Factory-Metadaten gespeichert werden. Die von Data Factory verwendeten zugeordneten Datenspeicher (z. B. Azure Storage und Azure SQL-Datenbank) und Computedienste (z. B. Azure HDInsight) können in anderen Regionen ausgeführt werden.

1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Seite **Data Factory** angezeigt. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Anwendung für die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.
   
   ![Startseite der Data Factory mit der Kachel „Erstellen und überwachen“](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Starten des Tools zum Kopieren von Daten

1. Klicken Sie auf der Seite **Erste Schritte** auf die Kachel **Daten kopieren**, um das Tool zum Kopieren von Daten zu starten. 

   ![Kachel „Daten kopieren“](./media/doc-common-process/get-started-page.png)

1. Auf der Seite **Eigenschaften** des Tools zum Kopieren von Daten können Sie einen Namen für die Pipeline und ihre Beschreibung eingeben. Klicken Sie anschließend auf **Weiter**. 

   ![Eigenschaftenseite](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Führen Sie auf der Seite **Quelldatenspeicher** die folgenden Schritte aus:

    a. Klicken Sie auf **+ Neue Verbindung erstellen**, um eine Verbindung hinzuzufügen.

    b. Wählen Sie den Typ des verknüpften Diensts aus, den Sie für die Quellverbindung erstellen möchten. In diesem Tutorial verwenden wir **Azure Blob Storage**. Wählen Sie diese Option im Katalog und dann **Weiter** aus.
    
    ![Blob auswählen](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Geben Sie auf der Seite **New Linked Service (Azure Blob Storage)** (Neuer verknüpfter Dienst (Azure Blob Storage)) einen Namen für Ihren verknüpften Dienst ein. Wählen Sie in der Liste **Speicherkontoname** Ihr Speicherkonto aus, testen Sie die Verbindung, und wählen Sie dann **Erstellen** aus. 

    ![Konfigurieren des Azure Blob Storage-Kontos](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Wählen Sie den neu erstellten verknüpften Dienst als Quelle aus, und klicken Sie auf **Weiter**.


1. Führen Sie auf der Seite **Choose the input file or folder** (Eingabedatei oder -ordner auswählen) die folgenden Schritte aus:

   a. Klicken Sie auf **Durchsuchen**, um zum Ordner **adftutorial/input** zu navigieren, wählen Sie die Datei **emp.txt** aus, und klicken Sie dann auf **Auswählen**. 

   d. Aktivieren Sie das Kontrollkästchen **Binary copy** (Binärkopie), um die Datei ohne Änderungen zu übernehmen, und wählen Sie dann **Weiter** aus. 

   ![Seite „Eingabedatei oder -ordner auswählen“](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Wählen Sie auf der Seite **Zieldatenspeicher** den soeben erstellten verknüpften Dienst **Azure Blob Storage** und anschließend **Weiter** aus. 

1. Geben Sie auf der Seite **Choose the output file or folder** (Ausgabedatei oder -ordner auswählen) als Ordnerpfad den Pfad **adftutorial/output** ein, und wählen Sie **Weiter** aus. 

   ![Seite „Choose the output file or folder“ (Ausgabedatei oder -ordner auswählen)](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Klicken Sie auf der Seite **Einstellungen** auf **Weiter**, um die Standardkonfigurationen zu verwenden. 

1. Überprüfen Sie auf der Seite **Zusammenfassung** alle Einstellungen, und klicken Sie auf **Weiter**. 

1. Klicken Sie auf der Seite **Deployment complete** (Bereitstellung abgeschlossen) auf **Überwachen**, um die erstellte Pipeline zu überwachen. 

    ![Seite „Deployment complete“ (Bereitstellung abgeschlossen)](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Die Anwendung wechselt zur Registerkarte **Überwachen**. Der Status der Pipeline wird auf dieser Registerkarte angezeigt. Klicken Sie zum Aktualisieren der Liste auf **Aktualisieren**. Klicken Sie auf den Link unter **PIPELINENAME**, um Details zur Aktivitätsausführung anzuzeigen oder die Pipeline erneut auszuführen. 
   
    ![Aktualisieren der Pipeline](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Wählen Sie auf der Seite „Aktivitätsausführungen“ unter der Spalte **AKTIVITÄTSNAME** den Link **Details** (Brillensymbol) aus, um weitere Details zum Kopiervorgang anzuzeigen. Einzelheiten zu den Eigenschaften finden Sie unter [Kopieraktivität in Azure Data Factory](copy-activity-overview.md). 

1. Wählen Sie im Breadcrumb-Menü den Link **Alle Pipelineausführungen** aus, um zur Ansicht „Pipelineausführungen“ zurückzukehren. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren**. 

1. Vergewissern Sie sich, dass im Ordner **output** des Containers **adftutorial** die Datei **emp.txt** erstellt wird. Ist der Ausgabeordner nicht vorhanden, wird er vom Data Factory-Dienst automatisch erstellt. 

1. Wechseln Sie im linken Bereich zur Registerkarte **Ersteller** über der Registerkarte **Überwachen**, um verknüpfte Dienste, Datasets und Pipelines zu bearbeiten. Informationen zum Bearbeiten dieser Elemente über die Data Factory-Benutzeroberfläche finden Sie unter [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md).

    ![Auswählen der Registerkarte „Autor“](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten in Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-portal.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können. 
