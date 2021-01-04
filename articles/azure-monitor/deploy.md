---
title: Bereitstellen von Azure Monitor
description: Beschreibt die erforderlichen Schritte für eine vollständige Implementierung von Azure Monitor, um alle Ressourcen in Ihrem Azure-Abonnement zu überwachen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a0591825bf187648293d5aabc88597b19ab2436e
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491051"
---
# <a name="deploy-azure-monitor"></a>Bereitstellen von Azure Monitor
Die Aktivierung von Azure Monitor für die Überwachung aller Ihrer Azure-Ressourcen umfasst die Konfiguration von Azure Monitor-Komponenten und von Azure-Ressourcen, damit diese Überwachungsdaten generieren, die Azure Monitor dann sammeln kann. In diesem Artikel werden die erforderlichen Schritte für eine vollständige Implementierung von Azure Monitor anhand einer allgemeinen Konfiguration beschrieben, mit der alle Ressourcen in Ihrem Azure-Abonnement überwacht werden können. Die grundlegenden Beschreibungen der einzelnen Schritte enthalten Links zu weiteren Informationen mit detaillierten Konfigurationsanforderungen.

> [!IMPORTANT]
> Die Features von Azure Monitor und deren Konfiguration variieren abhängig von den geschäftlichen Anforderungen und den Kosten der aktivierten Features. Bei jedem der nachfolgenden Schritte wird angegeben, ob mögliche Kosten anfallen. Sie sollten diese Kosten bewerten, bevor Sie mit diesem Schritt fortfahren. Ausführliche Preisinformationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="configuration-goals"></a>Konfigurationsziele
Das Ziel einer vollständigen Implementierung von Azure Monitor besteht darin, alle verfügbaren Daten von allen Ihren Cloudressourcen und -anwendungen zu sammeln und so viele Features in Azure Monitor wie möglich auf der Grundlage dieser Daten zu aktivieren.

Die von Azure Monitor gesammelten Daten werden entweder an [Azure Monitor-Metriken](platform/data-platform-metrics.md) oder an [Azure Monitor-Protokolle](platform/data-platform-logs.md) gesendet. Jede speichert verschiedene Arten von Daten und ermöglicht andere Arten von Analysen und Warnungen. Unter [Vergleich von Azure Monitor-Metriken und -Protokollen](platform/data-platform.md) finden Sie einen Vergleich der beiden, und [Übersicht über Warnungen in Microsoft Azure](platform/alerts-overview.md) enthält Beschreibungen verschiedener Warnungstypen. 

Einige Daten können an Metriken und Protokolle gesendet werden, um sie mit unterschiedlichen Features zu nutzen. In diesen Fällen müssen Sie beide eventuell separat konfigurieren. Beispielsweise werden Metrikdaten automatisch von Azure-Ressourcen an Metriken gesendet, sodass der Metrik-Explorer und Metrikwarnungen unterstützt werden. Sie müssen eine Diagnoseeinstellung für jede Ressource erstellen, um die gleichen Metrikdaten an Protokolle zu senden, sodass Sie Leistungstrends mit anderen Protokolldaten in Log Analytics analysieren können. In den folgenden Abschnitten wird angegeben, wohin Daten gesendet werden, und es werden die einzelnen Schritte zum Senden von Daten an alle möglichen Ziele beschrieben.

Möglicherweise haben Sie weitere Anforderungen, wie z. B. das Überwachen von Ressourcen außerhalb von Azure und das Senden von Daten an andere Ziele als Azure Monitor. Anforderungen wie diese können mit zusätzlichen Konfigurationen der in diesem Artikel beschriebenen Features erreicht werden. Befolgen Sie die Links zur Dokumentation in jedem Schritt, um Informationen zu zusätzlichen Konfigurationsoptionen zu erhalten.

## <a name="collect-data-from-azure-resources"></a>Erfassen von Daten von Azure-Ressourcen

