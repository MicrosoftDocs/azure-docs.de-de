---
title: Übersicht über das Entfernen der Azure Automation-Funktion „VMs außerhalb der Geschäftszeiten starten/beenden“
description: In diesem Artikel wird beschrieben, wie Sie die Funktion „VMs außerhalb der Geschäftszeiten starten/beenden“ entfernen und die Verknüpfung eines Automation-Kontos mit dem Log Analytics-Arbeitsbereich aufheben.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2021
ms.topic: conceptual
ms.openlocfilehash: bed9cd23abc96c51cad0a13e81ee0b64f0d433b6
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100012270"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Entfernen von „VMs außerhalb der Geschäftszeiten starten/beenden“ aus Automation-Konto

Nachdem das Feature „VMs außerhalb der Geschäftszeiten starten/beenden“ aktiviert wurde, um den Betriebszustand Ihrer Azure-VMs zu verwalten, entscheiden Sie möglicherweise, es nicht mehr zu verwenden. Das Entfernen dieses Features kann mit einer der folgenden Methoden basierend auf den unterstützten Bereitstellungsmodellen erfolgen:

* Löschen Sie die Ressourcengruppe, die das Automation-Konto und den verknüpften Azure Monitor Log Analytics-Arbeitsbereich enthält, die jeweils für die Unterstützung dieses Features vorgesehen sind.
* Heben Sie die Verknüpfung des Log Analytics-Arbeitsbereichs mit dem Automation-Konto auf, und löschen Sie das für dieses Feature vorgesehene Automation-Konto.
* Löschen Sie dieses Feature aus einem Automation-Konto und einem verknüpften Arbeitsbereich zur Unterstützung anderer Verwaltungs- und Überwachungsziele.

Durch das Löschen dieses Features werden nur die zugehörigen Runbooks entfernt, nicht aber die während der Bereitstellung erstellten benutzerdefinierten Zeitpläne bzw. nachträglich erstellten benutzerdefinierten Zeitpläne oder Variablen gelöscht.

## <a name="delete-the-dedicated-resource-group"></a>Löschen der dedizierten Ressourcengruppe

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) bei Azure an.

2. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

3. Klicken Sie auf **Zu Arbeitsbereich wechseln**.

4. Klicken Sie unter **Allgemein** auf **Lösungen**.

5. Wählen Sie auf der Seite „Lösungen“ **Start-Stop-VM[Arbeitsbereich]** aus.

6. Wählen Sie auf der Seite **VMManagementSolution[Arbeitsbereich]** im Menü die Option **Löschen** aus.

    ![Löschen der VM-Verwaltungsfunktion](media/automation-solution-vm-management/vm-management-solution-delete.png)

7. Um die Ressourcengruppe zu löschen, die nur erstellt wurde, um „VMs außerhalb der Geschäftszeiten starten/beenden“ zu unterstützen, folgen Sie den Schritten, die im Artikel [Azure Resource Manager-Ressourcengruppe und Löschen von Ressourcen](../azure-resource-manager/management/delete-resource-group.md) beschrieben sind.

## <a name="delete-the-automation-account"></a>Löschen des Automation-Kontos

Führen Sie die folgenden Schritte aus, um Ihr Automation-Konto zu löschen, das für „VMs außerhalb der Geschäftszeiten starten/beenden“ vorgesehen ist.

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) bei Azure an.

2. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

3. Klicken Sie auf **Zu Arbeitsbereich wechseln**.

4. Klicken Sie unter **Allgemein** auf **Lösungen**.

5. Wählen Sie auf der Seite „Lösungen“ **Start-Stop-VM[Arbeitsbereich]** aus.

6. Wählen Sie auf der Seite **VMManagementSolution[Arbeitsbereich]** im Menü die Option **Löschen** aus.

7. Während die Informationen überprüft werden und die Funktion gelöscht wird, können Sie den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen. Nach dem Löschvorgang kehren Sie zur Seite „Lösungen“ zurück.

### <a name="unlink-workspace-from-automation-account"></a>Aufheben der Verknüpfung eines Arbeitsbereichs über ein Automation-Konto

