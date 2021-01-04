---
title: Aktivieren der Azure Automation-Updateverwaltung über ein Runbook
description: In diesem Artikel erfahren Sie, wie Sie die Updateverwaltung über ein Runbook aktivieren.
services: automation
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5a9f12a823a22bfb48ccb4482d3402464aa77fea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908358"
---
# <a name="enable-update-management-from-a-runbook"></a>Aktivieren der Updateverwaltung über ein Runbook

In diesem Artikel wird beschrieben, wie Sie mit einem Runbook das Feature [Updateverwaltung](overview.md) für VMs in Ihrer Umgebung aktivieren können. Sie müssen eine vorhandene VM mit der Updateverwaltung aktivieren, um Azure-VMs im großen Stil zu aktivieren.

> [!NOTE]
> Wenn Sie die Updateverwaltung aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt. Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](../how-to/region-mappings.md).

Diese Methode verwendet zwei Runbooks:

* **Enable-MultipleSolution**: Das primäre Runbook, das zur Eingabe von Konfigurationsinformationen auffordert, den angegebenen virtuellen Computer abfragt und andere Überprüfungen durchführt. Anschließend wird das Runbook **Enable-AutomationSolution** aufgerufen, um die Updateverwaltung für jeden virtuellen Computer in der angegebenen Ressourcengruppe zu konfigurieren.
* **Enable-AutomationSolution**: Aktiviert die Updateverwaltung für mindestens einen in der Zielressourcengruppe angegebenen virtuellen Computer. Das Runbook überprüft, ob die Voraussetzungen erfüllt sind und die Log Analytics-VM-Erweiterung installiert ist, und installiert diese, sofern sie nicht gefunden wird. Darüber hinaus fügt es die virtuellen Computer zur Bereichskonfiguration im angegebenen Log Analytics-Arbeitsbereich hinzu, der mit dem Automation-Konto verknüpft ist.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](../automation-security-overview.md) zum Verwalten von Computern.
* [Log Analytics-Arbeitsbereich](../../azure-monitor/platform/design-logs-deployment.md)
* Ein [virtueller Computer](../../virtual-machines/windows/quick-create-portal.md).
* Zwei Automation-Ressourcen, die vom Runbook **Enable-AutomationSolution** verwendet werden. Wenn dieses Runbook nicht bereits in Ihrem Automation-Konto vorhanden ist, wird es während der ersten Ausführung automatisch vom Runbook **Enable-MultipleSolution** importiert.
    * *LASolutionSubscriptionId:* ID des Abonnements, in dem sich Ihr Log Analytics-Arbeitsbereich befindet.
    * *LASolutionWorkspaceId:* Arbeitsbereichs-ID des Log Analytics-Arbeitsbereichs, der mit Ihrem Automation-Konto verknüpft ist.

    Diese Variablen werden verwendet, um den Arbeitsbereich des integrierten virtuellen Computers zu konfigurieren. Diese Variablen müssen manuell erstellt werden. Sind diese nicht angegeben, sucht das Skript zunächst nach allen virtuellen Computern, die in ihrem Abonnement in die Updateverwaltung integriert sind. Anschließend sucht es nach dem Abonnement, in dem sich das Automation-Konto befindet und dann nach allen anderen Abonnements, auf die das Benutzerkonto Zugriff hat. Bei nicht ordnungsgemäßer Konfiguration führt dies unter Umständen dazu, dass Ihre Computer in einen zufällig ausgewählten Log Analytics-Arbeitsbereich integriert werden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

1. Navigieren Sie im Azure-Portal zu **Automation-Konten**. Wählen Sie auf der Seite **Automation-Konten** Ihr Konto in der Liste aus.

2. Wählen Sie in Ihrem Automation-Konto unter **Updateverwaltung** die Option **Updateverwaltung** aus.