> [!NOTE]
> Eine umfassende Anleitung zur Überwachung virtueller Computer mit Azure Monitor finden Sie unter [Überwachen von Azure-Ressourcen mit Azure Monitor](insights/monitor-azure-resource.md).

Ein Teil der Überwachung von Azure-Ressourcen ist automatisch verfügbar, ohne dass eine Konfiguration erforderlich ist. Für das Sammeln zusätzlicher Überwachungsdaten müssen Sie jedoch weitere Konfigurationsschritte ausführen. In der folgenden Tabelle werden die erforderlichen Konfigurationsschritte zum Erfassen aller verfügbaren Daten von Ihren Azure-Ressourcen erläutert, einschließlich der Daten, die im jeweiligen Schritt an Azure Monitor-Metriken und Azure Monitor-Protokolle gesendet werden. Jeder Schritt wird in den nachstehenden Abschnitten ausführlicher beschrieben.

[ ![Bereitstellen der Überwachung von Azure-Ressourcen](media/deploy/deploy-azure-resources.png) ](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Keine Konfiguration
Die folgenden Features von Azure Monitor werden beim Erstellen eines Azure-Abonnements ohne weitere Konfiguration aktiviert. Mit dieser Überwachung sind keine Kosten verbunden.

[Azure Active Directory-Protokolle](../active-directory/reports-monitoring/overview-reports.md) enthalten den Verlauf der Anmeldeaktivität und das Überwachungsprotokoll der Änderungen, die in Azure Active Directory vorgenommen wurden, auf Mandantenebene. Weitere Informationen zu den Azure Active Directory-Protokollen und zum Anzeigen dieser im Azure-Portal finden Sie unter [Berichte zu Überwachungsaktivitäten im Azure Active Directory-Portal](../active-directory/reports-monitoring/concept-audit-logs.md) und [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](../active-directory/reports-monitoring/concept-sign-ins.md).

Das [Aktivitätsprotokoll](platform/platform-logs-overview.md) bietet Erkenntnisse zu Ereignissen auf Verwaltungsgruppen- und Abonnementebene, die in Azure aufgetreten sind. Es werden automatisch Ereignisse in das Aktivitätsprotokoll geschrieben, wenn Sie eine neue Azure-Ressource erstellen, eine Ressource ändern oder eine wichtige Aktivität durchführen. Sie können Ereignisse im Azure-Portal anzeigen und Aktivitätsprotokollwarnungen erstellen, wenn bestimmte Ereignisse erstellt werden. Ausführliche Informationen zum Aktivitätsprotokoll und dessen Anzeige im Azure-Portal finden Sie unter [Azure-Aktivitätsprotokoll](platform/activity-log.md).

[Plattformmetriken](platform/metrics-supported.md) werden automatisch von Azure-Diensten in den [Azure Monitor-Metriken](platform/data-platform-metrics.md) erfasst. Diese Daten werden häufig auf der Seite **Übersicht** im Azure-Portal für verschiedene Dienste angezeigt. Ausführliche Informationen zum Analysieren von Plattformmetriken im Azure-Portal finden Sie unter [Erste Schritte mit dem Azure-Metrik-Explorer](platform/metrics-getting-started.md). 


### <a name="create-log-analytics-workspace"></a>Erstellen eines Log Analytics-Arbeitsbereichs
Sie benötigen mindestens einen Log Analytics-Arbeitsbereich, um [Azure Monitor-Protokolle](platform/data-platform-logs.md) zu aktivieren. Dies ist für das Erfassen von Daten wie Protokollen von Azure-Ressourcen, das Sammeln von Daten vom Gastbetriebssystem virtueller Azure-Computer und die meisten Azure Monitor-Erkenntnisse erforderlich. Andere Dienste wie Azure Sentinel und Azure Security Center verwenden ebenfalls einen Log Analytics-Arbeitsbereich und können denselben wie Azure Monitor gemeinsam verwenden. Sie können für diese Überwachung mit einem einzelnen Arbeitsbereich beginnen. Sehen Sie sich aber auch den Artikel [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](platform/design-logs-deployment.md) an, in dem Sie erfahren, wann Sie mehrere Arbeitsbereiche verwenden sollten.

Es fallen keine Kosten für das Erstellen eines Log Analytics-Arbeitsbereichs an, allerdings wird möglicherweise eine Gebühr berechnet, wenn Sie das Erfassen von Daten darin konfigurieren. Ausführliche Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md).  

