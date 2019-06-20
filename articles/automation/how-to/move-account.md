---
title: Verschieben Ihres Azure Automation-Kontos in ein anderes Abonnement
description: In diesem Artikel wird beschrieben, wie Sie Ihr Automation-Konto in ein anderes Abonnement verschieben.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 59d433bfb888eaa41cc8f66bdf3ad28c16efbe5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61302559"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>Verschieben Ihres Azure Automation-Kontos in ein anderes Abonnement

In Azure können Sie einige Ressourcen in eine neue Ressourcengruppe oder ein Abonnement verschieben. Sie können Ressourcen mit dem Azure-Portal, PowerShell, der Azure CLI oder der REST-API verschieben. Weitere Informationen zum Prozess finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../azure-resource-manager/resource-group-move-resources.md). 

Azure Automation-Konten sind ein Beispiel für Ressourcen, die verschoben werden können. In diesem Artikel werden die Schritte zum Verschieben von Automation-Konten in eine andere Ressource oder ein anderes Abonnement beschrieben.

Die groben Schritte zum Verschieben Ihres Automation-Kontos sind:

1. Entfernen Sie Ihre Lösungen.
2. Heben Sie die Verknüpfung mit Ihrem Arbeitsbereich auf.
3. Verschieben Sie das Automation-Konto.
4. Löschen Sie die ausführenden Konten, und erstellen Sie sie neu.
5. Reaktivieren Sie Ihre Lösungen.

## <a name="remove-solutions"></a>Entfernen von Lösungen

Um die Verknüpfung Ihres Arbeitsbereichs mit Ihrem Automation-Konto aufzuheben, müssen diese Lösungen aus Ihrem Arbeitsbereich entfernt werden:
- **Änderungsnachverfolgung und Bestand**
- **Updateverwaltung** 
- **VMs außerhalb der Geschäftszeiten starten/beenden** 

Suchen Sie in Ihrer Ressourcengruppe nach den einzelnen Lösungen, und wählen Sie **Löschen**. Vergewissern Sie sich auf der Seite **Ressourcen löschen**, dass die richtigen zu löschenden Ressourcen ausgewählt sind, und wählen Sie **Löschen**.

![Löschen von Lösungen über das Azure-Portal](../media/move-account/delete-solutions.png)

Sie können diesen Schritt auch mit dem Cmdlet [Remove-AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) ausführen. Dies wird im folgenden Beispiel veranschaulicht:

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>Zusätzliche Schritte für das Starten/Beenden von VMs

Für die Lösung **VMs starten/beenden** müssen Sie auch die Warnungsregeln entfernen, die von der Lösung erstellt werden.

Navigieren Sie im Azure-Portal zu Ihrer Ressourcengruppe, und wählen Sie **Überwachung** > **Warnungen** > **Warnungsregeln verwalten**.

![Seite „Warnungen“ mit Auswahl „Warnungsregeln verwalten“](../media/move-account/alert-rules.png)

Auf der Seite **Regeln** sollte eine Liste mit den Warnungen angezeigt werden, die in dieser Ressourcengruppe konfiguriert sind. Mit der Lösung **VMs starten/beenden** werden drei Warnungsregeln erstellt:

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

Wählen Sie diese drei Warnungsregeln und dann die Option **Löschen** aus. Mit dieser Aktion werden diese Warnungsregeln entfernt.

![Seite „Regeln“ mit Anforderung der Bestätigung des Löschvorgangs für ausgewählte Regeln](../media/move-account/delete-rules.png)

> [!NOTE]
> Wenn auf der Seite **Regeln** keine Warnungsregeln angezeigt werden, sollten Sie den **Status** so ändern, dass Warnungen vom Typ **Deaktiviert** angezeigt werden. Es kann sein, dass Sie sie ggf. deaktiviert haben.

Nachdem die Warnungsregeln entfernt wurden, können Sie die Aktionsgruppe entfernen, die für Benachrichtigungen der Lösung **VMs starten/beenden** erstellt wurde.

Wählen Sie im Azure-Portal die Optionen **Überwachung** > **Warnungen** > **Aktionsgruppen verwalten**.

Wählen Sie in der Liste die Option **StartStop_VM_Notification** aus. Wählen Sie auf der Seite „Aktionsgruppe“ die Option **Löschen** aus.

![Seite „Aktionsgruppe“, Auswahl von „Löschen“](../media/move-account/delete-action-group.png)

Auf ähnliche Weise können Sie Ihre Aktionsgruppe mit PowerShell löschen, indem Sie das Cmdlet [Remove-AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) verwenden. Dies ist im folgenden Beispiel dargestellt:

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>Aufheben der Verknüpfung mit Ihrem Arbeitsbereich

Wählen Sie im Azure-Portal **Automation-Konto** > **Zugehörige Ressourcen** > **Verknüpfter Arbeitsbereich**. Wählen Sie **Verknüpfung des Arbeitsbereichs aufheben**, um für den Arbeitsbereich die Verknüpfung mit Ihrem Automation-Konto aufzuheben.

