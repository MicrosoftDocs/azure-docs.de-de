---
title: Übersicht über den Azure Monitor-Agent
description: Übersicht über den Azure Monitor-Agent (AMA) zur Sammlung von Überwachungsdaten aus dem Gastbetriebssystem virtueller Computer.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: cf64deb17bea508637debb5612231d355d523fbb
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95315582"
---
# <a name="azure-monitor-agent-overview-preview"></a>Übersicht über den Azure Monitor-Agent (Vorschauversion)
Der Azure Monitor-Agent (AMA) sammelt Überwachungsdaten aus dem Gastbetriebssystem virtueller Computer und übermittelt sie an Azure Monitor. Dieser Artikel enthält eine Übersicht über den Azure Monitor-Agent sowie Informationen zur Installation und zur Konfiguration der Datensammlung.

## <a name="relationship-to-other-agents"></a>Beziehung zu anderen Agents
Der Azure Monitor-Agent ersetzt die folgenden Agents, die derzeit von Azure Monitor verwendet werden, um Gastdaten von virtuellen Computern zu sammeln:

- [Log Analytics-Agent:](log-analytics-agent.md) Sendet Daten an den Log Analytics-Arbeitsbereich und unterstützt Azure Monitor für VMs und Überwachungslösungen.
- [Diagnoseerweiterung:](diagnostics-extension-overview.md) Sendet Daten an Azure Monitor-Metriken (nur Windows), Azure Event Hubs und Azure Storage.
- [Telegraf-Agent:](collect-custom-metrics-linux-telegraf.md) Sendet Daten an Azure Monitor-Metriken (nur Linux).

Neben der Konsolidierung dieser Funktionalität in einem einzelnen Agent bietet der Azure Monitor-Agent folgende Vorteile gegenüber den vorhandenen Agents:

- Überwachungsumfang: Konfigurieren Sie die Sammlung zentral für verschiedene Daten von verschiedenen virtuellen Computern.  
- Multi-Homing (Linux): Senden Sie Daten von virtuellen Linux-Computern an mehrere Arbeitsbereiche.
- Ereignisfilterung (Windows): Verwenden Sie XPATH-Abfragen, um zu filtern, welche Windows-Ereignisse gesammelt werden.
- Verbesserte Erweiterungsverwaltung: Der Azure Monitor-Agent verwendet eine neue Methode für den Umgang mit der Erweiterbarkeit. Diese Methode ist transparenter und besser steuerbar als Management Packs und Linux-Plug-Ins in den aktuellen Log Analytics-Agents.

### <a name="changes-in-data-collection"></a>Änderungen bei der Datensammlung
Die Methoden zum Definieren der Datensammlung für die vorhandenen Agents unterscheiden sich deutlich voneinander und sind jeweils mit gewissen Herausforderungen verbunden, die durch den Azure Monitor-Agent beseitigt werden.

- Der Log Analytics-Agent bezieht seine Konfiguration aus einem Log Analytics-Arbeitsbereich. Dies lässt sich zwar ganz einfach zentral konfigurieren, es ist jedoch schwierig, unabhängige Definitionen für verschiedene virtuelle Computer zu definieren. Es können nur Daten an einen Log Analytics-Arbeitsbereich gesendet werden.

- Die Diagnoseerweiterung verfügt über eine Konfiguration für jeden virtuellen Computer. Dadurch lassen sich ganz einfach unabhängige Definitionen für verschiedene virtuelle Computer definieren, die zentrale Verwaltung gestaltet sich jedoch schwierig. Daten können nur an Azure Monitor-Metriken, an Azure Event Hubs oder an Azure Storage gesendet werden. Für Linux-Agents ist der Telegraf-Agent erforderlich, um Daten an Azure Monitor-Metriken zu senden.

Der Azure Monitor-Agent verwendet [Datensammlungsregeln (Data Collection Rules, DCR)](data-collection-rule-overview.md), um die Daten zu konfigurieren, die von den einzelnen Agents gesammelt werden sollen. Datensammlungsregeln ermöglichen die Verwaltung von Sammlungseinstellungen im großen Stil sowie die Verwendung individueller, bereichsspezifischer Konfigurationen für Untergruppen von Computern. Dank ihrer Unabhängigkeit von Arbeitsbereichen und virtuellen Computern können sie einmalig definiert und für verschiedene Computer und Umgebungen wiederverwendet werden. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](data-collection-rule-azure-monitor-agent.md).

