---
title: Verwalten von Updates und Patches für Ihre VMs in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie die Updateverwaltung verwenden können, um Updates und Patches für Ihre Azure- und Nicht-Azure-VMs zu verwalten.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915981"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Verwalten von Updates und Patches für Ihre VMs

Softwareupdates in der Azure Automation-Updateverwaltung bieten eine Gruppe von Tools und Ressourcen, mit denen die komplexe Aufgabe der Nachverfolgung und Anwendung von Softwareupdates auf Computer in Azure und Hybrid Cloud leichter verwaltet werden kann. Ein effektiver Softwareupdate-Verwaltungsprozess ist erforderlich, um die Betriebseffektivität aufrechtzuerhalten, Sicherheitsprobleme zu beheben und die Risiken gesteigerter Cybersicherheitsbedrohungen zu reduzieren. Da die Technologie sich kontinuierlich wandelt und immer wieder neue Sicherheitsrisiken auftreten, gebührt der effektiven Verwaltung von Softwareupdates besonderes Augenmerk.

> [!NOTE]
> Die Updateverwaltung unterstützt die Bereitstellung von Erstanbieterupdates sowie deren Vorabdownload. Hierzu sind Änderungen an den Systemen erforderlich, die aktualisiert werden. Informationen zum Konfigurieren dieser Einstellungen auf Ihren Systemen finden Sie unter [Konfigurieren von Windows Update-Einstellungen für die Azure Automation-Updateverwaltung](configure-wuagent.md).

Bevor Sie versuchen, Updates für Ihre virtuellen Computer zu verwalten, vergewissern Sie sich, dass Sie die Updateverwaltung mit einer der folgenden Methoden auf ihnen aktiviert haben:

* [Aktivieren der Updateverwaltung über ein Automation-Konto](enable-from-automation-account.md)
* [Aktivieren der Updateverwaltung mittels Durchsuchen im Azure-Portal](enable-from-portal.md)
* [Aktivieren der Updateverwaltung über ein Runbook](enable-from-runbook.md)
* [Aktivieren der Updateverwaltung über einen virtuellen Azure-Computer](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Beschränken des Bereichs für die Bereitstellung

Die Updateverwaltung verwendet eine Bereichskonfiguration innerhalb des Arbeitsbereichs, um die Computer, die Updates erhalten sollen, zu erreichen. Weitere Informationen finden Sie unter [Einschränken des Bereitstellungsbereichs für die Updateverwaltung](scope-configuration.md).

## <a name="compliance-assessment"></a>Kompatibilitätsbewertung

Bevor Sie Softwareupdates auf ihren Computern bereitstellen, überprüfen Sie die Ergebnisse der Updatekompatibilitätsbewertung für aktivierte Computer. Für jedes Softwareupdate wird der zugehörige Kompatibilitätszustand aufgezeichnet, nach Abschluss der Bewertung erfasst und per Massenvorgang an Azure Monitor-Protokolle weitergeleitet.

Auf einem Windows-Computer wird der Konformitätsscan standardmäßig alle 12 Stunden ausgeführt und innerhalb von 15 Minuten nach einem Neustart des Log Analytics-Agents für Windows initiiert. Die Auswertungsdaten werden dann an den Arbeitsbereich weitergeleitet, um die Tabelle **Updates** zu aktualisieren. Vor und nach der Updateinstallation wird ein Konformitätsscan ausgeführt, um fehlende Updates zu ermitteln. Die Ergebnisse werden jedoch nicht zum Aktualisieren der Auswertungsdaten in der Tabelle verwendet.

Lesen Sie unbedingt unsere Empfehlungen zum [Konfigurieren von Windows Update-Einstellungen für die Azure Automation-Updateverwaltung](configure-wuagent.md), um Probleme zu vermeiden, die eine korrekte Verwaltung verhindern.

Für einen Linux-Computer wird der Konformitätsscan standardmäßig jede Stunde durchgeführt. Wenn der Log Analytics-Agent für Linux neu gestartet wird, wird ein Konformitätsscan innerhalb von 15 Minuten eingeleitet.

Die Kompatibilitätsergebnisse werden für jeden bewerteten Computer in der Updateverwaltung angezeigt. Bis für einen neu für die Verwaltung aktivierten Computer aktualisierte Daten im Dashboard angezeigt werden, können bis zu 30 Minuten vergehen.

Weitere Informationen zum Anzeigen von Kompatibilitätsergebnissen finden Sie unter [Anzeigen von Updatebewertungen](view-update-assessments.md).

## <a name="deploy-updates"></a>Bereitstellen von Updates

Nach Überprüfung der Kompatibilitätsergebnisse ist die Softwareupdate-Bereitstellungsphase der Vorgang der Bereitstellung von Softwareupdates. Planen Sie zum Installieren von Updates eine Bereitstellung, die Ihrem Releasezeitplan und Wartungsfenster entspricht. Sie können auswählen, welche Updatetypen in die Bereitstellung eingeschlossen werden sollen. Beispielsweise können Sie kritische oder Sicherheitsupdates einschließen und Updaterollups ausschließen.

Lesen Sie [Bereitstellen von Updates und Überprüfen von Ergebnissen](deploy-updates.md), um zu erfahren, wie Sie eine Updatebereitstellung planen.

## <a name="review-update-deployments"></a>Überprüfen von Updatebereitstellungen

Nachdem die Bereitstellung abgeschlossen ist, überprüfen Sie den Prozess, um den Erfolg der Updatebereitstellung nach Computer oder Zielgruppe zu ermitteln. Weitere Informationen zum Überwachen des Bereitstellungsstatus finden Sie unter [Überprüfen des Bereitstellungsstatus](deploy-updates.md#check-deployment-status).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen von Warnungen, die Sie über die Ergebnisse der Updatebereitstellung informieren, finden Sie unter [Erstellen von Warnungen für die Updateverwaltung](configure-alerts.md).

* Sie können [Azure Monitor-Protokolle abfragen](query-logs.md), um Updatebewertungen, Bereitstellungen und andere verwandte Verwaltungsaufgaben zu analysieren. Es umfasst vordefinierte Abfragen, die Ihnen beim Einstieg helfen sollen.