![Aufheben der Verknüpfung eines Arbeitsbereichs mit einem Automation-Konto](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>Verschieben Ihres Automation-Kontos

Nachdem Sie die vorherigen Elemente entfernt haben, können Sie damit fortfahren, Ihr Automation-Konto und die zugehörigen Runbooks zu entfernen. Navigieren Sie im Azure-Portal zur Ressourcengruppe Ihres Automation-Kontos. Wählen Sie **Verschieben** > **In ein anderes Abonnement verschieben**.

![Seite „Ressourcengruppe“ > „In ein anderes Abonnement verschieben“](../media/move-account/move-resources.png)

Wählen Sie die Ressourcen in der Ressourcengruppe aus, die Sie verschieben möchten. Stellen Sie sicher, dass Sie Ihre Ressourcen **Automation-Konto**, **Runbook** und **Log Analytics-Arbeitsbereich** einschließen.

Nachdem das Verschieben abgeschlossen wurde, sind noch weitere Schritte erforderlich, damit alles funktioniert.

## <a name="re-create-run-as-accounts"></a>Erneutes Erstellen von ausführenden Konten

Mit [ausführenden Konten](../manage-runas-account.md) wird in Azure Active Directory ein Dienstprinzipal für die Authentifizierung mit Azure-Ressourcen erstellt. Wenn Sie Abonnements ändern, werden die vorhandenen ausführenden Konten nicht mehr für das Automation-Konto verwendet.

Navigieren Sie unter dem neuen Abonnement zu Ihrem Automation-Konto, und wählen Sie unter **Kontoeinstellungen** die Option **Ausführende Konten**. Sie sehen, dass die ausführenden Konten jetzt als unvollständig angezeigt werden.

![Ausführende Konten sind unvollständig](../media/move-account/run-as-accounts.png)

Wählen Sie jedes einzelne ausführende Konto aus. Wählen Sie auf der Seite **Eigenschaften** die Option **Löschen**, um das ausführende Konto zu löschen.

> [!NOTE]
> Wenn Sie keine Berechtigungen zum Erstellen oder Anzeigen der ausführenden Konten haben, wird die folgende Meldung angezeigt: `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` Informationen zu den Berechtigungen, die zum Konfigurieren eines ausführenden Kontos benötigt werden, finden Sie unter [Berechtigungen zum Konfigurieren von ausführenden Konten](../manage-runas-account.md#permissions).

Wählen Sie nach dem Löschen der ausführenden Konten unter **Ausführendes Azure-Konto** die Option **Erstellen**. Wählen Sie auf der Seite **Ausführendes Azure-Konto hinzufügen** die Option **Erstellen**, um das ausführende Konto und den Dienstprinzipal zu erstellen. Wiederholen Sie die vorherigen Schritte mit dem **klassischen ausführenden Azure-Konto**.

## <a name="enable-solutions"></a>Aktivieren von Lösungen

Nachdem Sie die ausführenden Konten neu erstellt haben, müssen Sie die Lösungen wieder aktivieren, die Sie vor dem Verschieben entfernt haben. Wählen Sie die entsprechende Funktion in Ihrem Automation-Konto aus, um **Änderungsnachverfolgung und Bestand** und **Updateverwaltung** zu aktivieren. Wählen Sie den Log Analytics-Arbeitsbereich aus, den Sie verschoben haben, und dann **Aktivieren**.

![Reaktivieren von Lösungen in Ihrem verschobenen Automation-Konto](../media/move-account/reenable-solutions.png)

Computer, für die mit Ihren Lösungen das Onboarding durchgeführt wird, sind sichtbar, wenn Sie eine Verbindung mit dem vorhandenen Log Analytics-Arbeitsbereich hergestellt haben.

Um die Lösung **VMs außerhalb der Geschäftszeiten starten/beenden** zu aktivieren, müssen Sie die Lösung erneut bereitstellen. Wählen Sie unter **Zugehörige Ressourcen** die Optionen **VMs starten/beenden** > **Weitere Informationen anzeigen und Lösung aktivieren** > **Erstellen**, um die Bereitstellung zu starten.

Wählen Sie auf der Seite **Lösung hinzufügen** Ihren Log Analytics-Arbeitsbereich und das Automation-Konto aus.  

![Menü „Lösung hinzufügen“](../media/move-account/add-solution-vm.png)

Ausführliche Anweisungen zum Konfigurieren der Lösung finden Sie unter [Lösung zum Starten/Beenden von VMs außerhalb der Geschäftszeiten in Azure Automation](../automation-solution-vm-management.md).

## <a name="post-move-verification"></a>Überprüfung nach dem Verschieben

Überprüfen Sie nach Abschluss des Verschiebevorgangs für die folgende Liste mit Aufgaben, ob sie verifiziert wurden:

|Funktion|Tests|Link für Problembehandlung|
|---|---|---|
|Runbooks|Ein Runbook kann erfolgreich ausgeführt werden und eine Verbindung mit Azure-Ressourcen herstellen.|[Beheben von Fehlern bei Runbooks](../troubleshoot/runbooks.md)
| Quellcodeverwaltung|Sie können eine manuelle Synchronisierung für Ihr Repository für die Quellcodeverwaltung durchführen.|[Integration der Quellcodeverwaltung](../source-control-integration.md)|
|Änderungsnachverfolgung und Bestand|Stellen Sie sicher, dass die aktuellen Bestandsdaten Ihrer Computer angezeigt werden.|[Problembehandlung bei Änderungsnachverfolgung und Inventur](../troubleshoot/change-tracking.md)|
|Updateverwaltung|Stellen Sie sicher, dass Ihre Computer angezeigt werden und fehlerfrei sind.</br>Führen Sie die Bereitstellung eines Updates der Testsoftware durch.|[Behandeln von Problemen mit Updateverwaltung](../troubleshoot/update-management.md)|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verschieben von Ressourcen in Azure finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](../../azure-resource-manager/move-support-resources.md).
