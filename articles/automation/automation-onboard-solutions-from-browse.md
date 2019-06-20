---
title: Erfahren Sie, wie Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand für mehrere VMs in Azure Automation integriert werden
description: Erfahren Sie, wie Sie einen virtuellen Azure-Computer mit Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand integrieren, die Bestandteil von Azure Automation sind
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e6359d57a1f4cce6ec89fd76ef343b515cafae6e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133138"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Aktivieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand für mehrere VMs

Azure Automation stellt Lösungen zum Verwalten der Sicherheitsupdates für das Betriebssystem, zum Nachverfolgen von Änderungen und für den Bestand bereit, der auf Ihren Computern installiert ist. Es gibt mehrere Möglichkeiten, Computer zu integrieren. Sie können die Lösung [über einen virtuellen Computer](automation-onboard-solutions-from-vm.md), über Ihr [Automation-Konto](automation-onboard-solutions-from-automation-account.md), durch Durchsuchen mehrerer Computer oder per [Runbook](automation-onboard-solutions.md) integrieren. Dieser Artikel behandelt das Integrieren dieser Lösungen durch Durchsuchen virtueller Computer in Azure.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="enable-solutions"></a>Aktivieren von Lösungen

Navigieren Sie im Azure-Portal zu **Virtuelle Computer**.

Wählen Sie über die Kontrollkästchen die virtuellen Computer aus, die Sie in Änderungsnachverfolgung und Bestand oder Updateverwaltung integrieren möchten. Die Integration kann für bis zu drei verschiedene Ressourcengruppen gleichzeitig durchgeführt werden. Azure-VMs können in jeder Region vorhanden sein, unabhängig vom Standort Ihres Automation-Kontos.

