---
title: Erfahren Sie, wie Sie Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand in Azure Automation integrieren
description: Erfahren Sie, wie Sie einen virtuellen Azure-Computer mit Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand integrieren, die Bestandteil von Azure Automation sind
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: a1bb5534d2f98a4e5143038ab1d5fbbcc76184fe
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133186"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Integrieren von Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand

Azure Automation stellt Lösungen zum Verwalten der Sicherheitsupdates für das Betriebssystem, zum Nachverfolgen von Änderungen und für den Bestand bereit, der auf Ihren Computern installiert ist. Es gibt mehrere Möglichkeiten, Computer zu integrieren. Sie können die Lösung [über einen virtuellen Computer](automation-onboard-solutions-from-vm.md), [durch Durchsuchen mehrerer Computer](automation-onboard-solutions-from-browse.md), über Ihr Automation-Konto oder per [Runbook](automation-onboard-solutions.md) integrieren. Dieser Artikel behandelt das Integrieren dieser Lösungen über Ihr Automation-Konto.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="enable-solutions"></a>Aktivieren von Lösungen

Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **KONFIGURATIONSVERWALTUNG** entweder **Bestand** oder **Änderungsnachverfolgung** aus.

Wählen Sie den Log Analytics-Arbeitsbereich und das Automation-Konto aus, und klicken Sie auf **Aktivieren**, um die Lösung zu aktivieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