## <a name="should-i-switch-to-azure-monitor-agent"></a>Sollte ich zum Azure Monitor-Agent wechseln?
Der Azure Monitor-Agent wird gleichzeitig mit den [ allgemein verfügbaren Agents für Azure Monitor](agents-overview.md) verwendet, Sie können jedoch erwägen, Ihre VMs während der öffentlichen Vorschauphase des Azure Monitor-Agents von den aktuellen Agents abzuziehen. Berücksichtigen Sie die folgenden Faktoren, wenn Sie diese Entscheidung treffen.

- **Umgebungsanforderungen.** Azure Monitor-Agent unterstützt weniger Betriebssysteme, Umgebungen und Netzwerkanforderungen als die aktuellen Agents. Unterstützung von zukünftigen Umgebungen wie z. B. neuen Betriebssystemversionen und Netzwerk-Anforderungstypen wird wahrscheinlich nur in Azure Monitor-Agent bereitgestellt. Sie sollten bewerten, ob Ihre Umgebung von Azure Monitor-Agent unterstützt wird. Wenn dies nicht der Fall ist, müssen Sie den aktuellen Agent beibehalten. Wenn Azure Monitor-Agent Ihre aktuelle Umgebung unterstützt, sollten Sie einen Wechsel in Erwägung ziehen.
- **Risikotoleranz der Public Preview.** Azure Monitor-Agent wurde für die derzeit unterstützten Szenarien zwar gründlich getestet, befindet sich jedoch weiterhin in der öffentlichen Vorschauphase. Versionsupdates und Funktionsverbesserungen erfolgen häufig und führen möglicherweise zu Fehlern. Sie sollten das Risiko eines Fehlers im Agent auf Ihren virtuellen Computern beurteilen, der u. U. die Datensammlung verhindert. Wenn eine Lücke in der Datensammlung keine erheblichen Auswirkungen auf ihre Dienste hat, können Sie mit Azure Monitor-Agent fortfahren. Bei einer geringen Toleranz für Instabilität sollten Sie die allgemein verfügbaren Agents beibehalten, bis Azure Monitor-Agent diesen Status erreicht.
- **Aktuelle und neue Funktionsanforderungen.** Azure Monitor-Agent führt mehrere neue Funktionen wie Filterung, Bereichsdefinition und Multi-Homing ein, ist aber noch nicht gleichwertig mit den aktuellen Agents für andere Funktionen wie benutzerdefinierte Protokollsammlung und Integration mit Lösungen. Die meisten neuen Funktionen in Azure Monitor werden nur mit Azure Monitor-Agent zur Verfügung gestellt, sodass im Laufe der Zeit mehr Funktionen nur im neuen Agent verfügbar sind. Sie sollten berücksichtigen, ob der Azure Monitor-Agent die von Ihnen benötigten Funktionen hat und ob Sie auf einige Funktionen vorübergehend verzichten können, um andere wichtige Funktionen im neuen Agent zu erhalten. Wenn Azure Monitor-Agent über alle Kernfunktionen verfügt, die Sie benötigen, sollten Sie eine Umstellung darauf erwägen. Wenn es entscheidende Funktionen gibt, die Sie benötigen, fahren Sie mit dem aktuellen Agent fort, bis der Azure Monitor-Agent die Featureparität erreicht.
- **Toleranz für Nacharbeit.** Wenn Sie eine neue Umgebung mit Ressourcen wie Bereitstellungsskripts und Onboardingvorlagen einrichten, sollten Sie überprüfen, ob Sie diese nachbearbeiten können, wenn Azure Monitor-Agent allgemein verfügbar wird. Wenn der Aufwand für diese Nachbearbeitung minimal ist, behalten Sie die aktuellen Agents vorerst bei. Wenn erheblicher Arbeitsaufwand erforderlich ist, sollten Sie Ihre neue Umgebung mit dem neuen Agent einrichten. Die allgemeine Verfügbarkeit von Azure Monitor-Agent sowie die Veröffentlichung eines Veraltungsdatums für die Log Analytics-Agents wird für 2021 erwartet. Die aktuellen Agents werden nach Beginn der Veraltungsphase noch einige Jahre lang unterstützt.



## <a name="current-limitations"></a>Aktuelle Einschränkungen
Während der Public Preview-Phase des Azure Monitor-Agents gelten folgende Einschränkungen:

