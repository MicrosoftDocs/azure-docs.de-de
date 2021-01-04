---
title: Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)
description: Enthält eine Übersicht über das Integritätsfeature von Azure Monitor für VMs, z. B. Informationen zum Anzeigen der Integrität Ihrer virtuellen Computer und Empfangen von Warnungen bei einem Fehlerstatus eines virtuellen Computers.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686519"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)
Mit dem Feature „Gastintegrität“ von Azure Monitor für VMs können Sie Informationen zur Integrität virtueller Computer anzeigen. Diese basieren auf verschiedenen Leistungsmessungen, für die in regelmäßigen Abständen Stichproben des Gastbetriebssystems genommen werden. Sie können die Integrität aller virtuellen Computer eines Abonnements oder einer Ressourcengruppe schnell überprüfen, einen Drilldown zu den ausführlichen Integritätsinformationen eines bestimmten virtuellen Computers ausführen oder sich proaktiv benachrichtigen lassen, wenn ein virtueller Computer nicht mehr fehlerfrei ist. 

## <a name="enable-virtual-machine-health"></a>Aktivieren des Integritätsfeatures für virtuelle Computer
Ausführliche Informationen zur Aktivierung des Features „Gastintegrität“ und zur Durchführung des Onboardings für virtuelle Computer finden Sie unter [Aktivieren des Features „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](vminsights-health-enable.md).

## <a name="pricing"></a>Preise
Für das Feature „Gastintegrität“ fallen keine direkten Kosten an, aber für die Erfassung und Speicherung von Integritätsstatusdaten im Log Analytics-Arbeitsbereich werden Kosten berechnet. Alle Daten werden in der Tabelle *HealthStateChangeEvent* gespeichert. Ausführliche Informationen zu Preismodellen und Kosten finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../platform/manage-cost-storage.md).

## <a name="view-virtual-machine-health"></a>Anzeigen der Integrität von virtuellen Computern
Auf der Seite **Erste Schritte** in der Spalte **Integrität der Gast-VM** finden Sie eine Kurzübersicht zur Integrität der einzelnen virtuellen Computer eines bestimmten Abonnements oder einer Ressourcengruppe. Der aktuelle Integritätsstatus der einzelnen virtuellen Computer wird angezeigt, und auf Symbolen für jede Gruppe wird die Anzahl von virtuellen Computern angegeben, die jeweils einen bestimmten Status aufweisen.