![Integrieren der Bestandslösung](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Wenn Sie Lösungen aktivieren, werden nur bestimmte Regionen zum Verknüpfen mit einem Log Analytics-Arbeitsbereich und einem Automation-Konto unterstützt.
>
> Eine Liste der unterstützten Zuordnungspaare finden Sie unter [Regionszuordnung für Automation-Konto und Log Analytics-Arbeitsbereich](how-to/region-mappings.md).

Die Lösung für Änderungsnachverfolgung und Bestand bietet die Möglichkeit zum Nachverfolgen von [Änderungen](automation-vm-change-tracking.md) und [Bestand](automation-vm-inventory.md) in Ihren virtuellen Computern. In diesem Schritt aktivieren Sie die Lösung auf einem virtuellen Computer.

Wenn Sie die Benachrichtigung erhalten haben, dass die Lösung für Änderungsnachverfolgung und Bestand integriert wurde, klicken Sie unter **KONFIGURATIONSVERWALTUNG** auf **Updateverwaltung**.

Mithilfe der Lösung zur Updateverwaltung können Sie Updates und Patches für Ihre Azure-Windows-VMs verwalten. Sie können den Status der verfügbaren Updates bewerten, die Installation der erforderlichen Updates planen und Bereitstellungsergebnisse überprüfen, um sicherzustellen, dass Updates erfolgreich auf die VM angewendet wurden. Mit dieser Aktion wird die Lösung für Ihren virtuellen Computer aktiviert.

Wählen Sie unter **UPDATEVERWALTUNG** die Option **Updateverwaltung** aus. Der ausgewählte Log Analytics-Arbeitsbereich ist derselbe, der im vorherigen Schritt verwendet wurde. Klicken Sie auf **Aktivieren**, um die Lösung für die Updateverwaltung zu integrieren. Es dauert ungefähr 15 Minuten, bis die Lösung aktiviert ist.

![Integrieren der Updatelösung](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Bereichskonfiguration

Jede Lösung verwendet eine Bereichskonfiguration innerhalb des Arbeitsbereichs, um die Computer zu erreichen, welche die Lösung erhalten sollen. Die Bereichskonfiguration besteht aus einer Gruppe von mindestens einem gespeicherten Suchvorgang, der zum Begrenzen des Bereichs der Lösung auf bestimmte Computer verwendet wird. Wählen Sie in Ihrem Automation-Konto unter **ZUGEHÖRIGE RESSOURCEN** die Option **Arbeitsbereich** aus, um auf die Bereichskonfigurationen zuzugreifen. Wählen dann im Arbeitsbereich unter **ARBEITSBEREICHSDATENQUELLEN** die Option **Bereichskonfigurationen** aus.

Falls der ausgewählte Arbeitsbereich noch nicht die Lösungen „Updateverwaltung“ und „Änderungsnachverfolgung“ enthält, werden die folgenden Bereichskonfigurationen erstellt:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Verfügt der ausgewählte Arbeitsbereich bereits über die Lösung, wird die Lösung nicht erneut bereitgestellt, und die Bereichskonfiguration wird nicht hinzugefügt.

## <a name="saved-searches"></a>Gespeicherte Suchvorgänge

Wenn ein Computer zu den Lösungen für die Updateverwaltung, Änderungsnachverfolgung und den Bestand hinzugefügt wird, werden diese Lösungen einem der beiden gespeicherten Suchvorgänge in Ihrem Arbeitsbereich hinzugefügt. Diese gespeicherten Suchvorgänge sind Abfragen, welche die Computer enthalten, die die Zielgruppe für diese Lösungen bilden.

Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie **Gespeicherte Suchvorgänge** unter **Allgemein** aus. Die beiden gespeicherten Suchvorgänge, die von diesen Lösungen verwendet werden, können Sie in der folgenden Tabelle anzeigen:

|NAME     |Category (Kategorie)  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aktualisierungen        | Updates__MicrosoftDefaultComputerGroup         |

Wählen Sie einen der gespeicherten Suchvorgänge aus, um die zum Füllen der Gruppe verwendete Abfrage anzuzeigen. Die folgende Abbildung zeigt die Abfrage und ihre Ergebnisse:

![Gespeicherte Suchvorgänge](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Integrieren von Azure-VMs

Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** entweder **Bestand** oder **Änderungsnachverfolgung** bzw. unter **UPDATEVERWALTUNG** die Option **Updateverwaltung** aus.

Klicken Sie auf **+ Azure-VMs hinzufügen**, und wählen Sie mindestens eine VM aus der Liste aus. Virtuelle Computer, die nicht aktiviert werden können, sind ausgegraut und können nicht ausgewählt werden. Azure-VMs können in jeder Region vorhanden sein, unabhängig vom Standort Ihres Automation-Kontos. Klicken Sie auf der Seite **Updateverwaltung aktivieren** auf **Aktivieren**. Diese Aktion fügt die ausgewählten virtuellen Computer zum gespeicherten Suchvorgang der Computergruppe für die Lösung hinzu.

![Aktivieren von Azure-VMs](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Integrieren eines Nicht-Azure-Computers

Computer, die nicht in Azure enthalten sind, müssen manuell hinzugefügt werden. Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** entweder **Bestand** oder **Änderungsnachverfolgung** bzw. unter **UPDATEVERWALTUNG** die Option **Updateverwaltung** aus.

Klicken Sie auf **Nicht-Azure-Computer hinzufügen**. Diese Aktion öffnet ein neues Browserfenster mit den [Anweisungen zum Installieren und Konfigurieren von Microsoft Monitoring Agent auf dem Computer](../azure-monitor/platform/log-analytics-agent.md), sodass der Computer mit der Berichterstellung für die Lösung beginnen kann. Wenn Sie einen Computer integrieren, der aktuell von System Center Operations Manager verwaltet wird, ist kein neuer Agent erforderlich. Die Arbeitsbereichsinformationen werden in den vorhandenen Agent eingegeben.

## <a name="onboard-machines-in-the-workspace"></a>Integrierte Computer im Arbeitsbereich

Manuell installierte Computer oder Computer, die bereits Berichte für Ihren Arbeitsbereich erstellen, müssen Azure Automation hinzugefügt werden, damit die Lösung aktiviert wird. Wählen Sie in Ihrem Automation-Konto unter **KONFIGURATIONSVERWALTUNG** entweder **Bestand** oder **Änderungsnachverfolgung** bzw. unter **UPDATEVERWALTUNG** die Option **Updateverwaltung** aus.

Wählen Sie **Computer verwalten** aus. Diese Aktion öffnet die Seite **Computer verwalten**. Auf dieser Seite können Sie die Lösung für eine ausgewählte Computergruppe, für alle verfügbaren Computer oder für alle aktuellen und zukünftigen Computer aktivieren. Die Schaltfläche **Computer verwalten** wird möglicherweise abgeblendet dargestellt, wenn Sie zuvor die Option **Auf allen verfügbaren und zukünftigen Computern aktivieren** ausgewählt haben.

![Gespeicherte Suchvorgänge](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Alle verfügbaren Computer

Um die Lösung für alle verfügbaren Computer zu aktivieren, wählen Sie **Auf allen verfügbaren Computern aktivieren** aus. Diese Aktion deaktiviert das Steuerelement zum einzelnen Hinzufügen von Computern. Mit dieser Aufgabe werden alle Namen der Computer hinzugefügt, die Berichte für den Arbeitsbereich und die Computergruppe der gespeicherten Suchabfrage erstellen. Diese Aktion deaktiviert die Schaltfläche **Computer verwalten**.

### <a name="all-available-and-future-machines"></a>Alle verfügbaren und zukünftigen Computer

Um die Lösung für alle verfügbaren und zukünftigen Computer zu aktivieren, wählen Sie **Auf allen verfügbaren und zukünftigen Computern aktivieren** aus. Mit dieser Option werden die gespeicherte Suchvorgänge und Bereichskonfigurationen aus dem Arbeitsbereich gelöscht. Diese Aktion öffnet die Lösung für alle Azure- und Nicht-Azure-Computer, die Berichte für den Arbeitsbereich erstellen. Diese Aktion deaktiviert die Schaltfläche **Computer verwalten** dauerhaft, da keine Bereichskonfiguration übrig ist.

### <a name="selected-machines"></a>Ausgewählte Computer

Um die Lösung für einen oder mehrere Computer zu aktivieren, wählen Sie **Auf ausgewählten Computern aktivieren** aus, und klicken Sie neben jedem Computer, den Sie der Lösung hinzufügen möchten, auf **Hinzufügen**. Mit dieser Aufgabe werden die Namen der ausgewählten Computer der Computergruppe der gespeicherten Suchabfrage für die Lösung hinzugefügt.

## <a name="unlink-workspace"></a>Aufheben der Verknüpfung mit dem Arbeitsbereich

Die folgenden Lösungen sind vom Log Analytics-Arbeitsbereich abhängig:

* [Updateverwaltung](automation-update-management.md)
* [Änderungsnachverfolgung](automation-change-tracking.md)
* [Starten und Beenden von VMs außerhalb der Kernzeit](automation-solution-vm-management.md)

Wenn Sie Ihr Automation-Konto nicht länger in einen Log Analytics-Arbeitsbereich integriert sein soll, können Sie die Verknüpfung direkt im Azure-Portal aufheben.  Bevor Sie fortfahren, müssen Sie zuerst die zuvor erwähnten Lösungen entfernen, da dieser Prozess andernfalls nicht fortgesetzt werden kann. Lesen Sie den Artikel für die jeweilige Lösung, die Sie importiert haben, um zu erfahren, welche Schritte zur Entfernung ausgeführt werden müssen.

Nach dem Entfernen dieser Lösungen können Sie die folgenden Schritte ausführen, um die Verknüpfung Ihres Automation-Kontos aufzuheben.

> [!NOTE]
> Einige Lösungen – einschließlich früherer Versionen der Azure SQL-Überwachungslösung – haben möglicherweise Automatisierungsressourcen erstellt und müssen möglicherweise vor dem Aufheben der Verknüpfung des Arbeitsbereichs entfernt werden.

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie links auf der Seite „Automation-Konto“ im Abschnitt **Zugehörige Ressourcen** die Option **Verknüpfter Arbeitsbereich** aus.

2. Klicken Sie auf der Seite „Verknüpfung des Arbeitsbereichs aufheben“ auf **Verknüpfung des Arbeitsbereichs aufheben**.

   ![Seite „Verknüpfung des Arbeitsbereichs aufheben“](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Sie werden gefragt, ob Sie fortfahren möchten.

3. Während Azure Automation versucht, die Verknüpfung des Kontos mit Ihrem Log Analytics-Arbeitsbereich aufzuheben, können Sie den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

Wenn Sie die Lösung „Updateverwaltung“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Zeitpläne für Updates: Diese verfügen über Namen, die den Updatebereitstellungen entsprechen, die Sie erstellt haben.

* Für die Lösung erstellte Hybrid Worker-Gruppen: Jede wird ähnlich wie „machine1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8“ benannt.

Wenn Sie die Lösung „Starten und Beenden von VMs außerhalb der Kernzeit“ verwendet haben, können Sie optional die folgenden Elemente entfernen, die nach dem Entfernen der Lösung nicht mehr benötigt werden.

* Starten und beenden Sie Zeitpläne für VM-Runbooks.
* Starten und beenden Sie VM-Runbooks.
* Variables

Alternativ können Sie Ihren Arbeitsbereich auch von Ihrem Automation-Konto in Ihrem Log Analytics-Arbeitsbereich trennen. Wählen Sie in Ihrem Arbeitsbereich unter **Verwandte Ressourcen** die Option **Automation-Konto** aus. Klicken Sie auf der Seite „Automation-Konto“ auf **Verknüpfung zu diesem Konto aufheben**.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit den Tutorials fort, um mehr über die Verwendung der Lösungen zu erfahren.

* [Tutorial: Verwalten von Updates für den virtuellen Computer](automation-tutorial-update-management.md)

* [Tutorial: Identifizieren von Software auf einem virtuellen Computer](automation-tutorial-installed-software.md)

* [Tutorial: Problembehandlung bei Änderungen auf einem virtuellen Computer](automation-tutorial-troubleshoot-changes.md)