Eine Anleitung zum Erstellen eines ersten Log Analytics-Arbeitsbereichs finden Sie unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](learn/quick-create-workspace.md). Unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](platform/manage-access.md) erfahren Sie, wie Sie den Zugriff konfigurieren. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Erstellen einer Diagnoseeinstellung zum Erfassen von Mandanten- und Abonnementprotokollen
Die [Azure Active Directory-Protokolle für Ihren Mandanten](../active-directory/reports-monitoring/overview-reports.md) und das [Aktivitätsprotokoll](platform/platform-logs-overview.md) für Ihr Abonnement werden automatisch gesammelt. Wenn Sie diese jedoch an einen Log Analytics-Arbeitsbereich senden, können Sie die Ereignisse zusammen mit anderen Protokolldaten mithilfe von Protokollabfragen in Log Analytics analysieren. Dies ermöglicht es Ihnen auch, Protokollabfragewarnungen zu erstellen. Dies ist die einzige Möglichkeit, Warnungen für Azure Active Directory-Protokolle zu erstellen und komplexere Logik als mit Aktivitätsprotokollwarnungen bereitzustellen.

Es fallen keine Kosten für das Senden des Aktivitätsprotokolls an einen Arbeitsbereich an, es gibt jedoch eine Gebühr für Datenerfassung und -aufbewahrung für Azure Active Directory-Protokolle. 

Weitere Informationen zum Erstellen einer Diagnoseeinstellung für Ihren Mandanten und Ihr Abonnement, um Protokolleinträge an Ihren Log Analytics-Arbeitsbereich zu senden, finden Sie unter [Integrieren von Azure AD-Protokollen mit Azure Monitor-Protokollen](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) und [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](platform/diagnostic-settings.md). 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen und Plattformmetriken
Ressourcen in Azure generieren automatisch [Ressourcenprotokolle](platform/platform-logs-overview.md), die Details zu den in der Ressource ausgeführten Vorgängen enthalten. Anders als bei Plattformmetriken müssen Sie das Erfassen von Ressourcenprotokollen jedoch konfigurieren. Erstellen Sie eine Diagnoseeinstellung, um sie an einen Log Analytics-Arbeitsbereich zu senden und mit den anderen Daten zu verknüpfen, die mit Azure Monitor-Protokollen verwendet werden. Die gleiche Diagnoseeinstellung kann auch verwendet werden, um Plattformmetriken für die meisten Ressourcen an denselben Arbeitsbereich zu senden, sodass Sie Metrikdaten mithilfe von Protokollabfragen zusammen mit anderen gesammelten Daten analysieren können.

Für diese Sammlung fallen Kosten an. Informieren Sie sich zunächst unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/), bevor Sie dies für eine größere Anzahl von Ressourcen implementieren. Ausführliche Informationen zur Kostenoptimierung Ihrer Protokollsammlung finden Sie auch unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md).

