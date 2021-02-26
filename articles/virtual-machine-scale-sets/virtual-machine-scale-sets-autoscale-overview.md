---
title: Übersicht über die automatische Skalierung mit Azure-VM-Skalierungsgruppen
description: Es werden unterschiedliche Möglichkeiten beschrieben, wie Sie eine Azure-VM-Skalierungsgruppe basierend auf der Leistung oder nach einem festen Zeitplan automatisch skalieren können.
author: avirishuv
ms.author: avverma
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: dbd2d6dc3034ff64ef6b5d4d36d836ce8462b154
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591393"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Übersicht über die automatische Skalierung mit Azure-VM-Skalierungsgruppen
Mit einer Azure-VM-Skalierungsgruppe kann die Anzahl von VM-Instanzen, von denen Ihre Anwendung ausgeführt wird, automatisch erhöht oder verringert werden. Mit diesem automatisierten und elastischen Verhalten wird der Verwaltungsaufwand reduziert, der für das Überwachen und Optimieren der Leistung Ihrer Anwendung anfällt. Sie erstellen Regeln, mit denen die angemessene Leistung definiert wird, die für eine positive Kundenerfahrung erforderlich ist. Wenn diese definierten Schwellenwerte erfüllt sind, werden von der automatischen Skalierung die Aktionen zum Anpassen der Kapazität Ihrer Skalierungsgruppe durchgeführt. Außerdem können Sie Ereignisse planen, um die Kapazität Ihrer Skalierungsgruppe zu festen Zeiten automatisch zu erhöhen oder zu verringern. Dieser Artikel enthält eine Übersicht darüber, welche Leistungsmetriken verfügbar sind und welche Aktionen von der automatischen Skalierung durchgeführt werden können.


## <a name="benefits-of-autoscale"></a>Vorteile der automatischen Skalierung
Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihrer Skalierungsgruppe. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen.

> [!NOTE]
> Wenn Sie automatische Instanzreparaturen für Ihre Skalierungsgruppe verwenden, kann die maximale Anzahl von Instanzen in der Skalierungsgruppe 200 sein. Weitere Informationen zu [automatischem Instanzreparaturen](./virtual-machine-scale-sets-automatic-instance-repairs.md).

Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Sie steuern, welche Metriken überwacht werden, z.B. CPU oder Arbeitsspeicher, wie lange die Anwendungslast einen bestimmten Schwellenwert einhalten muss und wie viele VM-Instanzen der Skalierungsgruppe hinzugefügt werden sollen.

Es kann sein, dass die Nachfrage nach Ihrer Anwendung abends oder am Wochenende abnimmt. Wenn diese Verringerung der Last für einen bestimmten Zeitraum anhält, können Sie Regeln zur automatischen Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu reduzieren. Mit dieser Aktion zum horizontalen Herunterskalieren werden die Kosten für die Ausführung Ihrer Skalierungsgruppe gesenkt, da Sie nur so viele Instanzen ausführen, wie für die Erfüllung der derzeitigen Nachfrage erforderlich sind.


## <a name="use-host-based-metrics"></a>Verwenden von hostbasierten Metriken
Sie können Regeln für die automatische Skalierung erstellen, in die Hostmetriken Ihrer VM-Instanzen integriert sind. Anhand von Hostmetriken können Sie die Leistung der VM-Instanzen einer Skalierungsgruppe verfolgen, ohne dass Sie zusätzliche Agents und Datensammlungen installieren oder konfigurieren müssen. Regeln für die automatische Skalierung, für die diese Metriken verwendet werden, können als Reaktion auf Änderungen der CPU-Auslastung, des Arbeitsspeicherbedarfs oder der Datenträgerzugriffe das horizontale Hochskalieren durchführen oder die Anzahl von VM-Instanzen erhöhen.

Regeln zur automatischen Skalierung mit hostbasierten Metriken können mit einem der folgenden Tools erstellt werden:

- [Azure portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-Befehlszeilenschnittstelle](tutorial-autoscale-cli.md)
- [Azure-Vorlage](tutorial-autoscale-template.md)