Es gibt zwei Optionen für das Aufheben der Verknüpfung des Log Analytics-Arbeitsbereichs mit Ihrem Automation-Konto. Dieser Vorgang lässt sich im Automation-Konto oder verknüpften Arbeitsbereich durchführen.

Führen Sie die folgenden Schritte aus, um die Verknüpfung mit Ihrem Automation-Konto aufzuheben.

1. Wählen Sie im Azure-Portal **Automation-Konten** aus.

2. Öffnen Sie Ihr Automation-Konto, und wählen Sie auf der linken Seite unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

3. Wählen Sie auf der Seite **Verknüpfung des Arbeitsbereichs aufheben** die Option **Verknüpfung des Arbeitsbereichs aufheben** aus, und gehen Sie entsprechend den Aufforderungen vor.

   ![Seite „Verknüpfung des Arbeitsbereichs aufheben“](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Während versucht wird, die Verknüpfung mit dem Log Analytics-Arbeitsbereich aufzuheben, können Sie den Status im Menü unter **Benachrichtigungen** nachverfolgen.

Führen Sie die folgenden Schritte aus, um die Verknüpfung im Arbeitsbereich aufzuheben.

1. Wählen Sie im Azure-Portal **Log Analytics-Arbeitsbereiche** aus.

2. Wählen Sie im Arbeitsbereich unter **Verwandte Ressourcen** die Option **Automation-Konto** aus.

3. Klicken Sie auf der Seite „Automation-Konto“ auf **Kontoverknüpfung aufheben**, und reagieren Sie auf die Aufforderungen.

Während versucht wird, die Verknüpfung mit dem Automation-Konto aufzuheben, können Sie den Status im Menü unter **Benachrichtigungen** nachverfolgen.

### <a name="delete-automation-account"></a>Löschen des Automation-Kontos

1. Wählen Sie im Azure-Portal **Automation-Konten** aus.

2. Öffnen Sie Ihr Automation-Konto, und wählen Sie im Menü **Löschen** aus.

Während die Informationen überprüft werden und das Konto gelöscht wird, können Sie den Status im Menü unter **Benachrichtigungen** nachverfolgen.

## <a name="delete-the-feature"></a>Löschen des Features

Führen Sie die folgenden Schritte aus, um „VMs außerhalb der Geschäftszeiten starten/beenden“ aus Ihrem Automation-Konto zu löschen. Das Automation-Konto und der Log Analytics-Arbeitsbereich werden bei diesem Vorgang nicht gelöscht. Wenn Sie den Log Analytics-Arbeitsbereich nicht beibehalten möchten, müssen Sie ihn manuell löschen. Weitere Informationen zum Löschen des Arbeitsbereichs finden Sie unter [Löschen und Wiederherstellen eines Azure Log Analytics-Arbeitsbereichs](../azure-monitor/platform/delete-workspace.md).

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

2. Klicken Sie auf **Zu Arbeitsbereich wechseln**.

3. Klicken Sie unter **Allgemein** auf **Lösungen**.

4. Wählen Sie auf der Seite „Lösungen“ **Start-Stop-VM[Arbeitsbereich]** aus.

5. Wählen Sie auf der Seite **VMManagementSolution[Arbeitsbereich]** im Menü die Option **Löschen** aus.

    ![Löschen der VM-Verwaltungsfunktion](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. Bestätigen Sie im Fenster „Lösung löschen“, dass Sie die Funktion löschen möchten.

7. Während die Informationen überprüft werden und die Funktion gelöscht wird, können Sie den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen. Nach dem Löschvorgang kehren Sie zur Seite „Lösungen“ zurück.

8. Wenn Sie die von diesem Feature oder von Ihnen nachträglich erstellten [Ressourcen](automation-solution-vm-management.md#components) (z. B. Variablen, Zeitpläne usw.) nicht behalten möchten, müssen Sie sie manuell aus dem Konto löschen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum erneuten Aktivieren dieses Features finden Sie unter [Aktivieren des Startens/Beendens außerhalb der Geschäftszeiten](automation-solution-vm-management-enable.md).