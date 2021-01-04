---
title: Verwenden von Azure Automation, um einen Auftrag in StorSimple Data Manager zu starten
description: Erfahren Sie, wie Sie Azure Automation verwenden, um StorSimple Data Manager-Aufträge auszulösen.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 3d0103857b6f759560129dbe8e693ec6c0d7291e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961208"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Verwenden von Azure Automation, um einen Auftrag auszulösen

In diesem Artikel wird erläutert, wie Sie die Datentransformationsfunktion innerhalb des StorSimple Data Manager-Diensts verwenden, um StorSimple-Gerätedaten zu transformieren. Sie können einen Datentransformationsauftrag auf zwei Arten starten: 

 - Verwenden des .NET SDK
 - Verwenden des Azure Automation-Runbooks
 
In diesem Artikel wird ausführlicher erläutert, wie ein Azure Automation-Runbook erstellt und dieses dann dazu verwendet wird, einen Datentransformationsauftrag zu starten. Weitere Informationen zum Starten einer Datentransformation über .NET SDK finden Sie unter [Verwenden des .NET SDK, um Datentransformationsaufträge auszulösen](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen stellen Sie sicher, dass Sie über Folgendes verfügen:

*   Auf dem Clientcomputer ist Azure PowerShell installiert. [Azure PowerShell herunterladen](/powershell/azure/azurerm/install-azurerm-ps).
*   Eine ordnungsgemäß konfigurierte Auftragsdefinition in einem StorSimple Data Manager-Dienst innerhalb einer Ressourcengruppe.
*   Laden Sie die Datei [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) aus dem GitHub-Repository herunter. 
*   Laden Sie das Skript [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) aus dem GitHub-Repository herunter.

## <a name="step-by-step-procedure"></a>Ausführliche Anleitung

### <a name="set-up-the-automation-account"></a>Einrichten des Automation-Kontos

1. Erstellen Sie ein ausführendes Azure Automation-Konto im Azure-Portal. Wechseln Sie zu diesem Zweck zu **Azure Marketplace > Alles**, und suchen Sie dann nach **Automation**. Wählen Sie die Option **Automation-Konten** aus.

    ![Erstellen eines ausführenden Automation-Kontos](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Um ein neues Automation-Konto hinzuzufügen, klicken Sie auf **+ Hinzufügen**.

    ![Erstellen eines ausführenden Automation-Kontos 2](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. Im Dialogfeld **Automation-Konto hinzufügen**:

   1. Geben Sie den **Namen** für Ihr Automation-Konto an.
   2. Wählen Sie das **Abonnement** aus, das mit Ihrem StorSimple Data Manager-Dienst verknüpft ist.
   3. Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus.
   4. Wählen Sie einen **Speicherort** aus.
   5. Lassen Sie die Standardoption **Ausführendes Konto erstellen** aktiviert.
   6. Um einen Link für den schnellen Zugriff auf das Dashboard abzurufen, aktivieren Sie **An Dashboard anheften**. Klicken Sie auf **Erstellen**.

      ![Erstellen eines ausführenden Automation-Kontos 3](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Sie werden benachrichtigt, nachdem das Automation-Konto erfolgreich erstellt wurde.
    
      ![Benachrichtigung für die Bereitstellung eines Automation-Kontos](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Weitere Informationen hierzu finden Sie unter [Erstellen eines ausführenden Kontos](../automation/manage-runas-account.md).

3. Wechseln Sie im neu erstellten Konto zu **Freigegebene Ressourcen > Module**, und klicken Sie auf **+ Modul hinzufügen**.

    ![Importieren eines Moduls 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Navigieren Sie auf dem lokalen Computer zum Speicherort der Datei `DataTransformationApp.zip`, wählen Sie das Modul aus, und öffnen Sie es. Klicken Sie auf **OK**, um das Modul zu importieren.

    ![Importieren eines Moduls 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Wenn Azure Automation ein Modul in Ihr Konto importiert, werden Metadaten zu dem Modul extrahiert. Dieser Vorgang kann einige Minuten dauern.

   ![Importieren eines Moduls 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Sie erhalten jeweils eine Benachrichtigung, wenn das Modul bereitgestellt wird und wenn der Vorgang abgeschlossen ist.  Der Status in **Module** ändert sich in **Verfügbar**.

    ![Importieren eines Moduls 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importieren, Veröffentlichen und Ausführen von Automation-Runbooks

Führen Sie die folgenden Schritte aus, um das Runbook zu importieren, zu veröffentlichen und auszuführen, damit die Auftragsdefinition ausgelöst wird.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto. Wechseln Sie zu **Prozessautomatisierung > Runbooks**, und klicken Sie auf **+ Runbook hinzufügen**.

    ![Hinzufügen eines Runbooks 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Klicken Sie in **Runbook hinzufügen** auf **Vorhandenes Runbook importieren**.

3. Zeigen Sie auf die Azure PowerShell-Skriptdatei `Trigger-DataTransformation-Job.ps1` für die **Runbookdatei**. Das Runbooktyp wird automatisch ausgewählt. Geben Sie einen Namen und eine optionale Beschreibung für das Runbook an. Klicken Sie auf **Erstellen**.

    ![Hinzufügen eines Runbooks 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Das neue Runbook wird in der Liste der Runbooks für das Automation-Konto angezeigt. Wählen Sie dieses Runbook aus, und klicken Sie dann darauf.

    ![Hinzufügen eines Runbooks 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Bearbeiten Sie das Runbook, und klicken Sie auf den Bereich **Test**.

    ![Hinzufügen eines Runbooks 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Geben Sie die Parameter an, etwa den Namen Ihres StorSimple Data Manager-Diensts, die zugeordnete Ressourcengruppe und den Namen der Auftragsdefinition. **Starten** Sie den Test. Der Bericht wird nach Abschluss der Ausführung generiert. Weitere Informationen finden Sie unter [Testen eines Runbooks](../automation/learn/automation-tutorial-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Hinzufügen eines Runbooks 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Untersuchen Sie die Ausgabe des Runbooks im Testbereich. Wenn Sie mit dem Ergebnis zufrieden sind, schließen Sie den Bereich. Klicken Sie auf **Veröffentlichen**, und wenn Sie zur Bestätigung aufgefordert werden, bestätigen und veröffentlichen Sie das Runbook.

    ![Hinzufügen eines Runbooks 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Wechseln Sie zurück zu **Runbooks**, und wählen Sie das neu erstellte Runbook aus.

    ![Hinzufügen eines Runbooks 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Starten** Sie das Runbook. Geben Sie in **Runbook starten** alle Parameter ein. Klicken Sie auf **OK**, um den Datentransformationsauftrag zu senden und zu starten.

10. Um den Status des Auftrags im Azure-Portal zu überwachen, wechseln Sie zu **Aufträge** in Ihrem StorSimple Data Manager-Dienst. Klicken Sie auf den Auftrag, um die Auftragsdetails anzuzeigen.

    ![Hinzufügen eines Runbooks 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Nächste Schritte

[Use StorSimple Data Manager UI to transform your data (Verwenden der StorSimple Data Manager-UI zum Transformieren von Daten) ](storsimple-data-manager-ui.md).