Zum Erstellen von Regeln für die automatische Skalierung, für die ausführlichere Leistungsmetriken verwendet werden, können Sie [die Azure-Diagnoseerweiterung für VM-Instanzen installieren und konfigurieren](#in-guest-vm-metrics-with-the-azure-diagnostics-extension) oder [Ihre Anwendung für die Verwendung von App Insights konfigurieren](#application-level-metrics-with-app-insights).

Für Regeln für die automatische Skalierung, bei denen hostbasierte Metriken, VM-Metriken auf Gastsystemen mit der Azure-Diagnoseerweiterung und App Insights verwendet werden, können die folgenden Konfigurationseinstellungen genutzt werden:

### <a name="metric-sources"></a>Metrikquellen
Für Regeln für die automatische Skalierung können Metriken aus einer der folgenden Quellen verwendet werden:

| Metrikquelle        | Anwendungsfall                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| Aktuelle Skalierungsgruppe    | Für hostbasierte Metriken, für die keine zusätzlichen Agents installiert oder konfiguriert werden müssen.                                  |
| Speicherkonto      | Die Azure-Diagnoseerweiterung schreibt Leistungsmetriken in Azure-Speicher, der dann genutzt wird, um Regeln für die automatische Skalierung auszulösen. |
| Service Bus-Warteschlange    | Ihre Anwendung oder andere Komponenten können Nachrichten in einer Azure Service Bus-Warteschlange übertragen, um Regeln auszulösen.                   |
| Application Insights | Ein Instrumentierungspaket, das in Ihrer Anwendung installiert ist und mit dem Metriken direkt aus der App gestreamt werden.                         |


### <a name="autoscale-rule-criteria"></a>Kriterien für Regeln für die automatische Skalierung
Die folgenden hostbasierten Metriken sind für die Nutzung verfügbar, wenn Sie Regeln für die automatische Skalierung erstellen. Wenn Sie die Azure-Diagnoseerweiterung oder App Insights verwenden, definieren Sie, welche Metriken mit Regeln für die automatische Skalierung überwacht und verwendet werden sollen.

| Metrikname               |
|---------------------------|
| CPU in Prozent            |
| Netzwerk eingehend                |
| Netzwerk ausgehend               |
| Datenträgerlesevorgänge in Bytes           |
| Datenträgerschreibvorgänge in Bytes          |
| Datenträgerlesevorgänge/Sek.  |
| Datenträgerschreibvorgänge/Sek. |
| Verbleibende CPU-Guthaben     |
| Verbrauchte CPU-Guthaben      |

Wenn Sie Regeln für die automatische Skalierung zum Überwachen einer bestimmten Metrik erstellen, stehen für die Regeln die folgenden Aktionen für die Metrikaggregation zur Verfügung:

| Aggregationstyp |
|------------------|
| Average          |
| Minimum          |
| Maximum          |
| Gesamt            |
| Letzter             |
| Anzahl            |

Die Regeln für die automatische Skalierung werden dann ausgelöst, wenn die Metriken mit einem der folgenden Operatoren mit Ihrem definierten Schwellenwert verglichen werden:

| Operator                 |
|--------------------------|
| Größer als             |
| Größer als oder gleich |
| Kleiner als                |
| Kleiner als oder gleich    |
| Gleich                 |
| Ungleich             |


### <a name="actions-when-rules-trigger"></a>Aktionen bei Auslösung von Regeln
Wenn eine Regel für die automatische Skalierung ausgelöst wird, kann Ihre Skalierungsgruppe auf eine der folgenden Arten automatisch skaliert werden:

| Skalierungsvorgang     | Anwendungsfall                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Anzahl erhöhen um   | Es wird eine feste Anzahl von VM-Instanzen erstellt. Nützlich für Skalierungsgruppen mit einer kleineren Anzahl von VMs.                                           |
| Prozentsatz erhöhen um | Eine prozentuale Erhöhung der VM-Instanzen. Ist gut für größere Skalierungsgruppen geeignet, bei denen eine Erhöhung um eine festgelegte Anzahl nicht zu einer merklichen Verbesserung der Leistung führt. |
| Anzahl erhöhen auf   | Es werden so viele VM-Instanzen erstellt, wie benötigt werden, um einen gewünschten Maximalwert zu erreichen.                                                            |
| Anzahl verringern um   | Es wird eine feste Anzahl von VM-Instanzen entfernt. Nützlich für Skalierungsgruppen mit einer kleineren Anzahl von VMs.                                           |
| Prozentsatz verringern um | Eine prozentuale Verringerung der VM-Instanzen. Ist gut für größere Skalierungsgruppen geeignet, bei denen eine Verringerung um eine festgelegte Anzahl nicht zu einer merklichen Verringerung des Ressourcenverbrauchs und der Kosten führt. |
| Anzahl verringern auf   | Es werden so viele VM-Instanzen entfernt, wie benötigt werden, um einen gewünschten Minimalwert zu erreichen.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>VM-Metriken auf Gastsystemen mit der Azure-Diagnoseerweiterung
Die Azure-Diagnoseerweiterung ist ein Agent, der auf einer VM-Instanz ausgeführt wird. Der Agent überwacht und speichert Leistungsmetriken in Azure-Speicher. Diese Leistungsmetriken enthalten ausführlichere Informationen zum Status der VM, z.B. *AverageReadTime* für Datenträger oder *PercentIdleTime* für die CPU. Sie können Regeln für die automatische Skalierung basierend auf ausführlicheren Informationen zur VM-Leistung erstellen, und nicht nur anhand des Prozentsatzes der CPU-Auslastung oder der Arbeitsspeichernutzung.

Zum Verwenden der Azure-Diagnoseerweiterung müssen Sie Azure-Speicherkonten für Ihre VM-Instanzen erstellen, den Azure-Diagnose-Agent installieren und die VMs dann so konfigurieren, dass bestimmte Leistungsindikatoren an das Speicherkonto gestreamt werden.

Weitere Informationen finden Sie in den Artikeln zum Aktivieren der Azure-Diagnoseerweiterung auf einem [virtuellen Linux-Computer](../virtual-machines/extensions/diagnostics-linux.md) oder auf einem [virtuellen Windows-Computer](../virtual-machines/extensions/diagnostics-windows.md).


## <a name="application-level-metrics-with-app-insights"></a>Metriken auf Anwendungsebene mit App Insights
Sie können Application Insights nutzen, um mehr Erkenntnisse zur Leistung Ihrer Anwendung zu erhalten. Sie installieren ein kleines Instrumentierungspaket in Ihrer Anwendung, mit dem die App überwacht wird und Telemetriedaten an Azure gesendet werden. Sie können Metriken wie die Reaktionszeiten Ihrer Anwendung, die Ladeleistung für Seiten und die Sitzungsanzahl überwachen. Diese Anwendungsmetriken können zum Erstellen von präzisen und eingebetteten Regeln für die automatische Skalierung verwendet werden, wenn Sie Regeln basierend auf verwertbaren Erkenntnissen auslösen, die sich auf die Kundenerfahrung auswirken können.

Weitere Informationen zu Application Insights finden Sie unter [Was ist Application Insights?](../azure-monitor/app/app-insights-overview.md).


## <a name="scheduled-autoscale"></a>Geplante automatische Skalierung
Sie können Regeln für die automatische Skalierung auch anhand von Zeitplänen erstellen. Mit diesen auf Zeitplänen basierenden Regeln können Sie die Anzahl von VM-Instanzen zu festen Zeiten automatisch skalieren. Bei leistungsbasierten Regeln kann es zu Auswirkungen auf die Leistung der Anwendung kommen, bevor die Regeln für die automatische Skalierung ausgelöst und die neuen VM-Instanzen bereitgestellt werden. Wenn Sie diese Nachfrage voraussehen können, werden die zusätzlichen VM-Instanzen entsprechend bereitgestellt und sind dann für die Nutzung durch Kunden und die Deckung des Anwendungsbedarfs vorhanden.

Die folgenden Beispiele enthalten Szenarien, für die die Nutzung von Regeln für die automatische Skalierung nach Zeitplan Vorteile haben kann:

- Automatisches horizontales Hochskalieren der Anzahl von VM-Instanzen zu Beginn des Arbeitstags, wenn die Nachfrage der Kunden ansteigt. Am Ende des Arbeitstags wird die Anzahl von VM-Instanzen automatisch horizontal herunterskaliert, um die Ressourcenkosten für die Nacht zu reduzieren, wenn die Nachfrage nach der Anwendung nur gering ist.
- Wenn eine Abteilung eine Anwendung an bestimmten Tagen des Monats oder des Geschäftszyklus stark nachfragt, wird die Anzahl von VM-Instanzen automatisch skaliert, um den zusätzlichen Bedarf zu decken.
- Wenn ein Marketingereignis, eine Werbeaktion oder ein Sale ansteht, können Sie die Anzahl von VM-Instanzen vor der voraussichtlich starken Kundennachfrage automatisch skalieren. 


## <a name="next-steps"></a>Nächste Schritte
Sie können Regeln für die automatische Skalierung mit hostbasierten Metriken mit einem der folgenden Tools erstellen:

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure-Befehlszeilenschnittstelle](tutorial-autoscale-cli.md)
- [Azure-Vorlage](tutorial-autoscale-template.md)

In dieser Übersicht wurde ausführlich beschrieben, wie Sie mit Regeln für die automatische Skalierung horizontal skalieren und die *Anzahl* von VM-Instanzen in Ihrer Skalierungsgruppe erhöhen oder verringern. Sie können auch vertikal skalieren, um die *Größe* der VM-Instanz zu erhöhen oder zu verringern. Weitere Informationen finden Sie unter [Vertikale automatische Skalierung mit VM-Skalierungsgruppen](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informationen zur Verwaltung Ihrer VM-Instanzen finden Sie unter [Verwalten einer VM-Skalierungsgruppe mit Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Informationen zum Generieren von Warnungen bei der Auslösung von Regeln für die automatische Skalierung finden Sie unter [Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen in Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Informieren Sie sich auch über das [Aufrufen eines Webhooks für Azure-Aktivitätsprotokollwarnungen](../azure-monitor/alerts/alerts-log-webhook.md).
