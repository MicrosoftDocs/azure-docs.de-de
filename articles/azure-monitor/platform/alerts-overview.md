---
title: Übersicht über die Benachrichtigung und Benachrichtigungsüberwachung in Azure
description: Übersicht über Benachrichtigungen in Azure Warnungen, klassische Warnungen und die Schnittstelle für Warnungen.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 6fb49baf8ab58ae6cfe7639cedcc4466810c8b96
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347452"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Überblick über Warnungen in Microsoft Azure 

In diesem Artikel wird beschrieben, was Warnungen sind, welche Vorteile sie haben und wie Sie mit der Nutzung beginnen.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Was sind Warnungen in Microsoft Azure?
Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. 

In diesem Artikel wird die einheitliche Oberfläche für Warnungen in Azure Monitor behandelt, die nun auch die in Log Analytics und Application Insights verwalteten Warnungen umfasst. Die [früheren Erfahrungen mit Warnungen](alerts-classic.overview.md) und Warnungstypen werden als **klassische Warnungen** bezeichnet. Sie können diese älteren Erfahrungen und den älteren Warnungstyp anzeigen, indem Sie oben auf der Warnungsseite auf **Klassische Warnungen anzeigen** klicken. 

## <a name="overview"></a>Übersicht

Das folgende Diagramm zeigt den Warnungsablauf. 

![Warnungsablauf](media/alerts-overview/Azure-Monitor-Alerts.svg)

Die Warnungsregeln sind von den Warnungen und Aktionen getrennt, die beim Auslösen einer Warnung ergriffen werden. 

**Warnungsregel**: Die Warnungsregel erfasst das Ziel und die Kriterien für Warnungen. Die Warnungsregel kann sich im Zustand „Aktiviert“ oder „Deaktiviert“ befinden. Warnungen werden nur ausgelöst, wenn sie aktiviert sind. 

Die wichtigsten Attribute einer Warnungsregel sind:

**Zielressource**: Die Zielressource definiert den Umfang und die für Warnungen verfügbaren Signale. Ein Ziel kann eine beliebige Azure-Ressource sein. Beispiele für Ziele: Virtuelle Computer, Speicherkonten, VM-Skalierungsgruppen, Log Analytics-Arbeitsbereiche oder Application Insights-Ressourcen. Für bestimmte Ressourcen (wie virtuelle Computer) können Sie mehrere Ressourcen als Ziel der Warnungsregel angeben.

**Signal**: Signale werden von der Zielressource ausgegeben und können verschiedene Typen annehmen. Metrik, Aktivitätsprotokoll, Application Insights und Protokoll.

**Kriterien**: Kriterien sind eine Kombination aus Signal und Logik, die auf eine Ressource angewendet wird. Beispiele: 
   - CPU in Prozent > 70 %
   - Serverantwortzeit > 4 ms 
   - Anzahl der Ergebnisse einer Protokollabfrage > 100

**Warnungsname**: Ein bestimmter Name für die Warnungsregel, der vom Benutzer konfiguriert wird.

**Warnungsbeschreibung**: Eine Beschreibung für die Warnungsregel, die vom Benutzer konfiguriert wird.

**Schweregrad**: Der Schweregrad der Warnung, nachdem die in der Warnungsregel angegebene Kriterien erfüllt sind. Der Schweregrad kann zwischen 0 und 4 liegen.

**Aktion**: Eine bestimmte Aktion, die ausgeführt wird, sobald die Warnung ausgelöst wird. Weitere Informationen finden Sie unter [Aktionsgruppen](../../azure-monitor/platform/action-groups.md).

## <a name="what-you-can-alert-on"></a>Wofür Sie Warnungen ausgeben können

Sie können Warnungen gemäß der Beschreibung in [Überwachen von Datenquellen](../../azure-monitor/platform/data-sources-reference.md) für Metriken und Protokolle ausgeben. Dazu gehören unter anderem folgende Ansprüche:
- Metrikwerte
- Protokollsuchabfragen
- Aktivitätsprotokollereignisse
- Integrität der zugrunde liegenden Azure-Plattform
- Tests für die Verfügbarkeit von Websites

Früher verfügten Azure Monitor-Metriken, Application Insights, Log Analytics und Service Health über separate Benachrichtigungsfunktionen. Im Laufe der Zeit verbesserte und kombinierte Azure sowohl die Benutzeroberfläche als auch verschiedene Benachrichtigungsmethoden. Diese Konsolidierung ist noch nicht abgeschlossen. Infolgedessen gibt es noch einige Benachrichtigungsfunktionen, die im neuen Warnungssystem noch nicht vorhanden sind.  