- Der Azure Monitor-Agent unterstützt keine Lösungen und Erkenntnisse wie Azure Monitor für VMs und Azure Security Center. Das einzige derzeit unterstützte Szenario ist die Sammlung von Daten unter Verwendung der von Ihnen konfigurierten Datensammlungsregeln. 
- Datensammlungsregeln müssen in der gleichen Region erstellt werden, in der sich auch die als Ziel verwendeten Log Analytics-Arbeitsbereiche befinden.
- Azure-VMs und Server mit Azure Arc-Unterstützung werden aktuell unterstützt. VM-Skalierungsgruppen, Azure Kubernetes Service und andere Computeressourcentypen werden derzeit nicht unterstützt.
- Der virtuelle Computer benötigt Zugriff auf die folgenden HTTPS-Endpunkte:
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>Koexistenz mit anderen Agents
Der Azure Monitor-Agent kann parallel zu den bereits vorhandenen Agents vorhanden sein, sodass deren Funktionen bei der Bewertung oder Migration weiterhin zur Verfügung stehen. Dies ist besonders wichtig, da während der Public Preview-Phase gewisse Einschränkungen bei der Unterstützung bereits vorhandener Lösungen bestehen. Eine doppelte Datensammlung kann jedoch zu einer Verzerrung von Abfrageergebnissen führen und zusätzliche Kosten für die Datenerfassung und -aufbewahrung zur Folge haben.

So wird von Azure Monitor für VMs beispielsweise der Log Analytics-Agent verwendet, um Leistungsdaten an einen Log Analytics-Arbeitsbereich zu senden. Gegebenenfalls haben Sie auch den Arbeitsbereich für die Sammlung von Windows- und Syslog-Ereignissen konfiguriert. Wenn Sie nun den Azure Monitor-Agent installieren und eine Datensammlungsregel für die gleichen Ereignisse und Leistungsdaten erstellen, führt dies zu doppelten Daten.


## <a name="costs"></a>Kosten
Für den Azure Monitor-Agent fallen keine Kosten an, möglicherweise aber für die erfassten Daten. Ausführliche Informationen zur Datensammlung und -aufbewahrung von Log Analytics sowie zu Kundenmetriken finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="data-sources-and-destinations"></a>Datenquellen und -ziele
Die folgende Tabelle gibt Aufschluss darüber, welche Datentypen Sie aktuell mit dem Azure Monitor-Agent unter Verwendung von Datensammlungsregeln sammeln und wohin diese Daten gesendet werden können. Unter [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine Liste mit Erkenntnissen und wichtigen Lösungen sowie mit anderen Lösungen, die den Azure Monitor-Agent verwenden, um andere Arten von Daten zu sammeln.


Der Azure Monitor-Agent sendet Daten an Azure Monitor-Metriken oder an einen Log Analytics-Arbeitsbereich, der Azure Monitor-Protokolle unterstützt.

| Data source | Destinations | BESCHREIBUNG |
|:---|:---|:---|
| Leistung        | Azure Monitor-Metriken<br>Log Analytics-Arbeitsbereich | Numerische Werte zur Messung der Leistung verschiedener Betriebssystem- und Workloadaspekte |
| Windows-Ereignisprotokolle | Log Analytics-Arbeitsbereich | An das Windows-System für die Ereignisprotokollierung gesendete Informationen |
| syslog             | Log Analytics-Arbeitsbereich | Informationen, die an das Linux-System für die Ereignisprotokollierung gesendet werden |


## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme
Eine Liste der Windows- und Linux-Betriebssystemversionen, die derzeit vom Azure Monitor-Agent unterstützt werden, finden Sie unter [Unterstützte Betriebssysteme](agents-overview.md#supported-operating-systems).



## <a name="security"></a>Sicherheit
Der Azure Monitor-Agent benötigt zwar keine Schlüssel, dafür aber eine [systemseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity). Für die Agent-Bereitstellung muss auf dem jeweiligen virtuellen Computer eine systemseitig zugewiesene verwaltete Identität aktiviert sein.

## <a name="networking"></a>Netzwerk
Der Azure Monitor-Agent unterstützt Azure-Diensttags (es sind sowohl AzureMonitor- als auch AzureResourceManager-Tags erforderlich), derzeit jedoch weder Azure Monitor Private Link-Bereiche noch direkte Proxys.


## <a name="next-steps"></a>Nächste Schritte

- [Installieren des Azure Monitor-Agents](azure-monitor-agent-install.md) auf Windows- und Linux-VMs
- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md), um Daten des Agents zu sammeln und an Azure Monitor zu senden.
