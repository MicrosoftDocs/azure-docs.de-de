---
title: Überwachen von virtuellen Azure-Computern mit Azure Monitor
description: Erfahren Sie, wie Sie Überwachungsdaten von VMs in Azure mithilfe von Azure Monitor sammeln und analysieren.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: fc89790c7d268bcfa0c08bd26249bc91979d7fca
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186897"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Überwachen von virtuellen Azure-Computern mit Azure Monitor
In diesem Artikel wird beschrieben, wie Sie mit Azure Monitor Überwachungsdaten von virtuellen Azure-Computern erfassen und analysieren, um deren Integrität aufrechtzuerhalten. Virtuelle Computer können wie alle [anderen Azure-Ressourcen](monitor-azure-resource.md) mit Azure Monitor auf Verfügbarkeit und Leistung überwacht werden, unterscheiden sich jedoch von anderen Ressourcen darin, dass Sie außerdem das Gastbetriebssystem und die darin ausgeführten Workloads überwachen müssen. 

> [!NOTE]
> Dieser Artikel bietet einen umfassenden Überblick über die Konzepte und Optionen zum Überwachen virtueller Computer in Azure Monitor. Wie Sie die Überwachung Ihrer virtuellen Computer schnell starten können, ohne sich auf die zugrunde liegenden Konzepte zu konzentrieren, erfahren Sie unter [Schnellstart: Überwachen eines virtuellen Azure-Computers mit Azure Monitor](../learn/quick-monitor-azure-vm.md).


## <a name="differences-from-other-azure-resources"></a>Unterschiede zu anderen Azure-Ressourcen
In dem Artikel [Überwachen eines virtuellen Azure-Computers mit Azure Monitor](monitor-azure-resource.md) wird das Überwachen von Daten beschrieben, die von Azure-Ressourcen generiert wurden. Außerdem wird erläutert, wie Sie die Funktionen von Azure Monitor nutzen können, um diese Daten zu analysieren und Warnungen dafür zu erstellen. Mit den folgenden Unterschieden können Sie die gleichen Überwachungsdaten von Azure-VMs erfassen und nutzen:

-  [Plattformmetriken](../platform/data-platform-metrics.md) werden automatisch für virtuelle Computer gesammelt, aber nur für den [VM-Host](#monitoring-data). Sie benötigen einen Agent, um Leistungsdaten aus dem Gastbetriebssystem zu sammeln. 
- Virtuelle Computer generieren keine [Ressourcenprotokolle](../platform/platform-logs-overview.md), die Einblicke in Vorgänge bereitstellen, die in einer Azure-Ressource ausgeführt wurden. Sie verwenden einen Agent, um Protokolldaten aus dem Gastbetriebssystem zu sammeln.
- Sie können [Diagnoseeinstellungen](../platform/diagnostic-settings.md) für eine VM erstellen, um Computerplattformmetriken an andere Ziele wie Speicher und Event Hubs zu senden, aber Sie können diese Diagnoseeinstellungen nicht im Azure-Portal konfigurieren. 

## <a name="monitoring-data"></a>Überwachungsdaten
VMs in Azure generieren [Protokolle](../platform/data-platform-logs.md) und [Metriken](../platform/data-platform-metrics.md), wie im folgenden Diagramm dargestellt.

![Übersicht](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>VM-Host
Virtuelle Computer in Azure generieren die folgenden Daten für den VM-Host wie andere Azure-Ressourcen, wie in [Überwachen von Daten](monitor-azure-resource.md#monitoring-data) beschrieben.

- [Plattformmetriken](../platform/data-platform-metrics.md) sind numerische Werte, die automatisch in regelmäßigen Abständen erfasst werden und einen Aspekt einer Ressource zu einem bestimmten Zeitpunkt beschreiben. Für den VM-Host werden Plattformmetriken gesammelt, aber Sie benötigen die Diagnoseerweiterung, um Metriken für das Gastbetriebssystem zu sammeln.
- [Aktivitätsprotokoll](../platform/platform-logs-overview.md): Bietet von außen Einblicke in die Vorgänge auf jeder Azure-Ressource im Abonnement (die Verwaltungsebene). Bei einem virtuellen Computer werden Informationen wie der Zustand beim Start und alle Konfigurationsänderungen angezeigt.


### <a name="guest-operating-system"></a>Gastbetriebssystem
Zum Sammeln von Daten aus dem Gastbetriebssystem einer VM benötigen Sie einen Agent, der lokal auf den einzelnen virtuellen Computern ausgeführt wird und Daten an Azure Monitor sendet. Mehrere Agents sind für Azure Monitor verfügbar, wobei jeweils verschiedene Daten gesammelt und Daten an verschiedene Speicherorte geschrieben werden. Einen ausführlichen Vergleich der einzelnen Agents finden Sie unter [Übersicht über die Azure Monitor-Agents](../platform/agents-overview.md). 

- [Log Analytics-Agent](../platform/agents-overview.md#log-analytics-agent): Verfügbar für virtuelle Computer in Azure, andere Cloudumgebungen und lokalen Einsatz. Sammelt Daten in Azure Monitor-Protokollen. Unterstützt Azure Monitor für VMs-und Überwachungslösungen. Derselbe Agent wird für den System Center Operations Manager verwendet.
- [Dependency-Agent](../platform/agents-overview.md#dependency-agent): Sammelt Daten über Prozesse, die auf dem virtuellen Computer ausgeführt werden, und über ihre Abhängigkeiten. Basiert auf dem Log Analytics-Agent zum Übertragen von Daten in Azure und unterstützt Azure Monitor für VMs, Dienstzuordnung und Wire Data 2.0-Lösungen.
- [Azure-Diagnose-Erweiterung](../platform/agents-overview.md#azure-diagnostics-extension): Nur für Azure Monitor-VMs verfügbar. Kann Daten an mehreren Speicherorten sammeln, wird aber hauptsächlich zum Sammeln von Gastleistungsdaten in Azure Monitor-Metriken für virtuelle Windows-Computer verwendet.
- [Telegraf-Agent](../platform/collect-custom-metrics-linux-telegraf.md): Sammelt Leistungsdaten von Linux-VMs in Azure Monitor-Metriken.


## <a name="configuration-requirements"></a>Konfigurationsanforderungen
Um alle Features von Azure Monitor zum Überwachen einer VM zu aktivieren, müssen Sie die Überwachungsdaten des VM-Hosts und des Gastbetriebssystems sowohl für [Azure Monitor-Metriken](../platform/data-platform-logs.md) als auch [Azure Monitor-Protokolle](../platform/data-platform-logs.md) sammeln. In der folgenden Tabelle wird die Konfiguration aufgelistet, die zum Aktivieren dieser Sammlung ausgeführt werden muss. Je nach ihren jeweiligen Anforderungen können Sie wählen, nicht alle diese Schritte auszuführen.

| Konfigurationsschritt | Abgeschlossene Aktionen | Aktivierte Features |
|:---|:---|:---|
| Keine Konfiguration | – In Metriken erfasste Hostplattformmetriken.<br>– Erfasstes Aktivitätsprotokoll. | – Metrik-Explorer für Host.<br>– Metrikwarnungen für Host.<br>– Aktivitätsprotokollwarnungen. |
| [Azure Monitor für VMs aktivieren](#enable-azure-monitor-for-vms) | – Log Analytics-Agent installiert.<br>– Dependency-Agent installiert.<br>– In Protokollen gesammelte Gastleistungsdaten.<br>– In Protokollen gesammelte Prozess- und Abhängigkeitsdetails. | – Leistungsdiagramme und -arbeitsmappen für Gastleistungsdaten.<br>– Protokollabfragen für Gastleistungsdaten.<br>– Protokollwarnungen für Gastleistungsdaten.<br>– Abhängigkeitsdiagramm. |
| [Installieren von Diagnoseerweiterung und Telegraf-Agent](#enable-diagnostics-extension-and-telegraf-agent) | – In Metriken gesammelte Gastleistungsdaten. | – Metrik-Explorer für Gast.<br>– Metrikwarnungen für Gast.  |
| [Konfigurieren des Log Analytics-Arbeitsbereichs](#configure-log-analytics-workspace) | – Auf dem Gast gesammelte Ereignisse. | – Protokollabfragen für Gastereignisse.<br>– Protokollwarnungen für Gastereignisse. |
| [Erstellen der Diagnoseeinstellung für die VM](#collect-platform-metrics-and-activity-log) | – In Protokollen erfasste Plattformmetriken.<br>– In Protokollen erfasstes Aktivitätsprotokoll. | – Protokollabfragen für Hostmetriken.<br>– Protokollwarnungen für Hostmetriken.<br>– Protokollabfragen für Aktivitätsprotokoll.

Die einzelnen Konfigurationsschritte werden in den folgenden Abschnitten beschrieben.

### <a name="enable-azure-monitor-for-vms"></a>Aktivieren von Azure Monitor für VMs
[Azure Monitor für VMs](vminsights-overview.md) ist ein [Einblick](../monitor-reference.md) in Azure Monitor, der das primäre Tool zur VM-Überwachung in Azure Monitor ist. Gegenüber standardmäßigen Azure Monitor-Features bietet Azure Monitor für VMs den folgenden Mehrwert.

- Vereinfachtes Onboarding von Log Analytics-Agent und Dependency-Agent zum Aktivieren der Überwachung eines VM-Gastbetriebssystems und von Workloads. 
- Vordefinierte Leistungsdiagramme und -arbeitsmappen, mit denen Sie Kernleistungsmetriken aus dem VM-Gastbetriebssystem analysieren und Trends erkennen können.
- Abhängigkeitsdiagramm, dass Prozesse anzeigt, die auf den einzelnen virtuellen Computern ausgeführt werden, und die mit anderen Computern und externen Quellen verbundenen Komponenten.

![Leistungsansicht „Azure Monitor für VMs“](media/monitor-vm-azure/vminsights-01.png)

![Zuordnungsansicht „Azure Monitor für VMs“](media/monitor-vm-azure/vminsights-02.png)


Aktivieren Sie Azure Monitor für VMs von der **Einblicke**-Option im VM-Menü des Azure-Portals aus. Weitere Informationen und andere Konfigurationsmethoden finden Sie unter [Aktivieren von Azure Monitor für VMs: Übersicht](vminsights-enable-overview.md).

![Aktivieren von Azure Monitor für VMs](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Konfigurieren des Log Analytics-Arbeitsbereichs
Der Log Analytics-Agent sendet von Azure Monitor für VMs verwendete Daten an einen [Log Analytics-Arbeitsbereich](../platform/data-platform-logs.md). Sie können die Sammlung zusätzlicher Leistungsdaten, Ereignisse und anderer Überwachungsdaten durch den Agent aktivieren, indem Sie den Log Analytics-Arbeitsbereich konfigurieren. Er muss nur einmal konfiguriert werden, da jeder Agent, der eine Verbindung mit dem Arbeitsbereich herstellt, die Konfiguration automatisch herunterlädt und sofort mit der Erfassung der definierten Daten beginnt. 

Sie können die Konfiguration für den Arbeitsbereich direkt von Azure Monitor für VMs aus aufrufen, indem Sie **Arbeitsbereichskonfiguration** in **Erste Schritte** auswählen. Klicken Sie auf den Namen des Arbeitsbereichs, um das Menü zu öffnen.

![Arbeitsbereichskonfiguration](media/monitor-vm-azure/workspace-configuration.png)

Wählen Sie im Arbeitsbereichsmenü **Erweiterte Einstellungen** aus und dann **Daten**, um Datenquellen zu konfigurieren. Wählen Sie für Windows-Agents **Windows-Ereignisprotokolle** aus, und fügen Sie gängige Ereignisprotokolle wie *System* und *Anwendung* hinzu. Wählen Sie für Linux-Agents **Syslog** aus, und fügen Sie allgemeine Funktionen wie *Kern* und *Daemon* hinzu. Eine Liste der verfügbaren Datenquellen und nähere Informationen zu ihrer Konfiguration finden Sie unter [Agent-Datenquellen in Azure Monitor](../platform/agent-data-sources.md). 

![Konfigurieren von Ereignissen](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Sie können Leistungsindikatoren so konfigurieren, dass sie von der Arbeitsbereichskonfiguration aus gesammelt werden. Diese sind jedoch möglicherweise mit den von Azure Monitor für VMs gesammelten Leistungsindikatoren redundant. Azure Monitor für VMs erfasst die gängigsten Indikatoren einmal pro Minute. Konfigurieren Sie nur die Leistungsindikatoren, die vom Arbeitsbereich erfasst werden sollen, wenn Sie Leistungsindikatoren sammeln möchten, die nicht bereits von Azure Monitor für VMs gesammelt wurden, oder wenn vorhandene Abfragen Leistungsdaten verwenden.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Aktivieren von Diagnoseerweiterung und Telegraf-Agent
Azure Monitor für VMs basiert auf dem Log Analytics-Agent, der Daten an einen Log Analytics-Arbeitsbereich sendet. Dies unterstützt mehrere Features von Azure Monitor, z. B. [Protokollabfragen](../log-query/log-query-overview.md), [Protokollwarnungen](../platform/alerts-log.md) und [Arbeitsmappen](../platform/workbooks-overview.md). Die [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) erfasst Leistungsdaten vom Gastbetriebssystem der virtuellen Windows-Computer in Azure Storage und sendet optional Leistungsdaten an [Azure Monitor-Metriken](../platform/data-platform-metrics.md). Bei virtuellen Linux-Computern ist der [Telegraf-Agent](../platform/collect-custom-metrics-linux-telegraf.md) zum Senden von Daten an Azure-Metriken erforderlich.  Dieser aktiviert andere Features von Azure Monitor wie [Metrik-Explorer](../platform/metrics-getting-started.md) und [Metrikwarnungen](../platform/alerts-metric.md). Sie können die Diagnoseerweiterung auch so konfigurieren, dass Ereignisse und Leistungsdaten außerhalb von Azure Monitor mithilfe von Azure Event Hubs gesendet werden.

Installieren Sie die Diagnoseerweiterung für einen einzelnen virtuellen Windows-Computer im Azure-Portal von der Option **Diagnoseeinstellung** im VM-Menü aus. Wählen Sie die Option aus, um **Azure Monitor** auf der Registerkarte **Senken** zu aktivieren. Informationen zum Aktivieren der Erweiterung über eine Vorlage oder Befehlszeile für mehrere virtuelle Computer finden Sie unter [Installation und Konfiguration](../platform/diagnostics-extension-overview.md#installation-and-configuration). Im Gegensatz zum Log Analytics-Agent werden die zu sammelnden Daten in der Konfiguration für die Erweiterung auf jedem virtuellen Computer definiert.

![Diagnoseeinstellung](media/monitor-vm-azure/diagnostic-setting.png)

Ausführliche Informationen zum Konfigurieren der Telegraf-Agents auf virtuellen Linux-Computern finden Sie unter [Installieren und Konfigurieren von Telegraf](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf). Die Menüoption **Diagnoseeinstellung** ist für Linux verfügbar. Sie ermöglicht jedoch nur, Daten an den Azure-Speicher zu senden.

### <a name="collect-platform-metrics-and-activity-log"></a>Sammeln von Plattformmetriken und Aktivitätsprotokoll
Sie können die Plattformmetriken und das Aktivitätsprotokoll anzeigen, die für jeden VM-Host im Azure-Portal gesammelt wurden. Erfassen Sie diese Daten in demselben Log Analytics-Arbeitsbereich wie Azure Monitor für VMs, um Sie mit den anderen Überwachungsdaten zu analysieren, die für den virtuellen Computer gesammelt wurden. Diese Sammlung wird mit einer [Diagnoseeinstellung](../platform/diagnostic-settings.md) konfiguriert. Erfassen Sie das Aktivitätsprotokoll mit einer [Diagnoseeinstellung für das Abonnement](../platform/diagnostic-settings.md#create-in-azure-portal).

Sammeln Sie Plattformmetriken mit einer Diagnoseeinstellung für den virtuellen Computer. Im Gegensatz zu anderen Azure-Ressourcen können Sie keine Diagnoseeinstellung für einen virtuellen Computer im Azure-Portal erstellen, sondern müssen [eine andere Methode](../platform/diagnostic-settings.md#create-using-powershell) verwenden. Die folgenden Beispiele veranschaulichen, wie Sie mithilfe von PowerShell und CLI Metriken für einen virtuellen Computer erfassen.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Überwachen im Azure-Portal 
Nachdem Sie die Sammlung von Überwachungsdaten für einen virtuellen Computer konfiguriert haben, haben Sie mehrere Optionen für den Zugriff auf die Daten im Azure-Portal:

- Verwenden Sie das **Azure Monitor**-Menü, um auf Daten aus allen überwachten Ressourcen zuzugreifen. 
- Verwenden Sie Azure Monitor für VMs, um Gruppen von virtuellen Computern flexibel zu überwachen.
- Analysieren Sie Daten für einen einzelnen virtuellen Computer über das Menü im Azure-Portal. In der folgenden Tabelle werden verschiedene Optionen zum Überwachen des Menüs für virtuelle Computer aufgelistet.

![Überwachen im Azure-Portal](media/monitor-vm-azure/monitor-menu.png)

| Menüoption | BESCHREIBUNG |
|:---|:---|
| Übersicht | Zeigt [Plattformmetriken](../platform/data-platform-metrics.md) für den VM-Host an. Klicken Sie auf ein Diagramm, um mit diesen Daten im [Metrik-Explorer](../platform/metrics-getting-started.md) zu arbeiten. |
| Aktivitätsprotokoll | [Aktivitätsprotokolleinträge](../platform/activity-log.md#view-the-activity-log), die für den aktuellen virtuellen Computer gefiltert werden. |
| Einblicke | Öffnet [Azure Monitor für VMs](./vminsights-overview.md) mit der Zuordnung für den aktuell ausgewählten virtuellen Computer. |
| Alerts | Zeigt [Warnungen](../platform/alerts-overview.md) für die aktuelle VM an.  |
| Metriken | Öffnet den [Metrik-Explorer](../platform/metrics-getting-started.md), wobei der Bereich auf den aktuellen virtuellen Computer festgelegt ist. |
| Diagnoseeinstellungen | Aktiviert und konfiguriert die [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) für die aktuelle VM. |
| Advisor-Empfehlungen | Empfehlungen für die aktuelle VM vom [Azure Advisor](../../advisor/index.yml). |
| Protokolle | Öffnet [Log Analytics](../log-query/log-analytics-overview.md), wobei der [Bereich](../log-query/scope.md) auf den aktuellen virtuellen Computer festgelegt ist. |
| Verbindungsmonitor | Öffnet [Network Watcher – Verbindungsmonitor](../../network-watcher/connection-monitor-overview.md), um die Verbindungen zwischen dem aktuellen virtuellen Computer und anderen VMs zu überwachen. |


## <a name="analyzing-metric-data"></a>Analysieren von Metrikdaten
Sie können Metriken für virtuelle Computer mit dem Metrik-Explorer analysieren, indem Sie im Menü des virtuellen Computers **Metriken** öffnen. Ausführliche Informationen zur Verwendung dieses Tools finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](../platform/metrics-getting-started.md). 

Drei Namespaces werden von virtuellen Computern für Metriken verwendet:

| Namespace | BESCHREIBUNG | Anforderung |
|:---|:---|:---|
| Host für virtuelle Computer | Hostmetriken werden automatisch für alle virtuellen Azure-Computer gesammelt. Eine ausführliche Liste der Metriken finden Sie unter [Microsoft.Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Werden automatisch gesammelt, ohne dass eine Konfiguration erforderlich ist. |
| Gast (klassisch) | Eingeschränkte Gruppe von Leistungsdaten aus Gastbetriebssystem und Anwendung. Im Metrik-Explorer, aber nicht in anderen Azure Monitor-Features wie Metrikwarnungen verfügbar.  | [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) ist installiert. Daten werden aus dem Azure-Speicher gelesen.  |
| VM-Gast | Leistungsdaten aus Gastbetriebssystem und Anwendung sind für alle Azure Monitor-Features über Metriken verfügbar. | Für Windows ist die [Diagnoseerweiterung](../platform/diagnostics-extension-overview.md) mit aktivierter Azure Monitor-Senke installiert. Für Linux ist der [Telegraf-Agent](../platform/collect-custom-metrics-linux-telegraf.md) installiert. |

![Metrik-Explorer im Azure-Portal](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Analysieren von Protokolldaten
Die folgenden Daten werden von virtuellen Azure-Computern in Azure Monitor-Protokollen gesammelt. 

Azure Monitor für VMs aktiviert die Auflistung eines vordefinierten Satzes von Leistungsindikatoren, die in die *InsightsMetrics*-Tabelle geschrieben werden. Dies ist die gleiche Tabelle, die von [Azure Monitor für Container](container-insights-overview.md) verwendet wird. 

| Datenquelle | Requirements (Anforderungen) | Tabellen |
|:---|:---|:---|
| Azure Monitor für VMs | Aktivieren Sie dies auf jedem virtuellen Computer. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>Weitere Informationen finden Sie unter [Abfragen von Protokollen aus Azure Monitor für VMs](vminsights-log-search.md). |
| Aktivitätsprotokoll | Diagnoseeinstellung für das Abonnement. | AzureActivity |
| Hostmetriken | Diagnoseeinstellung für die VM. | AzureMetrics |
| Datenquellen aus dem Gastbetriebssystem | Aktivieren Sie den Log Analytics-Agent, und konfigurieren Sie Datenquellen. | Nutzen Sie die Dokumentation zu den einzelnen Datenquellen. |


> [!NOTE]
> Vom Log Analytics-Agent gesammelte Leistungsdaten werden in die *Perf*-Tabelle geschrieben, während Azure Monitor für VMs sie in der *InsightsMetrics*-Tabelle erfasst. Dabei handelt es sich um die gleichen Daten, die Tabellen haben jedoch eine andere Struktur. Wenn Sie über Abfragen verfügen, die auf *Perf* basieren, müssen sie zur Verwendung von *InsightsMetrics* umgeschrieben werden.


## <a name="alerts"></a>Alerts
[Warnungen](../platform/alerts-overview.md) in Azure Monitor benachrichtigen Sie proaktiv, wenn wichtige Bedingungen in ihren Überwachungsdaten gefunden werden, und können möglicherweise eine Aktion wie das Starten einer Logik-App oder das Aufrufen eines Webhooks initiieren. Warnungsregeln definieren die Logik, mit der bestimmt wird, wann eine Warnung erstellt werden soll. Azure Monitor sammelt die von Warnungsregeln verwendeten Daten, aber Sie müssen Regeln erstellen, um Warnungsbedingungen in Ihrem Azure-Abonnement zu definieren.

In den folgenden Abschnitten werden die Typen von Warnungsregeln und Empfehlungen beschrieben, und wann Sie sie verwenden sollten. Diese Empfehlung basiert auf der Funktionalität und den Kosten des Warnungsregeltyps. Details zu den Preisen von Warnungen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Warnungsregeln für Aktivitätsprotokolle
[Warnungsregeln für Aktivitätsprotokolle](../platform/alerts-activity-log.md) werden ausgelöst, wenn im Aktivitätsprotokoll ein Eintrag erstellt wird, der bestimmten Kriterien entspricht. Sie verursachen keine Kosten, daher sollten sie Ihre erste Wahl sein, wenn die erforderliche Logik im Aktivitätsprotokoll enthalten ist. 

Die Zielressource für Aktivitätsprotokollwarnungen können eine bestimmte VM, alle virtuellen Computer in einer Ressourcengruppe oder alle virtuellen Computer in einem Abonnement sein.

Erstellen Sie beispielsweise eine Warnung, wenn ein kritischer virtueller Computer angehalten wird, indem Sie *Virtuellen Computer ausschalten* als Signalnamen auswählen.

![Aktivitätsprotokollwarnung](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Metrikwarnungsregeln
[Metrikwarnungsregeln](../platform/alerts-metric.md) werden ausgelöst, wenn ein Metrikwert einen Schwellenwert überschreitet. Sie können einen bestimmten Schwellenwert definieren oder zulassen, dass Azure Monitor einen Schwellenwert auf der Grundlage von Verlaufsdaten dynamisch festlegt.  Verwenden Sie wann immer möglich Metrikwarnungen in Verbindung mit Metrikdaten, da diese weniger kosten und reaktionsfähiger als Protokollwarnungsregeln sind. Sie sind auch zustandsbehaftet, d. h. sie lösen sich selbst auf, wenn die Metrik unter den Schwellenwert fällt.

Die Zielressource für Aktivitätsprotokollwarnungen können eine bestimmte VM oder alle virtuellen Computer in einer Ressourcengruppe sein.

Um beispielsweise eine Warnung zu erstellen, wenn der Prozessor eines virtuellen Computers einen bestimmten Wert überschreitet, erstellen Sie eine Metrikwarnungsregel, indem Sie *CPU in Prozent* als Signaltyp verwenden. Legen Sie entweder einen bestimmten Schwellenwert fest, oder lassen Sie durch Azure Monitor einen dynamischen Schwellenwert festlegen. 

![Metrikwarnung](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Protokollwarnungen
[Protokollwarnungsregeln](../platform/alerts-log.md) werden ausgelöst, wenn die Ergebnisse einer geplanten Protokollabfrage bestimmten Kriterien entsprechen. Protokollabfragewarnungen sind die kostspieligsten und am wenigsten reaktionsfähigen Warnungsregeln, haben aber Zugriff auf die vielfältigsten Daten und können komplexe Logik ausführen, die von den anderen Warnungsregeln nicht ausgeführt werden kann. 

Die Zielressource für eine Protokollabfrage ist ein Log Analytics-Arbeitsbereich. Filtern Sie in der Abfrage nach bestimmten Computern.

Um beispielsweise eine Warnung zu erstellen, mit der überprüft wird, ob virtuelle Computer in einer bestimmten Ressourcengruppe offline sind, verwenden Sie die folgende Abfrage, die für jeden Computer, der in den letzten zehn Minuten einen Takt verpasst hat, einen Datensatz zurückgibt. Verwenden Sie einen Schwellenwert von 1, der ausgelöst wird, wenn mindestens ein Computer einen Takt verpasst hat.

```kusto
Heartbeat
| where TimeGenerated > ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Protokollwarnung für verpassten Takt](media/monitor-vm-azure/log-alert-01.png)

Um eine Warnung zu erstellen, wenn auf allen virtuellen Windows-Computern im Abonnement eine übermäßige Anzahl von fehlerhaften Anmeldungen aufgetreten ist, verwenden Sie die folgende Abfrage, die für jedes fehlerhafte Anmeldeereignis in der letzten Stunde einen Datensatz zurückgibt. Verwenden Sie einen Schwellenwert, der auf die Anzahl der fehlerhaften Anmeldungen festgelegt ist, die Sie zulassen. 

```kusto
Event
| where TimeGenerated > ago(1hr)
| where EventID == 4625
```

![Protokollwarnung für Anmeldefehler](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager bietet eine präzise Überwachung von Workloads auf virtuellen Computern. Einen Vergleich der Überwachungsplattformen und verschiedenen Implementierungsstrategien finden Sie im [Leitfaden zur Cloudüberwachung: Einführung](/azure/cloud-adoption-framework/manage/monitor/).

Wenn Sie über eine vorhandene Operations Manager-Umgebung verfügen, die Sie weiterhin verwenden möchten, können Sie sie in Azure Monitor integrieren, um zusätzliche Funktionen bereitzustellen. Da von Azure Monitor und Operations Manager derselbe Log Analytics-Agent verwendet wird, senden überwachte virtuelle Computer Daten an beide. Sie müssen den Agent weiterhin Azure Monitor für VMs hinzufügen und den Arbeitsbereich so konfigurieren, dass wie oben angegeben zusätzliche Daten gesammelt werden, aber der virtuelle Computer kann weiterhin Ihre vorhandenen Management Packs ohne Änderung in einer Operations Manager-Umgebung ausführen.

Zu den Features von Azure Monitor, die die vorhandenen Operations Manager-Features ergänzen, zählen die folgenden:

- Verwenden Sie Log Analytics, um die Protokoll- und Leistungsdaten interaktiv zu analysieren.
- Verwenden Sie Protokollwarnungen, um Warnungsbedingungen für mehrere virtuelle Computer zu definieren und langfristige Trends zu verwenden, was mithilfe von Warnungen in Operations Manager nicht möglich ist.   

Ausführliche Informationen zum Verbinden Ihrer vorhandenen Operations Manager-Verwaltungsgruppe mit Ihrem Log Analytics-Arbeitsbereich finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](../platform/om-agents.md).


## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Daten in Azure Monitor-Protokollen mithilfe von Protokollabfragen analysieren.](../log-query/get-started-queries.md)
* [Erfahren Sie mehr über Warnungen mithilfe von Metriken und Protokollen in Azure Monitor.](../platform/alerts-overview.md)