3. Wählen Sie den Log Analytics-Arbeitsbereich aus, und klicken Sie dann auf **Aktivieren**. Während die Updateverwaltung aktiviert wird, wird ein Banner angezeigt.

    ![Aktivieren der Updateverwaltung](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Installieren und Aktualisieren von Modulen

Um die Updateverwaltung für Ihre virtuellen Computer mit dem Runbook erfolgreich zu aktivieren, müssen Sie ein Update auf die aktuellen Azure-Module durchführen und das Modul [AzureRM.OperationalInsights](/powershell/module/azurerm.operationalinsights) importieren.

1. Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Module** aus.

2. Klicken Sie auf **Azure-Module aktualisieren**, um die Azure-Module auf die neueste Version zu aktualisieren.

3. Klicken Sie auf **Ja**, um alle vorhandenen Azure-Module auf die aktuelle Version zu aktualisieren.

    ![Aktualisieren von Modulen](media/enable-from-runbook/update-modules.png)

4. Kehren Sie unter **Freigegebene Ressourcen** zur Option **Module** zurück.

5. Wählen Sie die Option **Katalog durchsuchen** aus, um den Modulkatalog zu öffnen.

6. Suchen Sie nach `AzureRM.OperationalInsights`, und importieren Sie dieses Modul in Ihr Automation-Konto.

    ![Importieren des OperationalInsights-Moduls](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>Auswählen eines zu verwaltenden virtuellen Azure-Computers

Wenn die Updateverwaltung aktiviert ist, können Sie eine Azure-VM hinzufügen, um Updates zu erhalten.

1. Wählen Sie in Ihrem Automation-Konto im Abschnitt **Updateverwaltung** die Option **Updateverwaltung** aus.

2. Wählen Sie **Azure-VMs hinzufügen**, um Ihren virtuellen Computer hinzuzufügen

3. Wählen Sie den virtuellen Computer in der Liste aus, und klicken Sie auf  **Aktivieren**, um ihn für die Verwaltung zu konfigurieren.

   ![Aktivieren der Updateverwaltung für eine VM](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Wenn Sie versuchen, ein anderes Feature zu aktivieren, bevor die Einrichtung der Updateverwaltung abgeschlossen ist, erhalten Sie diese Meldung: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Importieren eines Runbooks zum Aktivieren der Updateverwaltung

1. Wählen Sie in Ihrem Automation-Konto unter **Prozessautomatisierung** die Option **Runbooks** aus.

2. Klicken Sie auf **Katalog durchsuchen**.

3. Suchen Sie nach **Update- und Änderungsnachverfolgung**.

4. Wählen Sie das Runbook aus, und klicken Sie auf der Seite **Quelle anzeigen** auf **Importieren**.

5. Klicken Sie auf **OK**, um das Runbook in das Automation-Konto zu importieren.

   ![Importieren eines Runbooks für Setup](media/enable-from-runbook/import-from-gallery.png)

6. Wählen Sie auf der Seite **Runbook** das Runbook **Enable-MultipleSolution** aus, und klicken Sie dann auf **Bearbeiten**. Wählen Sie im Text-Editor die Option **Veröffentlichen** aus.

7. Wenn Sie zum Bestätigen aufgefordert werden, klicken Sie auf **Ja**, um das Runbook zu veröffentlichen.

## <a name="start-the-runbook"></a>Starten des Runbooks

Sie müssen die Updateverwaltung für eine Azure-VM aktiviert haben, um dieses Runbook zu starten. Hierfür sind ein vorhandener virtueller Computer und eine vorhandene Ressourcengruppe mit dem aktivierten Feature erforderlich, um mindestens einen virtuellen Computer in der Zielressourcengruppe zu konfigurieren.

1. Öffnen Sie das Runbook **Enable-MultipleSolution**.

   ![Runbook für mehrere Lösungen](media/enable-from-runbook/runbook-overview.png)

2. Klicken Sie auf die Schaltfläche „Start“, und geben Sie die Parameterwerte in die folgenden Felder ein:

   * **VMNAME**: Der Name einer vorhandenen VM, die der Updateverwaltung hinzugefügt werden soll. Lassen Sie dieses Feld leer, um alle virtuellen Computer in der Ressourcengruppe hinzuzufügen.
   * **VMRESOURCEGROUP**: Der Name der Ressourcengruppe für die zu aktivierenden VMs.
   * **SUBSCRIPTIONID**: Die Abonnement-ID der zu aktivierenden neuen VM. Lassen Sie dieses Feld leer, um das Abonnement des Arbeitsbereichs zu verwenden. Wenn Sie eine andere Abonnement-ID verwenden, fügen Sie das ausführende Konto für das Automation-Konto als Mitwirkender für das Abonnement hinzu.
   * **ALREADYONBOARDEDVM**: Der Name der VM, die bereits manuell für Updates aktiviert ist.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: Der Name der Ressourcengruppe, der die VM angehört.
   * **SOLUTIONTYPE**: Geben Sie **Updates** ein.

   ![Parameter für das Enable-MultipleSolution-Runbook](media/enable-from-runbook/runbook-parameters.png)

3. Klicken Sie auf **OK**, um den Runbookauftrag zu starten.

4. Überwachen Sie den Status des Runbookauftrags und etwaige Fehler auf der Seite **Aufträge**.

## <a name="next-steps"></a>Nächste Schritte

* Wie Sie die Updateverwaltung für VMs verwenden, erfahren Sie unter [Verwalten von Updates und Patches für Ihre VMs](manage-updates-for-vm.md).

* Informationen zum Behandeln von Fehlern bei der Updateverwaltung finden Sie unter [Behandeln von Problemen mit der Updateverwaltung](../troubleshoot/update-management.md).
