---
title: 'Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten '
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline mit einer Kopieraktivität, indem Sie den von der Data Factory unterstützten Kopier-Assistenten verwenden.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 60a575fc211c512c8657bffd567c96f98cc3d69a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377005"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe des Data Factory-Kopier-Assistenten
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-dot-net.md). 


In diesem Tutorial wird gezeigt, wie Sie Daten mithilfe des **Kopier-Assistenten** aus einem Azure Blob Storage in Azure SQL-Datenbank kopieren. 

Der **Kopier-Assistent** von Azure Data Factory ermöglicht Ihnen die schnelle Erstellung einer Datenpipeline, die Daten aus einem unterstützten Quelldatenspeicher in einen unterstützten Zieldatenspeicher kopiert. Daher empfehlen wir Ihnen, den Assistenten als ersten Schritt zum Erstellen einer Beispielpipeline für die Datenverschiebung zu verwenden. Eine Liste der Datenspeicher, die als Quellen und Ziele unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

In diesem Tutorial wird gezeigt, wie Sie eine Azure Data Factory erstellen, den Kopier-Assistenten starten und die Schritte zum Angeben von Details zur Datenerfassung bzw. -verschiebung ausführen. Nachdem Sie die Schritte im Assistenten ausgeführt haben, erstellt der Assistent automatisch eine Pipeline mit einer Kopieraktivität, um Daten aus einem Azure Blob Storage in Azure SQL-Datenbank zu kopieren. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Voraussetzungen
Führen Sie vor dem Durcharbeiten dieses Tutorials die Schritte zur Erfüllung der Voraussetzungen aus, die im Artikel [Übersicht über das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) aufgeführt sind.