Weitere Informationen zum Erstellen einer Diagnoseeinstellung für eine Azure-Ressource finden Sie unter [Erstellen einer Diagnoseeinstellung zum Sammeln von Ressourcenprotokollen und -metriken in Azure](platform/diagnostic-settings.md#create-in-azure-portal). Da für jede Azure-Ressource eine Diagnoseeinstellung erstellt werden muss, lesen Sie auch [Bereitstellen von Azure Monitor im großen Stil](deploy-scale.md). Dort erhalten Sie ausführliche Informationen zur Verwendung von [Azure Policy](../governance/policy/overview.md) für die automatische Erstellung von Einstellungen bei jeder Erstellung einer Azure-Ressource.

### <a name="enable-insights-and-solutions"></a>Aktivieren von Erkenntnissen und Lösungen
Erkenntnisse und Lösungen bieten eine spezielle Überwachung für einen bestimmten Dienst oder eine Lösung. Erkenntnisse verwenden neuere Features von Azure Monitor, z. B. Arbeitsmappen. Sie sollten daher eine Erkenntnis nutzen, wenn diese für Ihren Dienst verfügbar ist. Sie sind automatisch in jedem Azure-Abonnement verfügbar, erfordern jedoch möglicherweise für die vollständige Funktionalität eine Konfiguration. In der Regel verwenden sie Plattformmetriken und Ressourcenprotokolle, die Sie zuvor konfiguriert haben, und können zusätzliche Daten sammeln.

Lösungen müssen jedem Abonnement einzeln hinzugefügt werden. Sie nutzen ausschließlich Daten aus Azure Monitor-Protokollen und können zusätzliche Protokolldaten sammeln.

Es fallen keine Kosten für Lösungen oder Erkenntnisse an, Ihnen werden jedoch möglicherweise die gesammelten Daten in Rechnung gestellt.

Unter [Was wird von Azure Monitor überwacht?](monitor-reference.md) finden Sie eine Liste der in Azure Monitor verfügbaren Erkenntnisse und Lösungen. Informationen zur jeweiligen Konfiguration und den Preisen finden Sie in der zugehörigen Dokumentation. 

## <a name="collect-data-from-virtual-machines"></a>Daten von virtuellen Computern sammeln

> [!NOTE]
> Eine umfassende Anleitung zur Überwachung virtueller Computer mit Azure Monitor finden Sie unter [Überwachen von virtuellen Azure-Computern mit Azure Monitor](insights/monitor-vm-azure.md). 

Virtuelle Computer generieren ähnliche Daten wie andere Azure-Ressourcen, aber Sie benötigen einen Agent, um Daten vom Gastbetriebssystem zu sammeln. Einen Vergleich der von Azure Monitor verwendeten Agents finden Sie unter [Übersicht über die Azure Monitor-Agents](platform/agents-overview.md). 

[Azure Monitor für VMs](insights/vminsights-overview.md) nutzt den Log Analytics-Agent und den Abhängigkeits-Agent, um Daten vom Gastbetriebssystem virtueller Computer zu erfassen, daher können Sie diese Agents im Rahmen der Implementierung dieser Erkenntnis bereitstellen. Dadurch wird der Log Analytics-Agent auch für andere Dienste aktiviert, die ihn verwenden, z. B. Azure Security Center.


[ ![Bereitstellen einer Azure-VM](media/deploy/deploy-azure-vm.png) ](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Konfigurieren eines Arbeitsbereichs für Azure Monitor für VMs
Azure Monitor für VMs erfordert einen Log Analytics-Arbeitsbereich, der in der Regel dem entspricht, der zum Sammeln von Daten anderer Azure-Ressourcen erstellt wurde. Bevor Sie virtuelle Computer aktivieren können, müssen Sie dem Arbeitsbereich die erforderliche Lösung für Azure Monitor für VMs hinzufügen.

Ausführliche Informationen zum Konfigurieren des Log Analytics-Arbeitsbereichs für Azure Monitor für VMs finden Sie unter [Konfigurieren eines Log Analytics-Arbeitsbereichs für Azure Monitor für VMs](insights/vminsights-configure-workspace.md).

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Aktivieren von Azure Monitor für VMs auf jedem virtuellen Computer
Nachdem ein Arbeitsbereich konfiguriert wurde, können Sie jeden virtuellen Computer aktivieren, indem Sie den Log Analytics-Agent und den Abhängigkeits-Agent installieren. Es gibt mehrere Methoden für die Installation dieser Agents, einschließlich Azure Policy, das auch die Möglichkeit bietet, jeden virtuellen Computer bei der Erstellung automatisch zu konfigurieren. Die von Azure Monitor für VMs gesammelten Leistungsdaten und Prozessdetails werden in Azure Monitor-Protokollen gespeichert.

Optionen zum Bereitstellen der Agents auf Ihren virtuellen Computern und zur Aktivierung für die Überwachung finden Sie unter [Aktivieren von Azure Monitor für VMs: Übersicht](insights/vminsights-enable-overview.md).

### <a name="configure-workspace-to-collect-events"></a>Konfigurieren des Arbeitsbereichs zum Sammeln von Ereignissen
Azure Monitor für VMs sammelt Leistungsdaten sowie die Details und Abhängigkeiten von Prozessen vom Gastbetriebssystem der einzelnen virtuellen Computer. Der Log Analytics-Agent kann auch Protokolle vom Gast erfassen, einschließlich des Ereignisprotokolls von Windows und von syslog unter Linux. Er ruft die Konfiguration für diese Protokolle aus dem Log Analytics-Arbeitsbereich ab, mit dem er verbunden ist. Der Arbeitsbereich muss nur einmal konfiguriert werden. Jedes Mal, wenn ein Agent eine Verbindung herstellt, werden alle Konfigurationsänderungen heruntergeladen. 

Ausführliche Informationen zum Konfigurieren des Log Analytics-Arbeitsbereichs zum Sammeln zusätzlicher Daten von den virtuellen Computern mit Agent finden Sie unter [Agent-Datenquellen in Azure Monitor](platform/agent-data-sources.md).

> [!NOTE]
> Sie können den Arbeitsbereich auch so konfigurieren, dass Leistungsindikatoren erfasst werden. Diese sind jedoch höchstwahrscheinlich zu den von Azure Monitor für VMs gesammelten Leistungsdaten redundant. Die vom Arbeitsbereich gesammelten Leistungsdaten werden in der Tabelle *Perf* gespeichert, während die von Azure Monitor für VMs gesammelten Leistungsdaten in der Tabelle *InsightsMetrics* gespeichert werden. Konfigurieren Sie die Sammlung von Leistungsdaten nur dann im Arbeitsbereich, wenn Sie Leistungsindikatoren benötigen, die nicht bereits von Azure Monitor für VMs gesammelt werden.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Diagnoseerweiterung und Telegraf-Agent
Azure Monitor für VMs verwendet den Log Analytics-Agent, der Leistungsdaten an einen Log Analytics-Arbeitsbereich sendet, aber nicht an Azure Monitor-Metriken. Wenn Sie diese Daten an Metriken senden, können sie mit Metrik-Explorer analysiert und mit Metrikwarnungen verwendet werden. Dies erfordert die Diagnoseerweiterung unter Windows bzw. den Telegraf-Agent unter Linux.

Ausführliche Informationen zum Installieren und Konfigurieren dieser Agents finden Sie unter [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](platform/diagnostics-extension-windows-install.md) und [Erfassen von benutzerdefinierten Metriken für einen virtuellen Linux-Computer mit dem InfluxData Telegraf-Agent](platform/collect-custom-metrics-linux-telegraf.md).


## <a name="monitor-applications"></a>Überwachen von Anwendungen
Azure Monitor überwacht Ihre benutzerdefinierten Anwendungen mithilfe von [Application Insights](app/app-insights-overview.md), das Sie für jede zu überwachende Anwendung konfigurieren müssen. Der Konfigurationsvorgang variiert abhängig vom Typ der zu überwachenden Anwendung und der Art der gewünschten Überwachung. Die von Application Insights gesammelten Daten werden je nach Feature in Azure Monitor-Metriken, Azure Monitor-Protokollen oder Azure Blob Storage gespeichert. Leistungsdaten werden ohne zusätzliche Konfiguration in Azure Monitor-Metriken und Azure Monitor-Protokollen gespeichert.

### <a name="create-an-application-resource"></a>Erstellen einer Anwendungsressource
Sie müssen in Application Insights für jede Anwendung, die Sie überwachen möchten, eine Ressource erstellen. Die von Application Insights gesammelten Protokolldaten für eine arbeitsbereichsbasierte Anwendung werden in Azure Monitor-Protokollen gespeichert. Protokolldaten für klassische Anwendungen werden getrennt vom Log Analytics-Arbeitsbereich gespeichert, wie es unter [Datenstruktur](platform/data-platform-logs.md#data-structure) beschrieben ist.

 Wenn Sie die Anwendung erstellen, müssen Sie auswählen, ob Sie eine klassische oder eine arbeitsbereichsbasierte Anwendung verwenden möchten. Weitere Informationen zum Erstellen einer klassischen Anwendung finden Sie unter [Erstellen einer Application Insights-Ressource](app/create-new-resource.md). Weitere Informationen zum Erstellen einer arbeitsbereichsbasierten Anwendung finden Sie unter [Arbeitsbereichsbasierte Application Insights-Ressourcen (Vorschau)](app/create-workspace-resource.md).

### <a name="configure-codeless-or-code-based-monitoring"></a>Konfigurieren der Überwachung mit oder ohne Code
Um die Überwachung für eine Anwendung zu aktivieren, müssen Sie entscheiden, ob Sie die codebasierte Überwachung oder die Überwachung ohne Code verwenden möchten. Der Konfigurationsvorgang variiert abhängig von dieser Entscheidung und vom Typ der Anwendung, die Sie überwachen möchten.

Die **Überwachung ohne Code** ist am einfachsten zu implementieren und kann nach der Codeentwicklung konfiguriert werden. Es sind keine Anpassungen an Ihrem Code erforderlich. Weitere Informationen zum Aktivieren der Überwachung in Abhängigkeit von Ihrer Anwendung finden Sie unter den folgenden Ressourcen.

- [Anwendungen, die in Azure-Web-Apps gehostet werden](app/azure-web-apps.md)
- [Java-Anwendungen](app/java-in-process-agent.md)
- [Auf IIS gehostete ASP.NET-Anwendungen auf virtuellem Azure-Computer oder in Azure-VM-Skalierungsgruppe](app/azure-vm-vmss-apps.md)
- [ASP.NET-Anwendungen, die auf IIS auf einem lokalen virtuellen Computer gehostet werden](app/monitor-performance-live-website-now.md)


Die **codebasierte Überwachung** bietet mehr Anpassungsmöglichkeiten und sammelt zusätzliche Telemetriedaten. Sie erfordert jedoch das Hinzufügen einer Abhängigkeit von NuGet-Paketen des Application Insights SDK zu Ihrem Code. Weitere Informationen zum Aktivieren der Überwachung in Abhängigkeit von Ihrer Anwendung finden Sie unter den folgenden Ressourcen.

- [ASP.NET-Anwendungen](app/asp-net.md)
- [ASP.NET Core-Anwendungen](app/asp-net-core.md)
- [.NET-Konsolenanwendungen](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Andere Plattformen](app/platforms.md)

### <a name="configure-availability-testing"></a>Konfigurieren von Verfügbarkeitstests
Verfügbarkeitstests in Application Insights sind wiederkehrende Tests, mit denen die Verfügbarkeit und Reaktionsfähigkeit Ihrer Anwendung in regelmäßigen Abständen von Standorten auf der ganzen Welt überwacht wird. Sie können einen einfachen Ping-Test kostenlos erstellen, oder Sie erstellen eine Sequenz von Webanforderungen, um Benutzertransaktionen zu simulieren, dies verursacht jedoch Kosten. 

Unter [Überwachen der Verfügbarkeit von Websites](app/monitor-web-app-availability.md) finden Sie eine Zusammenfassung der verschiedenen Arten von Tests sowie Details zu deren Erstellung.

### <a name="configure-profiler"></a>Konfigurieren von Profiler
Profiler in Application Insights stellt Leistungsüberwachungen für .NET-Anwendungen bereit. Der Dienst unterstützt Sie beim Identifizieren des langsamsten Codepfads, der beim Verarbeiten einer bestimmten Webanforderung am längsten dauert. Der Vorgang zum Konfigurieren von Profiler variiert je nach Anwendungstyp. 

Einzelheiten zum Konfigurieren von Profiler finden Sie unter [Profilerstellung für Produktionsanwendungen in Azure mit Application Insights](app/profiler-overview.md).

### <a name="configure-snapshot-debugger"></a>Konfigurieren des Momentaufnahmedebuggers
Der Momentaufnahmedebugger in Application Insights überwacht Telemetriedaten von Ausnahmen Ihrer .NET-Anwendung und sammelt Momentaufnahmen zu den am häufigsten ausgelösten Ausnahmen, damit Sie über die erforderlichen Informationen zur Diagnose von Problemen in der Produktion verfügen. Das Konfigurieren des Momentaufnahmedebuggers variiert abhängig vom Anwendungstyp. 

Ausführliche Informationen zum Konfigurieren des Momentaufnahmedebuggers finden Sie unter [Debugmomentaufnahmen von Ausnahmen in .NET-Apps](app/snapshot-debugger.md).


## <a name="visualize-data"></a>Visualisieren von Daten
Erkenntnisse und Lösungen enthalten eigene Arbeitsmappen und Ansichten zum Analysieren ihrer Daten. Zusätzlich können Sie eigene [Visualisierungen](visualizations.md) erstellen, einschließlich Arbeitsmappen für Azure Monitor-Daten und -Dashboards, um Azure Monitor-Daten mit Daten von anderen Diensten in Azure zu kombinieren.


### <a name="create-workbooks"></a>Erstellen von Arbeitsmappen
Mit [Arbeitsmappen](platform/workbooks-overview.md) in Azure Monitor können Sie umfassende visuelle Berichte im Azure-Portal erstellen. Sie können unterschiedliche Datensätze aus Azure Monitor-Metriken und Azure Monitor-Protokollen kombinieren, um einheitliche interaktive Umgebungen zu erstellen. Sie finden den Katalog von Arbeitsmappen auf der Registerkarte **Arbeitsmappen** im Menü von Azure Monitor. 

Ausführliche Informationen zum Erstellen von benutzerdefinierten Arbeitsmappen finden Sie unter [Azure Monitor-Arbeitsmappen](platform/workbooks-overview.md).

### <a name="create-dashboards"></a>Erstellen von Dashboards
[Azure-Dashboards](../azure-portal/azure-portal-dashboards.md) sind die primäre Technologie zum Erstellen von Dashboards für Azure. Sie ermöglichen Ihnen, Azure Monitor-Daten mit Daten anderer Dienste zu kombinieren, um einen zentralen Anzeigebereich für Ihre gesamte Azure-Infrastruktur bereitzustellen. Ausführliche Informationen zum Erstellen eines Dashboards, das Daten aus Azure Monitor-Protokollen enthält, finden Sie unter [Erstellen und Freigeben von Dashboards von Log Analytics-Daten](learn/tutorial-logs-dashboards.md). 

Ausführliche Informationen zum Erstellen eines Dashboards, das Daten aus Application Insights enthält, finden Sie unter [Erstellen benutzerdefinierter KPI-Dashboards mithilfe von Azure Application Insights](learn/tutorial-app-dashboards.md). 

## <a name="alerts"></a>Alerts
Warnungen in Azure Monitor informieren Sie proaktiv über interessante Daten und Muster in Ihren Überwachungsdaten. Bei einigen Erkenntnissen werden Warnungen ohne Konfiguration generiert. In anderen Szenarien müssen Sie [Warnungsregeln](platform/alerts-overview.md) erstellen, die die zu analysierenden Daten und die Kriterien für das Generieren einer Warnung enthalten, sowie Aktionsgruppen, die die beim Generieren einer Warnung auszuführende Aktion definieren. 


### <a name="create-action-groups"></a>Erstellen von Aktionsgruppen
[Aktionsgruppen](platform/action-groups.md) sind eine Sammlung von Benachrichtigungseinstellungen, die von Warnungsregeln verwendet werden, um die beim Auslösen einer Warnung auszuführende Aktion festzulegen. Beispiele für Aktionen sind das Senden einer E-Mail oder eines Texts, das Aufrufen eines Webhooks und das Senden von Daten an ein ITSM-Tool. Jede Warnungsregel erfordert mindestens eine Aktionsgruppe, und eine einzelne Aktionsgruppe kann von mehreren Warnungsregeln verwendet werden.

Ausführliche Informationen zum Erstellen einer Aktionsgruppe und eine Beschreibung der unterschiedlichen Aktionen, die sie enthalten kann, finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md).


### <a name="create-alert-rules"></a>Erstellen von Warnungsregeln
Es gibt mehrere Arten von Warnungsregeln, die durch den Typ der verwendeten Daten definiert werden. Jede verfügt über unterschiedliche Funktionen und verursacht andere Kosten. Als grundlegende Strategie sollten Sie den Warnungsregeltyp mit den niedrigsten Kosten verwenden, der die erforderliche Logik bereitstellt.

- [Aktivitätsprotokollregeln:](platform/activity-log-alerts.md) erstellen eine Warnung als Reaktion auf ein neues Aktivitätsprotokollereignis, das den angegebenen Bedingungen entspricht. Es fallen keine Kosten für diese Warnungen an, sodass sie Ihre erste Wahl sein sollten. Ausführliche Informationen zum Erstellen einer Aktivitätsprotokollwarnung finden Sie unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](platform/alerts-activity-log.md).
- [Metrikwarnungsregeln:](platform/alerts-metric-overview.md) erstellen eine Warnung als Reaktion auf Metrikwerte, die einen Schwellenwert überschreiten. Metrikwarnungen sind zustandsbehaftet. Das bedeutet, dass die Warnung automatisch geschlossen wird, wenn der Wert unter den Schwellenwert fällt, und dass nur dann Benachrichtigungen gesendet werden, wenn sich der Zustand ändert. Es fallen Kosten für Metrikwarnungen an, die jedoch erheblich geringer als für Protokollwarnungen sind. Ausführliche Informationen zum Erstellen von Metrikwarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](platform/alerts-metric.md).
- [Protokollwarnungsregeln:](platform/alerts-unified-log.md) erstellen eine Warnung, wenn die Ergebnisse einer Zeitplanabfrage den angegebenen Kriterien entsprechen. Dabei handelt es sich um die kostenintensivsten Warnungsregeln, die jedoch die komplexesten Kriterien ermöglichen. Ausführliche Informationen zum Erstellen von Protokollabfragewarnungen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](platform/alerts-log.md).
- [Anwendungswarnungen:](app/monitor-web-app-availability.md) ermöglichen Ihnen proaktive Leistungs- und Verfügbarkeitstests Ihrer Webanwendung. Sie können einen einfachen Ping-Test kostenlos ausführen, während für komplexere Tests Kosten anfallen. Unter [Überwachen der Verfügbarkeit von Websites](app/monitor-web-app-availability.md) finden Sie eine Beschreibung der verschiedenen Arten von Tests sowie Details zu deren Erstellung.


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Bedarfsorientiertes Bereitstellen von Azure Monitor mithilfe von Azure Policy](deploy-scale.md).