![Liste der VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Verwenden Sie die Filtersteuerelemente, um die Liste der virtuellen Computer zu ändern, und klicken Sie anschließend auf das oberste Kontrollkästchen, um alle virtuellen Computer in der Liste auszuwählen.

Klicken Sie in der Befehlsleiste auf **Dienste**, und wählen Sie **Änderungsnachverfolgung**, **Bestand** oder **Updateverwaltung**.

> [!NOTE]
> **Änderungsnachverfolgung** und **Bestand** verwenden dieselbe Lösung. Ist die eine Option aktiviert, ist die andere ebenfalls aktiviert.

Die folgende Abbildung zeigt die Updateverwaltung. Änderungsnachverfolgung und Bestand haben das gleiche Layout und Verhalten.

Die Liste der virtuellen Computer wird gefiltert, um nur die virtuellen Computer anzuzeigen, die sich in demselben Abonnement und Speicherort befinden. Wenn sich Ihr virtueller Computer in mehr als drei Ressourcengruppen befindet, werden die ersten drei Ressourcengruppen ausgewählt.

### <a name="resource-group-limit"></a> Onboarding-Einschränkungen

Für die Anzahl von Ressourcengruppen, die Sie für das Onboarding nutzen können, gelten [Resource Manager-Bereitstellungsgrenzwerte](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Resource Manager-Bereitstellungen (nicht zu verwechseln mit Updatebereitstellungen) sind auf fünf Ressourcengruppen pro Bereitstellung beschränkt. Zur Sicherstellung der Onboarding-Integrität sind zwei dieser Ressourcengruppen für die Konfiguration des Log Analytics-Arbeitsbereichs, des Automation-Kontos und der zugehörigen Ressourcen reserviert. Sie können also drei Ressourcengruppen für die Bereitstellung auswählen.

Verwenden Sie die Filtersteuerelemente, um virtuelle Computer aus verschiedenen Abonnements, Speicherorten und Ressourcengruppen auszuwählen.

![Integrieren der Updateverwaltungslösung](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Schauen Sie sich die Auswahlmöglichkeiten für den Log Analytics-Arbeitsbereich und das Automation-Konto an. Standardmäßig sind ein vorhandener Arbeitsbereich und ein neues Automation-Konto ausgewählt. Wenn Sie einen anderen Log Analytics-Arbeitsbereich und ein anderes Automation-Konto verwenden möchten, können Sie auf **BENUTZERDEFINIERT** klicken, um diese Komponenten auf der Seite **Benutzerdefinierte Konfiguration** auszuwählen. Wenn Sie einen Log Analytics-Arbeitsbereich auswählen, wird überprüft, ob eine Verknüpfung mit einem Automation-Konto besteht. Falls ein verknüpftes Automation-Konto gefunden wird, wird der folgende Bildschirm angezeigt. Klicken Sie auf **OK**, wenn Sie fertig sind.

![Auswählen von Arbeitsbereich und Konto](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Wenn der ausgewählte Arbeitsbereich nicht mit einem Automation-Konto verknüpft ist, wird der folgende Bildschirm angezeigt. Wählen Sie ein Automation-Konto aus, und klicken Sie auf **OK**, wenn Sie fertig sind.

![Kein Arbeitsbereich](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Wenn Sie Lösungen aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt.
>
> Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](how-to/region-mappings.md).

Deaktivieren Sie das Kontrollkästchen neben jedem virtuellen Computern, den Sie nicht aktivieren möchten. Die Auswahl für virtuelle Computer, die nicht aktiviert werden können, ist bereits aufgehoben.

Klicken Sie auf **Aktivieren**, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

## <a name="unlink-workspace"></a>Aufheben der Verknüpfung mit dem Arbeitsbereich

Die folgenden Lösungen sind vom Log Analytics-Arbeitsbereich abhängig:

* [Updateverwaltung](automation-update-management.md)
* [Änderungsnachverfolgung](automation-change-tracking.md)
* [Starten und Beenden von VMs außerhalb der Kernzeit](automation-solution-vm-management.md)

Wenn Sie Ihr Automation-Konto nicht länger in einen Log Analytics-Arbeitsbereich integriert sein soll, können Sie die Verknüpfung direkt im Azure-Portal aufheben. Bevor Sie fortfahren, müssen Sie zuerst die zuvor erwähnten Lösungen entfernen, da dieser Prozess andernfalls nicht fortgesetzt werden kann. Lesen Sie den Artikel für die jeweilige Lösung, die Sie importiert haben, um die Schritte zu deren Entfernung zu verstehen.

Nach dem Entfernen dieser Lösungen können Sie die folgenden Schritte ausführen, um die Verknüpfung Ihres Automation-Kontos aufzuheben.

> [!NOTE]
> Einige Lösungen – einschließlich früherer Versionen der Azure SQL-Überwachungslösung – haben möglicherweise Automatisierungsressourcen erstellt und müssen möglicherweise vor dem Aufheben der Verknüpfung des Arbeitsbereichs entfernt werden.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie links auf der Seite „Automation-Konto“ im Abschnitt **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

2. Klicken Sie auf der Seite „Verknüpfung des Arbeitsbereichs aufheben“ auf **Verknüpfung des Arbeitsbereichs aufheben**.

   ![Seite „Verknüpfung des Arbeitsbereichs aufheben“](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Sie werden gefragt, ob Sie fortfahren möchten.

3. Während Azure Automation versucht, die Verknüpfung des Kontos mit Ihrem Log Analytics-Arbeitsbereich aufzuheben, können Sie den Fortschritt unter **Benachrichtigungen** im Menü nachverfolgen.

Wenn Sie die Lösung „Updateverwaltung“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Zeitpläne für Updates: Diese verfügen über Namen, die den Updatebereitstellungen entsprechen, die Sie erstellt haben.

* Für die Lösung erstellte Hybrid Worker-Gruppen: Jede wird ähnlich wie „machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8“ benannt.

Wenn Sie die Lösung „Starten und Beenden von VMs außerhalb der Kernzeit“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Starten und beenden Sie Zeitpläne für VM-Runbooks.
* Starten und beenden Sie VM-Runbooks.
* Variables

Alternativ können Sie Ihren Arbeitsbereich auch von Ihrem Automation-Konto in Ihrem Log Analytics-Arbeitsbereich trennen. Wählen Sie in Ihrem Arbeitsbereich unter **Verwandte Ressourcen** die Option **Automation-Konto** aus. Klicken Sie auf der Seite **Automation-Konto** auf {0}Verknüpfung zu diesem Konto aufheben{0}.

## <a name="troubleshooting"></a>Problembehandlung

Beim Integrieren mehrerer Computer, werden einige Computer möglicherweise mit **Aktivieren nicht möglich** angezeigt. Es gibt verschiedene Gründe, warum einige Computer nicht aktiviert werden können. Die folgenden Abschnitte zeigen mögliche Gründe für den Status**Aktivieren nicht möglich** auf einem virtuellen Computer beim Versuch einer Integration.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM berichtet an einen anderen Arbeitsbereich: '\<workspaceName\>'.  Ändern der Konfiguration für die Verwendung zum Aktivieren

**Ursache:** Dieser Fehler zeigt an, dass der virtuelle Computer, den Sie integrieren möchten, an einen anderen Arbeitsbereich berichtet.

**Lösung**: Klicken Sie auf **Als Konfiguration verwenden**, um das angestrebte Automation-Konto und den angestrebten Log Analytics-Arbeitsbereich zu ändern.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM berichtet an einen Arbeitsbereich, der in diesem Abonnement nicht verfügbar ist.

**Ursache:** Der Arbeitsbereich, an den der virtuelle Computer berichtet:

* Befindet sich in einem anderen Abonnement oder
* Ist nicht mehr vorhanden oder
* Befindet sich in einer Ressourcengruppe, für die Sie keine Zugriffsberechtigungen haben

**Lösung**: Suchen Sie das Automation-Konto, das dem Arbeitsbereich zugeordnet ist, an den der virtuellen Computer berichtet, und integrieren Sie den virtuellen Computer, indem Sie die Bereichskonfiguration ändern.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM-Betriebssystemversion oder -Verteilung wird nicht unterstützt.

**Ursache:** Die Lösung wird nicht für alle Linux-Verteilungen oder alle Versionen von Windows unterstützt.

**Lösung:** Lesen Sie die [Liste der unterstützten Clients](automation-update-management.md#clients) für die Lösung.

### <a name="classic-vms-cannot-be-enabled"></a>Klassische VMs können nicht aktiviert werden.

**Ursache:** Virtuelle Computer, die das klassische Bereitstellungsmodell verwenden, werden nicht unterstützt.

**Lösung**: Migrieren Sie den virtuellen Computer zum Resource Manager-Bereitstellungsmodell. Informationen zur Vorgehensweise finden Sie in [Migrieren klassischer Bereitstellungsmodellressourcen](../virtual-machines/windows/migration-classic-resource-manager-overview.md)

### <a name="vm-is-stopped-deallocated"></a>Die VM wurde beendet. (Zuordnung aufgehoben)

**Ursache:** Der virtuelle Computer ist nicht im Status **Wird ausgeführt**.

**Lösung**: Um einen virtuellen Computer in eine Lösung zu integrieren, muss dieser ausgeführt werden. Klicken Sie auf den Inlinelink**VM starten**, um den virtuellen Computer zu starten, ohne die Seite zu verlassen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem die Lösung für Ihren virtuellen Computer aktiviert wurde, lesen Sie den Übersichtsartikel zur Updateverwaltung, um zu erfahren, wie Sie die Updatebewertung für Ihren Computer anzeigen können.

> [!div class="nextstepaction"]
> [Updateverwaltung – Anzeigen der Updatebewertung](./automation-update-management.md#viewing-update-assessments)

Zusätzliche Tutorials zu den Lösungen und ihrer Verwendung:

* [Tutorial: Verwalten von Updates für den virtuellen Computer](automation-tutorial-update-management.md)

* [Tutorial: Identifizieren von Software auf einem virtuellen Computer](automation-tutorial-installed-software.md)

* [Tutorial: Problembehandlung bei Änderungen auf einem virtuellen Computer](automation-tutorial-troubleshoot-changes.md)
