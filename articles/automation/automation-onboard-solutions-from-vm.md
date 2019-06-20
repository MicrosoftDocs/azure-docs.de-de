---
title: Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand von einem virtuellen Azure-Computer
description: Erfahren Sie, wie Sie einen virtuellen Azure-Computer mit Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand integrieren, die Bestandteil von Azure Automation sind.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 38b5b233d21c0c5d79d7bcb6a145e6232b238b0e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133121"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand von einem virtuellen Azure-Computer

Azure Automation stellt Lösungen zum Verwalten der Sicherheitsupdates für das Betriebssystem, zum Nachverfolgen von Änderungen und für den Bestand bereit, der auf Ihren Computern installiert ist. Computer können auf unterschiedliche Weise integriert werden. Sie können die Lösung über einen virtuellen Computer, [über Ihr Automation-Konto](automation-onboard-solutions-from-automation-account.md), [durch Durchsuchen mehrerer Computer](automation-onboard-solutions-from-browse.md) oder per [Runbook](automation-onboard-solutions.md) integrieren. Dieser Artikel behandelt das Integrieren dieser Lösungen von einem virtuellen Azure-Computer.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="enable-the-solutions"></a>Aktivieren der Lösungen

Navigieren Sie zu einem vorhandenen virtuellen Computer. Wählen Sie unter **VORGÄNGE** **Updateverwaltung**, **Bestand** oder **Änderungsnachverfolgung** aus. Der virtuelle Computer kann in jeder Region vorhanden sein, unabhängig vom Standort Ihres Automation-Kontos. Beim Onboarding einer Lösung von einer VM aus benötigen Sie die Berechtigung `Microsoft.OperationalInsights/workspaces/read`, um zu ermitteln, ob die VM in einen Arbeitsbereich integriert ist. Weitere Informationen zu allgemein benötigten zusätzlichen Berechtigungen finden Sie unter [Erforderliche Berechtigungen für das Onboarding von Computern](automation-role-based-access-control.md#onboarding).

Um die Lösung nur für die VM zu aktivieren, stellen sicher, dass **Für diesen virtuellen Computer aktivieren** ausgewählt ist. Um mehrere Computer in die Lösung zu integrieren, wählen Sie **Für virtuelle Computer in diesem Abonnement aktivieren** und dann **Klicken, um zu aktivierende Computer auszuwählen** aus. Wie Sie mehrere Computer gleichzeitig integrieren, erfahren Sie unter [Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand](automation-onboard-solutions-from-automation-account.md).

Wählen Sie den Azure Log Analytics-Arbeitsbereich sowie das Automation-Konto und dann **Aktivieren** aus, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

![Integrieren der Updateverwaltungslösung](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Navigieren Sie zu den anderen Lösungen, und wählen Sie **Aktivieren** aus. Die Dropdownlisten für Log Analytics-Arbeitsbereich und Automation-Konto sind deaktiviert, da diese Lösungen denselben Arbeitsbereich und dasselbe Automation-Konto verwenden wie die zuvor aktivierte Lösung.

> [!NOTE]
> **Änderungsnachverfolgung** und **Bestand** verwenden dieselbe Lösung. Wenn eine dieser Lösungen aktiviert ist, ist die andere auch aktiviert.

## <a name="scope-configuration"></a>Bereichskonfiguration

Jede Lösung verwendet eine Bereichskonfiguration innerhalb des Arbeitsbereichs, um die Computer zu erreichen, welche die Lösung erhalten sollen. Die Bereichskonfiguration besteht aus einer Gruppe von mindestens einem gespeicherten Suchvorgang, der zum Begrenzen des Bereichs der Lösung auf bestimmte Computer verwendet wird. Wählen Sie in Ihrem Automation-Konto unter **ZUGEHÖRIGE RESSOURCEN** die Option **Arbeitsbereich** aus, um auf die Bereichskonfigurationen zuzugreifen. Wählen Sie im Arbeitsbereich unter **ARBEITSBEREICHSDATENQUELLEN** die Option **Bereichskonfigurationen** aus.

Falls der ausgewählte Arbeitsbereich nicht die Lösungen „Updateverwaltung“ und „Änderungsnachverfolgung“ enthält, werden die folgenden Bereichskonfigurationen erstellt:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Verfügt der ausgewählte Arbeitsbereich bereits über die Lösung, wird die Lösung nicht erneut bereitgestellt, und die Bereichskonfiguration wird nicht hinzugefügt.

Wählen Sie die Auslassungspunkte ( **...** ) bei einer Konfiguration und dann **Bearbeiten** aus. Wählen Sie im Bereich **Bereichskonfiguration bearbeiten** die Option **Computergruppen auswählen** aus. Im Bereich **Computergruppen** werden die gespeicherten Suchvorgänge angezeigt, die zum Erstellen der Bereichskonfiguration verwendet werden.

## <a name="saved-searches"></a>Gespeicherte Suchvorgänge

Wird ein Computer den Lösungen für Updateverwaltung, Änderungsnachverfolgung oder Bestand hinzugefügt, wird er auch einem der beiden gespeicherten Suchvorgänge in Ihrem Arbeitsbereich hinzugefügt. Die gespeicherten Suchvorgänge sind Abfragen, welche die Computer enthalten, die die Zielgruppe dieser Lösungen sind.

Wechseln Sie zu Ihrem Arbeitsbereich. Wählen Sie unter **Allgemein** die Option **Gespeicherte Suchvorgänge** aus. Die beiden von diesen Lösungen verwendeten gespeicherten Suchvorgänge werden in der folgenden Tabelle angezeigt:

|NAME     |Category (Kategorie)  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualisierungen        | Updates__MicrosoftDefaultComputerGroup         |

Wählen Sie einen der gespeicherten Suchvorgänge aus, um die zum Füllen der Gruppe verwendete Abfrage anzuzeigen. Die folgende Abbildung zeigt die Abfrage und ihre Ergebnisse:

![Gespeicherte Suchvorgänge](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Aufheben der Verknüpfung mit dem Arbeitsbereich

Die folgenden Lösungen sind vom Log Analytics-Arbeitsbereich abhängig:

* [Updateverwaltung](automation-update-management.md)
* [Änderungsnachverfolgung](automation-change-tracking.md)
* [Starten und Beenden von VMs außerhalb der Kernzeit](automation-solution-vm-management.md)

Wenn Sie Ihr Automation-Konto nicht länger in einen Log Analytics-Arbeitsbereich integriert sein soll, können Sie die Verknüpfung direkt im Azure-Portal aufheben.  Bevor Sie fortfahren, müssen Sie zuerst die zuvor erwähnten Lösungen entfernen, da dieser Prozess andernfalls nicht fortgesetzt werden kann. Lesen Sie den Artikel für die jeweilige Lösung, die Sie importiert haben, um die Schritte zu deren Entfernung zu verstehen.

Nach dem Entfernen dieser Lösungen können Sie die folgenden Schritte ausführen, um die Verknüpfung Ihres Automation-Kontos aufzuheben.

> [!NOTE]
> Einige Lösungen – einschließlich früherer Versionen der Azure SQL-Überwachungslösung – haben möglicherweise Automatisierungsressourcen erstellt und müssen möglicherweise vor dem Aufheben der Verknüpfung des Arbeitsbereichs entfernt werden.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie links auf der Seite „Automation-Konto“ im Abschnitt **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

2. Klicken Sie auf der Seite „Verknüpfung des Arbeitsbereichs aufheben“ auf **Verknüpfung des Arbeitsbereichs aufheben**.

   ![Seite „Verknüpfung des Arbeitsbereichs aufheben“](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Sie werden gefragt, ob Sie fortfahren möchten.

3. Während Azure Automation versucht, die Verknüpfung des Kontos mit Ihrem Log Analytics-Arbeitsbereich aufzuheben, können Sie den Fortschritt unter **Benachrichtigungen** im Menü nachverfolgen.

Wenn Sie die Lösung „Updateverwaltung“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Zeitpläne für Updates: Diese verfügen über Namen, die den Updatebereitstellungen entsprechen, die Sie erstellt haben.

* Für die Lösung erstellte Hybrid Worker-Gruppen: Jede wird ähnlich wie „machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8“ benannt.

Wenn Sie die Lösung „Starten und Beenden von VMs außerhalb der Kernzeit“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Starten und beenden Sie Zeitpläne für VM-Runbooks.
* Starten und beenden Sie VM-Runbooks.
* Variables

Alternativ können Sie Ihren Arbeitsbereich auch von Ihrem Automation-Konto in Ihrem Log Analytics-Arbeitsbereich trennen. Wählen Sie in Ihrem Arbeitsbereich unter **Verwandte Ressourcen** die Option **Automation-Konto** aus. Klicken Sie auf der Seite „Automation-Konto“ auf **Verknüpfung zu diesem Konto aufheben**.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit den Tutorials fort, um mehr über die Verwendung der Lösungen zu erfahren:

* [Tutorial: Verwalten von Updates für den virtuellen Computer](automation-tutorial-update-management.md)
* [Tutorial: Identifizieren von Software auf einem virtuellen Computer](automation-tutorial-installed-software.md)
* [Tutorial: Problembehandlung bei Änderungen auf einem virtuellen Computer](automation-tutorial-troubleshoot-changes.md)