| **Überwachungsquelle** | **Signaltyp**  | **Beschreibung** | 
|-------------|----------------|-------------|
| Dienstintegrität | Aktivitätsprotokoll  | Nicht unterstützt. Siehe [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Webverfügbarkeitstests | Nicht unterstützt. Siehe [Webtestwarnungen](../../azure-monitor/app/monitor-web-app-availability.md). Verfügbar für jede Website, die für das Senden von Daten an Application Insights instrumentiert ist. Sie erhalten eine Benachrichtigung, wenn die Verfügbarkeit oder Reaktionsfähigkeit einer Website nicht den Erwartungen entspricht. |

## <a name="manage-alerts"></a>Warnungen verwalten
Sie können den Status einer Warnung festlegen, um anzugeben, an welchem Punkt des Lösungsprozesses sie sich befindet. Wenn die in der Warnungsregel angegebenen Kriterien erfüllt sind, wird eine Warnung erstellt oder ausgelöst, die den Status *Neu* aufweist. Sie können den Status ändern, wenn Sie eine Warnung bestätigen oder schließen. Alle Statusänderungen werden im Warnungsverlauf gespeichert.

Die folgenden Warnungsstatus werden unterstützt.

| Zustand | BESCHREIBUNG |
|:---|:---|
| Neu | Das Problem wurde gerade erkannt und noch nicht überprüft. |
| Bestätigt | Ein Administrator hat die Warnung überprüft und mit deren Bearbeitung begonnen. |
| Geschlossen | Das Problem wurde gelöst. Nachdem eine Warnung geschlossen wurde, können Sie diese erneut öffnen, indem Sie den Status ändern. |

**Warnungsstatus**: Der Warnungsstatus ist unabhängig von der **Überwachungsbedingung** und unterscheidet sich von dieser. Der Warnungsstatus wird vom Benutzer festgelegt. Die Überwachungsbedingung wird vom System bestimmt. Wenn eine Warnung ausgelöst wird, wird die Überwachungsbedingung der Warnung auf *Ausgelöst* festgelegt. Wenn die zugrunde liegende Bedingung gelöscht wurde, die die Warnung ausgelöst hat, wird die Überwachungsbedingung auf *Behoben* festgelegt. Der Warnungsstatus wird erst geändert, wenn der Benutzer ihn geändert hat. Erfahren Sie mehr über das [Ändern des Zustands Ihrer Warnungen und intelligenten Gruppen](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Intelligente Gruppen 
Intelligente Gruppen befinden sich in der Vorschauversion. 

Intelligente Gruppen sind Aggregationen von Warnungen, die auf Algorithmen des maschinellen Lernens basieren, und dabei helfen, Störungen bei Warnungen zu verringern sowie Probleme zu beheben. [Erfahren Sie mehr über intelligente Gruppen](https://aka.ms/smart-groups) und [zum Verwalten Ihrer intelligenten Gruppen](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Benutzeroberfläche „Warnungen“ 
Die Standardseite „Warnungen“ enthält eine Zusammenfassung der Warnungen, die in einem bestimmten Zeitfenster erstellt werden. Sie zeigt die gesamten Warnungen für jeden Schweregrad mit Spalten an, in denen die Gesamtanzahl der Warnungen im jeweiligen Status für jeden Schweregrad angegeben ist. Klicken Sie auf einen der Schweregrade, um die Seite [Alle Warnungen](#all-alerts-page) gefiltert nach diesem Schweregrad zu öffnen.

Es werden keine älteren [klassischen Warnungen](#classic-alerts) angezeigt oder verfolgt. Sie können die Abonnements oder Filterparameter ändern, um die Seite zu aktualisieren. 

![Seite „Warnungen“](media/alerts-overview/alerts-page.png)

Zum Filtern dieser Ansicht können Sie Werte in den Dropdownmenüs am oberen Rand der Seite auswählen.

| Column | BESCHREIBUNG |
|:---|:---|
| Abonnement | Wählen Sie bis zu fünf Azure-Abonnements aus. Nur Warnungen in den ausgewählten Abonnements sind in der Ansicht enthalten. |
| Ressourcengruppe | Wählen Sie eine einzelne Ressourcengruppe aus. Nur Warnungen mit Zielen in der ausgewählten Ressourcengruppe sind in der Ansicht enthalten. |
| Zeitbereich | Nur Warnungen, die innerhalb des ausgewählten Zeitfensters ausgelöst wurden, sind in der Ansicht enthalten. Unterstützte Werte sind die letzte Stunde, die letzten 24 Stunden, die letzten 7 Tage und die letzten 30 Tage. |

Klicken Sie auf die folgenden Werte am oberen Rand der Seite „Warnungen“, um eine andere Seite zu öffnen.

| Wert | BESCHREIBUNG |
|:---|:---|
| Warnungen gesamt | Die Gesamtanzahl der Warnungen, die den ausgewählten Kriterien entsprechen. Wählen Sie diesen Wert aus, um die Ansicht „Alle Warnungen“ ohne Filter zu öffnen. |
| Intelligente Gruppen | Die Gesamtanzahl anhand von Warnungen erstellter intelligenter Gruppen, die den ausgewählten Kriterien entsprechen. Wählen Sie diesen Wert aus, um die Liste „Intelligente Gruppen“ in der Ansicht „Alle Warnungen“ zu öffnen.
| Warnungsregeln gesamt | Die Gesamtanzahl der Warnungsregeln im ausgewählten Abonnement und der ausgewählten Ressourcengruppe. Wählen Sie diesen Wert aus, um die Ansicht „Regeln“ gefiltert nach ausgewählten Abonnements und ausgewählter Ressourcengruppe zu öffnen.


## <a name="manage-alert-rules"></a>Verwalten von Warnungsregeln
Klicken Sie auf **Warnungsregeln verwalten**, um die Seite **Regeln** anzuzeigen. Unter **Regeln** können Sie alle Warnungsregeln in Ihren Azure-Abonnements verwalten. Sie listet alle Warnungsregeln auf und kann basierend auf Zielressourcen, Ressourcengruppen, Regelnamen oder Status sortiert werden. Warnungsregeln können auf dieser Seite auch bearbeitet, aktiviert und deaktiviert werden.  

 ![Warnungsregeln](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Erstellen einer Warnungsregel
Warnungen können unabhängig vom Überwachungsdienst oder Signaltyp auf konsistente Weise erstellt werden. Alle ausgelösten Warnungen und zugehörigen Details sind auf einer einzelnen Seite verfügbar.
 
Sie erstellen eine neue Warnungsregel anhand der folgenden drei Schritte:
1. Wählen Sie das _Ziel_ für die Warnung aus.
1. Wählen Sie das _Signal_ aus den verfügbaren Signalen für das Ziel aus.
1. Geben Sie die _Logik_ an, die auf Daten vom Signal angewendet werden soll.
 
Bei dieser vereinfachten Erstellung müssen Sie nicht mehr die Überwachungsquelle oder -signale kennen, die unterstützt werden, bevor eine Azure-Ressource ausgewählt wird. Die Liste verfügbarer Signale wird automatisch auf Grundlage der ausgewählten Zielressource gefiltert. Basierend auf diesem Ziel werden Sie auch automatisch durch das Festlegen der Logik der Warnungsregel geführt.  

Weitere Informationen zum Erstellen von Warnungsregeln finden Sie unter [Erstellen, Anzeigen und Verwalten von Warnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

Warnungen sind übergreifend für mehrere Azure-Überwachungsdienste verfügbar. Informationen dazu, wie und wann Sie die jeweiligen Dienste verwenden, finden Sie unter [Überwachen von Azure-Anwendungen und -Ressourcen](../../azure-monitor/overview.md). 


## <a name="all-alerts-page"></a>Seite „Alle Warnungen“ 
Klicken Sie auf „Warnungen gesamt“, um die Seite mit allen Warnungen anzuzeigen. Hier können Sie eine Liste der Warnungen anzeigen, die innerhalb des ausgewählten Zeitfensters erstellt wurden. Sie können entweder eine Liste der einzelnen Warnungen oder eine Liste der intelligenten Gruppen anzeigen, die diese Warnungen enthalten. Klicken Sie auf das Banner am oberen Rand der Seite, um zwischen den Ansichten zu wechseln.

![Seite „Alle Warnungen“](media/alerts-overview/all-alerts-page.png)

Zum Filtern der Ansicht können Sie die folgenden Werte in den Dropdownmenüs am oberen Rand der Seite auswählen.

| Column | BESCHREIBUNG |
|:---|:---|
| Abonnement | Wählen Sie bis zu fünf Azure-Abonnements aus. Nur Warnungen in den ausgewählten Abonnements sind in der Ansicht enthalten. |
| Ressourcengruppe | Wählen Sie eine einzelne Ressourcengruppe aus. Nur Warnungen mit Zielen in der ausgewählten Ressourcengruppe sind in der Ansicht enthalten. |
| Ressourcentyp | Wählen Sie mindestens einen Ressourcentyp aus. Nur Warnungen mit Zielen des ausgewählten Typs sind in der Ansicht enthalten. Diese Spalte ist nur verfügbar, nachdem eine Ressourcengruppe angegeben wurde. |
| Resource | Wählen Sie eine Ressource aus. Nur Warnungen mit dieser Ressource als Ziel sind in der Ansicht enthalten. Diese Spalte ist nur verfügbar, nachdem ein Ressourcentyp angegeben wurde. |
| Severity | Wählen Sie einen Warnungsschweregrad oder *Alle* aus, um Warnungen aller Schweregrade einzuschließen. |
| Überwachungsbedingung | Wählen Sie eine Überwachungsbedingung oder *Alle* aus, um Warnungen aller Bedingungen einzuschließen. |
| Warnungsstatus | Wählen Sie einen Warnungsstatus oder *Alle* aus, um Warnungen aller Statusarten einzuschließen. |
| Überwachungsdienst | Wählen Sie einen Dienst oder *Alle* aus, um alle Dienste einzuschließen. Nur Warnungen, die anhand von Regeln erstellt wurden, die diesen Dienst als Ziel verwenden, sind enthalten. |
| Zeitbereich | Nur Warnungen, die innerhalb des ausgewählten Zeitfensters ausgelöst wurden, sind in der Ansicht enthalten. Unterstützte Werte sind die letzte Stunde, die letzten 24 Stunden, die letzten 7 Tage und die letzten 30 Tage. |

Klicken Sie am oberen Rand der Seite auf **Spalten**, um die anzuzeigenden Spalten auszuwählen. 

## <a name="alert-detail-page"></a>Seite „Warnungsdetails“
Die Seite „Warnungsdetails“ wird angezeigt, wenn Sie auf eine Warnung klicken. Sie enthält Details der Warnung und ermöglicht Ihnen das Ändern des Status.

![Warnungsdetails](media/alerts-overview/alert-detail2.png)

Die Seite „Warnungsdetails“ enthält die folgenden Abschnitte.

| `Section` | BESCHREIBUNG |
|:---|:---|
| Zusammenfassung | Zeigt die Eigenschaften und andere wichtige Informationen zur Warnung an. |
| Verlauf | Listet die einzelnen Aktionen, die von der Warnung ausgeführt wurden, und alle an der Warnung vorgenommenen Änderungen auf. Das beschränkt sich derzeit auf Statusänderungen. |
| Intelligente Gruppe | Informationen zur intelligenten Gruppe, der die Warnung angehört. Die *Warnungsanzahl* bezieht sich auf die Anzahl der Warnungen, die in der intelligenten Gruppe enthalten sind. Die Informationen enthalten andere Benachrichtigungen in der gleichen intelligenten Gruppe, die in den letzten 30 Tagen erstellt wurden, unabhängig vom Zeitfilter auf der Seite mit der Warnungsliste. Klicken Sie auf eine Warnung, um deren Details anzuzeigen. |
| Weitere Informationen | Zeigt weitere kontextbezogene Informationen für die Warnung an. Diese sind normalerweise für den Typ der Quelle spezifisch, die die Warnung erstellt hat. |


## <a name="classic-alerts"></a>Klassische Warnungen 

Die vor Juni 2018 verwendeten Azure Monitor-Metriken und Benachrichtigungsfunktionen für das Aktivitätsprotokoll werden als „Warnungen (klassisch)“ bezeichnet. 

Weitere Informationen finden Sie unter [Klassische Warnungen](./../../azure-monitor/platform/alerts-classic.overview.md).


## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu intelligenten Gruppen](https://aka.ms/smart-groups)
- [Informationen zu Aktionsgruppen](../../azure-monitor/platform/action-groups.md)
- [Verwalten Ihrer Warnungsinstanzen in Azure](https://aka.ms/managing-alert-instances)
- [Verwalten intelligenter Gruppen](https://aka.ms/managing-smart-groups)