## <a name="create-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie im Azure-Portal eine Azure Data Factory namens **ADFTutorialDataFactory**.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie links oben auf **Ressource erstellen** und anschließend auf **Daten + Analysen** > **Data Factory**. 
   
   ![Neu -> Data Factory](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. Gehen Sie auf dem Blatt **Neue Data Factory** wie folgt vor:
   
   1. Geben Sie **ADFTutorialDataFactory** als **Namen** ein.
       Der Name der Azure Data Factory muss global eindeutig sein. Sollte der Fehler `Data factory name "ADFTutorialDataFactory" is not available` auftreten, ändern Sie den Namen der Data Factory (z.B. in „IhrNameADFTutorialDataFactoryTTMMJJJJ“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .  
      
       ![Data Factory-Name nicht verfügbar](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Wählen Sie Ihr Azure- **Abonnement** aus.
   3. Führen Sie unter „Ressourcengruppe“ einen der folgenden Schritte aus: 
      
      - Wählen Sie **Use existing** (Vorhandene verwenden), um eine vorhandene Ressourcengruppe auszuwählen.
      - Wählen Sie **Neu erstellen**, um einen Namen für eine Ressourcengruppe einzugeben.
          
        Bei einigen Schritten in diesem Tutorial wird davon ausgegangen, dass Sie den Namen **ADFTutorialResourceGroup** für die Ressourcengruppe verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../../azure-resource-manager/management/overview.md).
   4. Wählen Sie einen **Standort** für die Data Factory aus.
   5. Aktivieren Sie unten auf dem Blatt das Kontrollkästchen **An Dashboard anheften**.  
   6. Klicken Sie auf **Erstellen**.
      
       ![Blatt "Neue Data Factory"](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Nach Abschluss der Erstellung wird das Blatt **Data Factory** wie in der folgenden Abbildung dargestellt angezeigt:
   
   ![Data Factory-Startseite](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Starten des Kopier-Assistenten
1. Klicken Sie auf dem Blatt „Data Factory“ auf **Daten kopieren**, um den **Kopier-Assistenten** zu starten. 
   
   > [!NOTE]
   > Wenn Sie feststellen, dass der Webbrowser bei der Autorisierung hängen bleibt, deaktivieren Sie in den Browsereinstellungen die Einstellung **Cookies und Websitedaten von Drittanbietern blockieren**, oder lassen Sie die Einstellung aktiviert, und erstellen Sie eine Ausnahme für **login.microsoftonline.com**. Versuchen Sie anschließend erneut, den Assistenten zu starten.
2. Auf der Seite **Eigenschaften**:
   
   1. Geben Sie unter **Aufgabenname** den Namen **CopyFromBlobToAzureSql** ein.
   2. Geben Sie eine **Beschreibung** ein (optional).
   3. Ändern Sie **Startdatum/-zeit** und **Enddatum/-zeit** so, dass das Enddatum auf den heutigen Tag und das Startdatum auf fünf Tage vor dem heutigen Tag festgelegt ist.  
   4. Klicken Sie auf **Weiter**.  
      
      ![Copy Tool - Properties page](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Klicken Sie auf der Seite **Quelldatenspeicher** auf die Kachel **Azure Blob Storage**. Sie können diese Seite verwenden, um den Quelldatenspeicher für die Kopieraufgabe anzugeben. 
   
    ![Kopiertool – Seite „Quelldatenspeicher“](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Auf der Seite **Azure Blob Storage-Konto angeben** :
   
   1. Geben Sie unter **Name des verknüpften Diensts** als Name **AzureStorageLinkedService** ein.
   2. Überprüfen Sie, ob unter **Kontoauswahlmethode** die Option **Über Azure-Abonnements** ausgewählt ist.
   3. Wählen Sie Ihr Azure- **Abonnement** aus.  
   4. Wählen Sie in der Liste mit den **Azure-Speicherkonten**, die im ausgewählten Abonnement verfügbar sind, ein Azure-Speicherkonto aus. Sie können sich auch für das manuelle Eingeben von Speicherkontoeinstellungen entscheiden, indem Sie unter **Kontoauswahlmethode** die Option **Manuell eingeben** auswählen und dann auf **Weiter** klicken. 
      
      ![Kopiertool – Azure Blob Storage-Konto angeben](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Auf der Seite **Eingabedatei oder -ordner auswählen** :
   
   1. Doppelklicken Sie auf **adftutorial** (Ordner).
   2. Wählen Sie **emp.txt** aus, und klicken Sie auf **Auswählen**.
      
      ![Screenshot: Option „Auswählen“ für die Eingabedatei](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Klicken Sie auf der Seite **Choose the input file or folder** (Eingabedatei oder -ordner auswählen) auf **Weiter**. Aktivieren Sie nicht die Option **Binary copy**(Binärkopie). 
   
    ![Screenshot: Option „Binary copy“ (Binärkopie) für die Eingabedatei](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Auf der Seite **File format settings** (Dateiformateinstellungen) werden die Trennzeichen und das Schema angezeigt. Diese Informationen werden vom Assistenten beim Analysieren der Datei automatisch erkannt. Sie können die Trennzeichen auch manuell eingeben, um die automatische Erkennung für den Kopier-Assistenten zu beenden oder die Werte zu überschreiben. Klicken Sie auf **Weiter**, nachdem Sie die Trennzeichen und Vorschaudaten geprüft haben. 
   
    ![Kopiertool – Dateiformateinstellungen](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Wählen Sie auf der Seite „Zieldatenspeicher“ die Option **Azure SQL-Datenbank**, und klicken Sie auf **Weiter**.
   
    ![Kopiertool – Zielspeicher auswählen](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Auf der Seite **Azure SQL-Datenbank angeben** :
   
   1. Geben Sie im Feld **Verbindungsname** den Text **AzureSqlLinkedService** ein.
   2. Überprüfen Sie, ob unter **Server/database selection method** (Server-/Datenbankauswahlmethode) die Option **Über Azure-Abonnements** ausgewählt ist.
   3. Wählen Sie Ihr Azure- **Abonnement** aus.  
   4. Wählen Sie **Servername** und **Datenbank** aus.
   5. Geben Sie **Benutzername** und **Kennwort** ein.
   6. Klicken Sie auf **Weiter**.  
      
      ![Kopiertool – Angeben der Azure SQL-Datenbank](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Wählen Sie auf der Seite **Tabellenzuordnung** für das Feld **Ziel** in der Dropdownliste die Option **emp** aus, und klicken Sie auf den **Pfeil nach unten** (optional), um das Schema und eine Vorschau der Daten anzuzeigen.
    
     ![Kopiertool – Tabellenzuordnung](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Klicken Sie auf der Seite **Schemazuordnung** auf **Weiter**.
    
    ![Kopiertool – Schemazuordnung](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Klicken Sie auf der Seite **Leistungseinstellungen** auf **Weiter**. 
    
    ![Screenshot: Seite „Leistungseinstellungen“, auf der Sie „Weiter“ auswählen können](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Überprüfen Sie die Informationen auf der Seite **Zusammenfassung**, und klicken Sie auf **Fertig stellen**. Der Assistent erstellt zwei verknüpfte Dienste, zwei Datasets (Eingabe und Ausgabe) und eine Pipeline in der Data Factory erstellt (von der aus Sie den Kopier-Assistenten gestartet haben). 
    
    ![Screenshot: Seite „Zusammenfassung“, auf der Sie „Weiter“ auswählen können](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Starten der Anwendung „Überwachung und Verwaltung“
1. Klicken Sie auf der Seite **Bereitstellung** auf diesen Link: `Click here to monitor copy pipeline`.
   
   ![Kopiertool – Bereitstellung erfolgreich](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Die Überwachungsanwendung wird in einer separaten Registerkarte im Webbrowser gestartet.   
   
   ![Überwachungs-App](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Um den aktuellen Status der stündlichen Slices anzuzeigen, klicken Sie unten in der Liste **AKTIVITÄTSFENSTER** auf die Schaltfläche **Aktualisieren**. Daraufhin werden fünf Tage lang zwischen Start- und Endzeit für die Pipeline fünf Aktivitätsfenster angezeigt. Die Liste wird nicht automatisch aktualisiert. Daher müssen Sie möglicherweise ein paar Mal auf „Aktualisieren“ klicken, bis alle Aktivitätsfenster im Status „Bereit“ angezeigt werden. 
4. Wählen Sie ein Aktivitätsfenster in der Liste aus. Die Details hierzu finden Sie im **Aktivitätenfenster-Explorer** auf der rechten Seite.

    ![Details zum Aktivitätsfenster](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Beachten Sie, dass die Daten 11, 12, 13, 14 und 15 in Grün angezeigt werden, was bedeutet, dass die täglichen Ausgabeslices für diese Daten bereits erstellt wurden. Diese Farbcodierung wird auch in der Pipeline angezeigt, während das Ausgabedataset in der Diagrammansicht angezeigt wird. Beachten Sie, dass im vorherigen Schritt bereits zwei Slices erstellt wurden, ein Slice derzeit verarbeitet wird und die anderen zwei Slices (basierend auf der Farbcodierung) noch verarbeitet werden müssen. 

    Weitere Informationen zur Verwendung dieser Anwendung finden Sie im Artikel [Überwachen und Verwalten einer Pipeline mithilfe der Überwachungs-App](data-factory-monitor-manage-app.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zu Feldern bzw. Eigenschaften zu erhalten, die im Kopier-Assistenten für einen Datenspeicher angezeigt werden, klicken Sie auf den Link für den Datenspeicher in der Tabelle. 