[![Seite „Erste Schritte“ mit „Integrität der Gast-VM“](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitore
Klicken Sie auf den Integritätsstatus eines virtuellen Computers, um den ausführlichen Status für seine einzelnen Monitore anzuzeigen. Mit jedem Monitor wird die Integrität einer bestimmten Komponente gemessen. Der Gesamtstatus des virtuellen Computers wird durch die Integrität seiner einzelnen Monitore bestimmt. 

![Beispiel für Monitore](media/vminsights-health-overview/monitors.png)

In der folgenden Tabelle sind die Aggregat- und Einheitenmonitore aufgeführt, die derzeit für die einzelnen virtuellen Computer verfügbar sind. 

| Überwachen | type | BESCHREIBUNG |
|:---|:---|:---|
| CPU-Auslastung | Einheit | Prozentuale Prozessorauslastung. |
| Dateisysteme | Aggregat | Aggregierte Integrität aller Dateisysteme auf Linux-VM. |
| Dateisystem  | Aggregat | Integrität des einzelnen Dateisystems auf Linux-VM. Der Name des Monitors ist der Name des Dateisystems. |
| Freier Speicherplatz | Einheit | Freier Speicherplatz im Dateisystem in Prozent. |
| Logische Datenträger | Aggregat | Aggregierte Integrität aller logischen Datenträger auf Windows-VM. |
| Logischer Datenträger  | Aggregat | Integrität der einzelnen logischen Datenträger auf Windows-VM. Der Name des Monitors ist der Name des Datenträgers. |
| Arbeitsspeicher | Aggregat | Aggregierte Integrität des Arbeitsspeichers auf der VM. |
| Verfügbarer Arbeitsspeicher | Einheit | Verfügbare Megabyte auf der VM. |

## <a name="health-states"></a>Integritätszustände
Von jedem Monitor werden jede Minute Stichproben der Werte auf dem Agent genommen, die dann mit den Kriterien für die einzelnen Integritätsstatus verglichen werden. Falls die Kriterien für einen bestimmten Status erfüllt sind, wird der Monitor auf den entsprechenden Status festgelegt. Wenn keines der Kriterien erfüllt ist, wird für den Monitor der Status „Fehlerfrei“ festgelegt. Daten werden vom Agent alle drei Minuten an Azure Monitor gesendet, sofern keine Statusänderung erfolgt. In diesem Fall werden die Daten sofort gesendet.

Jeder Monitor verfügt über ein Rückblickfenster und analysiert alle Stichproben, die während dieses Zeitraums genommen werden. Beispielsweise kann ein Monitor über ein Rückblickfenster von 240 Sekunden verfügen, in dem für mindestens zwei Stichprobenwerte (aber maximal die letzten drei) nach dem zulässigen Höchstwert gesucht wird. Auch wenn Stichproben der Werte regelmäßig genommen werden, kann die Stichprobenanzahl in einem bestimmten Fenster ggf. leicht variieren, falls es während des Betriebs des Agents zu einer Störung kommt.

Monitore können die Integritätsstatus aufweisen, die in der folgenden Tabelle angegeben sind (nur jeweils einen zur Zeit). Nachdem ein Monitor initialisiert wurde, befindet er sich im Status „Fehlerfrei“.

| Integritätsstatus | BESCHREIBUNG |
|:---|:---|
| Healthy  | Für den Monitor werden die Schwellenwerte für „Warnung“ oder „Kritisch“ derzeit nicht überschritten. |
| Warnung  | Für den Monitor wurde der Schwellenwert für „Warnung“ überschritten (falls zutreffend). |
| Kritisch | Für den Monitor wurde der Schwellenwert für „Kritisch“ überschritten (falls zutreffend). |
| Unbekannt  | Es wurden nicht genügend Daten gesammelt, um den Integritätsstatus zu ermitteln. |
| Disabled | Der Monitor ist derzeit deaktiviert. |
| Keine     | Der Monitor wurde gerade gestartet, und es wurde noch keine Auswertung durchgeführt, oder das überwachte Objekt ist nicht mehr vorhanden. |



Es gibt zwei Arten von Monitoren. Diese sind in der folgenden Tabelle angegeben.

| Überwachen | BESCHREIBUNG |
|:---|:---|
| Einheitenmonitor | Misst einen bestimmten Aspekt einer Ressource oder Anwendung. Dabei kann es sich um die Überprüfung eines Leistungszählers zum Ermitteln der Leistung der Ressource oder ihrer Verfügbarkeit handeln. |
| Aggregatmonitor | Eine Gruppierung mehrerer Monitore, um einen zusammengefassten Integritätsstatus angeben zu können. Ein Aggregatmonitor kann einen oder mehrere Einheitenmonitore sowie weitere Aggregatmonitore enthalten. |


  
### <a name="health-rollup-policy"></a>Richtlinie für Integritätsstatus-Rollups
Der Integritätsstatus eines virtuellen Computers wird anhand des Integritätsrollups seiner einzelnen Einheiten- und Aggregatmonitore bestimmt. Für jeden Aggregatmonitor wird eine Integritätsrollup-Richtlinie vom Typ „Schlechtester Status“ verwendet. Hierbei stimmt der Status des Aggregatmonitors mit dem Status des untergeordneten Monitors mit dem schlechtesten Integritätsstatus überein.  

Im folgenden Beispiel befindet sich der Monitor für freien Speicherplatz (**Free space**) im Status „Kritisch“, und dies wirkt sich bis hinauf zu den Aggregaten der Instanz und zu **File systems** (Dateisysteme) aus. Obwohl sich **CPU utilization** (CPU-Auslastung) im Status „Warnung“ befindet, wird für den virtuellen Computer der Status „Kritisch“ festgelegt, da dies der schlechteste Status in den Elementen darunter ist. Wenn für den freien Speicherplatz wieder in den Status „Fehlerfrei“ gewechselt wird, wird für den virtuellen Computer der Status „Warnung“ festgelegt, da dann „CPU-Auslastung“ der Monitor mit dem schlechtesten Status ist.

![Beispiel für Integritätsrollup](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>Monitordetails
Wählen Sie einen Monitor aus, um die zugehörigen Details anzuzeigen. Diese befinden sich auf den unten angegebenen Registerkarten.

**Übersicht**: Enthält eine Beschreibung des Monitors, den Zeitpunkt der letzten Auswertung und die Stichprobenwerte für die Ermittlung des aktuellen Integritätsstatus. Die Anzahl von Stichproben kann je nach Definition des Monitors und der Volatilität der Werte variieren.

[![Monitordetails: Übersicht](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**Verlauf**: Hier sind die Informationen zum Verlauf der Statusänderungen für den Monitor angegeben. Sie können die einzelnen Statusänderungen erweitern, um Werte anzuzeigen, die zur Ermittlung des Integritätsstatus ausgewertet werden. Klicken Sie auf **View configuration applied** (Angewendete Konfiguration anzeigen), um die Konfiguration des Monitors zu dem Zeitpunkt anzuzeigen, zu dem sich der Integritätsstatus geändert hat.



[![Monitordetails: Verlauf](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Konfiguration
Auf dieser Registerkarte können Sie die Konfiguration des Monitors für die ausgewählte VM anzeigen und ändern. Weitere Informationen finden Sie unter [Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs (Vorschau)](vminsights-health-enable.md).

[![Monitordetails: Konfiguration](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren des Features „Gastintegrität“ in Azure Monitor für VMs und Durchführen des Onboardings für Agents](vminsights-health-enable.md)
- [Konfigurieren von Monitoren mit dem Azure-Portal](vminsights-health-configure.md)
- [Konfigurieren von Monitoren mit Datensammlungsregeln](vminsights-health-configure-dcr.md)