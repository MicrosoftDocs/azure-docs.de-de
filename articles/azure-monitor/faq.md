---
title: Häufig gestellte Fragen zu Azure Monitor | Microsoft-Dokumentation
description: Hier finden Sie Antworten auf häufig gestellte Fragen zu Azure Monitor.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2020
ms.openlocfilehash: a8d3ded1d11a350ff53ffda71348b2cc707760b8
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008416"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Häufig gestellte Fragen zu Azure Monitor

Dieser Microsoft-Artikel enthält eine Liste häufig gestellter Fragen zu Azure Monitor. Wenn Sie weiteren Fragen haben, können Sie diese im [Diskussionsforum](/answers/questions/topics/single/24223.html) stellen. Wenn eine Frage häufiger gestellt wird, fügen wir sie diesem Artikel hinzu, damit sie schnell und einfach gefunden werden kann.


## <a name="general"></a>Allgemein

### <a name="what-is-azure-monitor"></a>Was ist Azure Monitor?
[Azure Monitor](overview.md) ist ein Dienst in Azure, der Leistungs- und Verfügbarkeitsüberwachung für Anwendungen und Dienste in Azure, anderen Cloudumgebungen oder lokal bereitstellt. Azure Monitor sammelt Daten aus mehreren Quellen auf einer gemeinsamen Datenplattform, wo Sie auf Trends und Anomalien hin analysiert werden können. Umfassende Funktionen in Azure Monitor helfen Ihnen beim schnellen Erkennen und Reagieren auf kritische Situationen, die sich auf Ihre Anwendung auswirken können.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Worin unterscheiden sich Azure Monitor, Log Analytics und Application Insights?
Im September 2018 hat Microsoft Azure Monitor, Log Analytics und Application Insights in einem einzigen Dienst kombiniert, um eine leistungsfähige End-to-End-Überwachung Ihrer Anwendungen und der Komponenten, auf denen sie basieren, bereitzustellen. Die Funktionen in Log Analytics und Application Insights haben sich nicht geändert, doch wurden einige in Azure Monitor umbenannt, um den neuen Bereich besser widerzuspiegeln. Die Protokolldaten-Engine und die Abfragesprache von Log Analytics werden nun als Azure Monitor-Protokolle bezeichnet. Weitere Informationen finden Sie unter [Updates für Azure Monitor-Terminologie](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Was kostet Azure Monitor?
Funktionen von Azure Monitor, die automatisch aktiviert werden, wie z. B. das Sammeln von Metriken und Aktivitätsprotokollen, werden kostenlos bereitgestellt. Mit anderen Funktionen, z. B. Protokollabfragen und Warnungen, sind Kosten verbunden. Ausführliche Preisinformationen finden Sie in der [Preisübersicht für Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-enable-azure-monitor"></a>Wie aktiviere ich Azure Monitor?
Azure Monitor wird in dem Moment aktiviert, in dem Sie ein neues Azure-Abonnement erstellen, und das [Aktivitätsprotokoll](./platform/platform-logs-overview.md) sowie [Metriken](platform/data-platform-metrics.md) der Plattform werden automatisch gesammelt. Erstellen Sie [Diagnoseeinstellungen](platform/diagnostic-settings.md), um ausführlichere Informationen zum Betrieb Ihrer Azure-Ressourcen zu sammeln, und fügen Sie [Überwachungslösungen](insights/solutions.md) und [Erkenntnisse](./monitor-reference.md) hinzu, um zusätzliche Analysen gesammelter Daten für bestimmte Dienste bereitzustellen. 

### <a name="how-do-i-access-azure-monitor"></a>Wie greife ich auf Azure Monitor zu?
Sie greifen auf alle Azure Monitor-Funktionen und -Daten über das Menü **Monitor** im Azure-Portal zu. Über den Abschnitt **Überwachung** des Menüs für verschiedene Azure-Dienste kann auf dieselben Tools mit gefilterten Daten für eine bestimmte Ressource zugegriffen werden. Auf Azure Monitor-Daten kann auch für eine Vielzahl von Szenarien über die CLI, PowerShell und eine REST-API zugegriffen werden.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Gibt es eine lokale Version von Azure Monitor?
Nein. Azure Monitor ist ein skalierbarer Clouddienst, der große Datenmengen verarbeitet und speichert, obwohl Azure Monitor auch lokale Ressourcen und Ressourcen in anderen Clouds überwachen kann.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Kann Azure Monitor lokale Ressourcen überwachen?
Ja. Zusätzlich zum Sammeln der Überwachungsdaten von Azure-Ressourcen kann Azure Monitor auch Daten von virtuellen Computern und Anwendungen in anderen Clouds und lokal sammeln. Weitere Informationen finden Sie unter [Quellen für Überwachungsdaten für Azure Monitor](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Kann Azure Monitor in System Center Operations Manager integriert werden?
Sie können Ihre vorhandene System Center Operations Manager-Verwaltungsgruppe mit Azure Monitor verbinden, um Daten von Agents in Azure Monitor-Protokollen zu erfassen. Dadurch können Sie Protokollabfragen und die Lösung zum Analysieren von Daten verwenden, die von Agents gesammelt werden. Sie können auch vorhandene System Center Operations Manager-Agents so konfigurieren, dass Daten direkt an Azure Monitor gesendet werden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Welche IP-Adressen verwendet Azure Monitor?
Eine Liste der IP-Adressen und Ports, die für Agents und andere externe Ressourcen für den Zugriff auf Azure Monitor erforderlich sind, finden Sie unter [Von Application Insights und Log Analytics verwendete IP-Adressen](app/ip-addresses.md). 

## <a name="monitoring-data"></a>Überwachungsdaten

### <a name="where-does-azure-monitor-get-its-data"></a>Woher erhält Azure Monitor seine Daten?
Azure Monitor sammelt Daten aus einer Vielzahl von Quellen. Dazu gehören Protokolle und Metriken von Azure-Plattform und -Ressourcen, benutzerdefinierte Anwendungen und Agents, die auf virtuellen Computern ausgeführt werden. Andere Dienste, z. B. Azure Security Center und Network Watcher, sammeln Daten in einem Log Analytics-Arbeitsbereich, damit diese mit Azure Monitor-Daten analysiert werden können. Sie können auch benutzerdefinierte Daten mit der REST-API für Protokolle oder Metriken an Azure Monitor senden. Weitere Informationen finden Sie unter [Quellen für Überwachungsdaten für Azure Monitor](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Welche Daten werden von Azure Monitor gesammelt? 
Azure Monitor sammelt Daten aus einer Vielzahl von Quellen in [Protokollen](platform/data-platform-logs.md) oder [Metriken](platform/data-platform-metrics.md). Jeder Datentyp hat seine eigenen relativen Vorteile und unterstützt jeweils einen bestimmte Gruppe von Funktionen in Azure Monitor. Es gibt eine einzelne Metrikdatenbank für jedes Azure-Abonnement, und Sie können mehrere Log Analytics-Arbeitsbereiche erstellen, um Protokolle entsprechend Ihren Anforderungen zu erfassen. Weitere Informationen finden Sie unter [Azure Monitor-Datenplattform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Gibt es eine maximale Datenmenge, die in Azure Monitor gesammelt werden kann?
Es gibt keine Beschränkung hinsichtlich der Menge gesammelter Metrikdaten, doch werden diese Daten maximal 93 Tage lang gespeichert. Weitere Informationen finden Sie unter [Aufbewahrung von Metriken](platform/data-platform-metrics.md#retention-of-metrics). Es gibt keine Beschränkung hinsichtlich der Menge gesammelter Protokolldaten, doch kann diese durch den Tarif beeinflusst werden, den Sie für den Log Analytics-Arbeitsbereich auswählen. Preisdetails finden Sie [hier](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Wie greife ich auf die von Azure Monitor gesammelten Daten zu?
Erkenntnisse und Lösungen bieten eine benutzerdefinierte Oberfläche zum Arbeiten mit Daten, die in Azure Monitor gespeichert wurden. Sie können direkt mit Protokolldaten arbeiten, indem Sie eine in der Kusto-Abfragesprache (KQL) geschriebene Protokollabfrage verwenden. Im Azure-Portal können Sie Abfragen schreiben und ausführen sowie Daten interaktiv mit Log Analytics analysieren. Analysieren Sie Metriken im Azure-Portal mit dem Metrik-Explorer. Weitere Informationen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](log-query/log-query-overview.md) und [Erste Schritte mit dem Azure-Metrik-Explorer](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Lösungen und Erkenntnisse

### <a name="what-is-an-insight-in-azure-monitor"></a>Was ist eine Erkenntnis in Azure Monitor?
Mit Erkenntnissen wird eine angepasste Überwachungsoberfläche für bestimmte Azure-Dienste bereitgestellt. Diese verwenden dieselben Metriken und Protokolle wie andere Funktionen in Azure Monitor, können jedoch zusätzliche Daten sammeln und eine einzigartige Benutzeroberfläche im Azure-Portal bereitstellen. Weitere Informationen finden Sie unter [Erkenntnisse in Azure Monitor](./monitor-reference.md).

Zum Anzeigen von Erkenntnissen im Azure-Portal verwenden Sie den Abschnitt **Erkenntnisse** im Menü **Monitor** oder den Abschnitt **Überwachung** im Menü des Diensts.

### <a name="what-is-a-solution-in-azure-monitor"></a>Was ist eine Lösung in Azure Monitor?
Überwachungslösungen sind Programmpakete zum Überwachen einer bestimmten Anwendung oder eines bestimmten Diensts basierend auf Azure Monitor-Funktionen. Sie sammeln Protokolldaten in Azure Monitor und stellen Protokollabfragen und Ansichten für das Analysieren der Daten auf einer gemeinsamen Benutzeroberfläche im Azure-Portal bereit. Weitere Informationen finden Sie unter [Überwachungslösungen in Azure Monitor](insights/solutions.md).

Zum Anzeigen von Lösungen im Azure-Portal klicken Sie im Menü **Monitor** im Abschnitt **Erkenntnisse** auf **Mehr**. Klicken Sie auf **Hinzufügen**, um dem Arbeitsbereich weitere Lösungen hinzuzufügen.

## <a name="logs"></a>Protokolle

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Was ist der Unterschied zwischen Azure Monitor-Protokollen und Azure Data Explorer?
Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). Azure Monitor-Protokolle basieren auf Azure Data Explorer und verwenden die gleiche Kusto-Abfragesprache (KQL) mit einigen geringfügigen Unterschieden. Weitere Informationen finden Sie unter [Azure Monitor – Unterschiede in der Protokollabfragesprache](/azure/data-explorer/kusto/query/).

### <a name="how-do-i-retrieve-log-data"></a>Wie rufe ich Protokolldaten ab?
Alle Daten werden mithilfe einer in der Kusto-Abfragesprache (KQL) geschriebenen Protokollabfrage aus einem Log Analytics-Arbeitsbereich abgerufen. Sie können eigene Abfragen schreiben oder Lösungen und Erkenntnisse verwenden, die Protokollabfragen für eine bestimmte Anwendung oder einen bestimmten Dienst enthalten. Weitere Informationen finden Sie in der [Übersicht über Protokollabfragen in Azure Monitor](log-query/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>Kann ich Daten aus einem Log Analytics-Arbeitsbereich löschen?
Daten werden gemäß ihrer [Beibehaltungsdauer](platform/manage-cost-storage.md#change-the-data-retention-period) aus einem Arbeitsbereich entfernt. Sie können bestimmte Daten aus Datenschutz- oder Compliancegründen löschen. Weitere Informationen finden Sie unter [Exportieren und Löschen personenbezogener Daten](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).

### <a name="is-log-analytics-storage-immutable"></a>Ist Log Analytics-Speicher unveränderlich?
Daten im Datenbankspeicher können nach der Erfassung nicht mehr geändert werden, aber sie können mithilfe der [*Purge*-API zum Löschen privater Daten](platform/personal-data-mgmt.md#delete) endgültig gelöscht werden. Obwohl Daten nicht verändert werden können, verlangen einige Zertifizierungen, dass Daten unveränderlich aufbewahrt werden und im Speicher nicht geändert oder gelöscht werden können. Die Unveränderlichkeit von Daten kann durch [Datenexport](platform/logs-data-export.md) in ein Speicherkonto erreicht werden, das als [unveränderlicher Speicher](../storage/blobs/storage-blob-immutability-policies-manage.md) konfiguriert ist.

### <a name="what-is-a-log-analytics-workspace"></a>Was ist ein Log Analytics-Arbeitsbereich?
Alle von Azure Monitor erfassten Protokolldaten werden in einem Log Analytics-Arbeitsbereich gespeichert. Ein Arbeitsbereich ist im Wesentlichen ein Container, in dem Protokolldaten aus einer Vielzahl von Quellen gesammelt werden. Möglicherweise verfügen Sie über einen einzigen Log Analytics-Arbeitsbereich für alle Überwachungsdaten oder es sind mehrere Arbeitsbereiche erforderlich. Weitere Informationen finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Kann ein vorhandener Log Analytics-Arbeitsbereich in ein anderes Azure-Abonnement verschoben werden?
Sie können einen Arbeitsbereich zwischen Ressourcengruppen oder Abonnements verschieben, jedoch nicht in eine andere Region. Weitere Informationen finden Sie unter [Verschieben von Log Analytics-Arbeitsbereichen in ein anderes Abonnement oder eine andere Ressourcengruppe](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Warum werden die Schaltflächen „Abfrage-Explorer“ und „Speichern“ in Log Analytics nicht angezeigt?

Die Schaltflächen **Abfrage-Explorer**, **Speichern** und **Neue Warnungsregel** sind nicht verfügbar, wenn der [Abfragebereich](log-query/scope.md) auf eine bestimmte Ressource festgelegt ist. Zum Erstellen von Benachrichtigungen sowie zum Speichern oder Laden von Abfragen muss Log Analytics auf einen Arbeitsbereich festgelegt sein. Wenn Sie Log Analytics im Arbeitsbereichskontext öffnen möchten, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Der zuletzt verwendete Arbeitsbereich ist ausgewählt, Sie können aber jeden anderen Arbeitsbereich auswählen. Weitere Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](log-query/scope.md).

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Warum erhalte ich in der Protokollumgebung nach einem Drilldown für einen virtuellen Computer eine Fehlermeldung mit dem Hinweis, dass ich den Ressourcenanbieter „Microsoft.Insights“ für dieses Abonnement registrieren muss, um diese Abfrage zu ermöglichen, wenn ich Log Analytics von einem virtuellen Computer aus öffne? 
Viele Ressourcenanbieter werden automatisch registriert. Einige Ressourcenanbieter müssen jedoch unter Umständen manuell registriert werden. Der Gültigkeitsbereich der Registrierung ist immer das Abonnement. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Warum erhalte ich eine Zugriffsfehlermeldung, wenn ich Log Analytics von einer VM aus öffne? 
Zum Anzeigen von VM-Protokollen benötigen Sie eine Leseberechtigung für die Arbeitsbereiche, in denen die VM-Protokolle gespeichert sind. In diesen Fällen muss Ihnen Ihr Administrator diese Berechtigungen in Azure erteilen.

## <a name="metrics"></a>Metriken

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Warum werden Metriken des Gastbetriebssystems meines virtuellen Azure-Computers nicht im Metrik-Explorer angezeigt?
[Plattformmetriken](insights/monitor-azure-resource.md#monitoring-data) werden für Azure-Ressourcen automatisch erfasst. Wenn Sie allerdings Metriken des Gastbetriebssystems eines virtuellen Computers erfassen möchten, sind ein paar Konfigurationsschritte erforderlich. Installieren Sie für einen virtuellen Windows-Computer die Diagnoseerweiterung, und konfigurieren Sie die Azure Monitor-Senke, wie unter [Installieren und Konfigurieren der Microsoft Azure-Diagnoseerweiterung (WAD)](platform/diagnostics-extension-windows-install.md) beschrieben. Installieren Sie für Linux den Telegraf-Agent, wie unter [Erfassen von benutzerdefinierten Metriken für einen virtuellen Linux-Computer mit dem InfluxData Telegraf-Agent](platform/collect-custom-metrics-linux-telegraf.md) beschrieben.

## <a name="alerts"></a>Alerts

### <a name="what-is-an-alert-in-azure-monitor"></a>Was ist eine Warnung in Azure Monitor?
Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen es Ihnen, Probleme zu identifizieren und zu beheben, bevor die Benutzer Ihres Systems sie bemerken. Es gibt mehrere Arten von Warnungen:

- Metrik: Ein Metrikwert überschreitet einen Schwellenwert.
- Protokollabfrage: Die Ergebnisse einer Protokollabfrage entsprechen definierten Kriterien.
- Aktivitätsprotokoll: Ein Aktivitätsprotokollereignis entspricht definierten Kriterien.
- Webtest: Die Ergebnisse eines Verfügbarkeitstests entsprechen definierten Kriterien.


Weitere Informationen finden Sie in der [Übersicht über Warnungen in Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Was ist eine Aktionsgruppe?
Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungen und Aktionen, die über eine Warnung ausgelöst werden können. Mehrere Warnungen können eine einzelne Aktionsgruppe verwenden, sodass gemeinsame Sätze von Benachrichtigungen und Aktionen genutzt werden können. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Was ist eine Aktionsregel?
Mithilfe einer Aktionsregel können Sie das Verhalten einer Gruppe von Warnungen ändern, die einem bestimmten Kriterium entsprechen. Dadurch können Sie Anforderungen wie das Deaktivieren von Warnungsaktionen während eines Wartungsfensters erfüllen. Sie können eine Aktionsgruppe auch auf eine Gruppe von Warnungen anwenden, anstatt sie direkt auf die Warnungsregeln anzuwenden. Weitere Informationen finden Sie unter [Aktionsregeln](platform/alerts-action-rules.md).

## <a name="agents"></a>Agents

### <a name="does-azure-monitor-require-an-agent"></a>Erfordert Azure Monitor einen Agent?
Ein Agent ist nur erforderlich, um Daten aus dem Betriebssystem und Workloads auf virtuellen Computern zu erfassen. Die virtuellen Computer können in Azure, einer anderen Cloudumgebung oder lokal vorhanden sein. Weitere Informationen finden Sie in der [Übersicht über die Azure Monitor-Agents](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Worin unterscheiden sich die Azure Monitor-Agents?
Die Azure-Diagnoseerweiterung wird für virtuelle Azure-Computer verwendet und sammelt Daten in Azure Monitor-Metriken, Azure Storage und Azure Event Hubs. Der Log Analytics-Agent wird für virtuelle Computer in Azure, einer anderen Cloudumgebung oder lokal verwendet und sammelt Daten in Azure Monitor-Protokollen. Der Dependency-Agent erfordert den Log Analytics-Agent sowie gesammelte Prozessdetails und Abhängigkeiten. Weitere Informationen finden Sie in der [Übersicht über die Azure Monitor-Agents](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Wird für den Agent-Datenverkehr meine ExpressRoute-Verbindung verwendet?
Für den Datenverkehr an Azure Monitor wird die ExpressRoute-Verbindung für Microsoft-Peering verwendet. Eine Beschreibung der verschiedenen Typen von ExpressRoute-Datenverkehr finden Sie in der [ExpressRoute-Dokumentation](../expressroute/expressroute-faqs.md#supported-services). 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Wie überprüfe ich, ob der Log Analytics-Agent mit Azure Monitor kommunizieren kann?
Wählen Sie auf dem Agent-Computer in der Systemsteuerung die Option **Sicherheit und Einstellungen** und dann „Microsoft Monitoring Agent“ aus. Auf der Registerkarte **Azure Log Analytics (OMS)** wird durch ein grünes Häkchen bestätigt, dass der Agent mit Azure Monitor kommunizieren kann. Ein gelbes Warnsymbol bedeutet, dass der Agent Probleme hat. Ein häufiger Grund ist, dass der **Microsoft Monitoring Agent**-Dienst beendet wurde. Starten Sie den Dienst mit dem Dienststeuerungs-Manager neu.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Wie beende ich die Kommunikation des Log Analytics-Agents mit Azure Monitor?
Bei Agents, die direkt mit Log Analytics verbunden sind, öffnen Sie die Systemsteuerung, und wählen Sie **Sicherheit und Einstellungen** und dann **Microsoft Monitoring Agent** aus. Entfernen Sie auf der Registerkarte **Azure Log Analytics (OMS)** alle aufgelisteten Arbeitsbereiche. Entfernen Sie in System Center Operations Manager den Computer aus der Liste der Log Analytics-verwalteten Computer. Operations Manager aktualisiert die Konfiguration des Agents so, dass er keine Berichte mehr an Log Analytics sendet. 

### <a name="how-much-data-is-sent-per-agent"></a>Wie viele Daten werden pro Agent gesendet?
Die pro Agent gesendete Datenmenge hängt von Folgendem ab:

* Den Lösungen, die Sie aktiviert haben
* Der Anzahl der Protokolle und Leistungsindikatoren, die gesammelt werden
* Der Menge der Daten in den Protokollen

Ausführliche Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](platform/manage-cost-storage.md).

Für Computer, die den WireData-Agent ausführen können, zeigen Sie mithilfe der folgenden Abfrage an, wie viele Daten gesendet werden:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Wie viel Netzwerkbandbreite nutzt der Microsoft-Verwaltungs-Agent (MMA) beim Senden von Daten an Azure Monitor?
Die Bandbreite hängt von der gesendeten Datenmenge ab. Daten werden komprimiert, bevor sie über das Netzwerk gesendet werden.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Wie kann ich benachrichtigt werden, wenn die Datensammlung vom Log Analytics-Agent beendet wird?

Führen Sie die Schritte unter [Erstellen einer Warnungsregel mit dem Azure-Portal](platform/alerts-metric.md) aus, um eine Benachrichtigung zu erhalten, wenn die Datensammlung beendet wird. Verwenden Sie die folgenden Einstellungen für die Warnungsregel:

- **Warnungsbedingung definieren**: Geben Sie Ihren Log Analytics-Arbeitsbereich als Ressourcenziel an.
- **Benachrichtigungskriterien** 
   - **Signalname**: *Benutzerdefinierte Protokollsuche*
   - **Suchabfrage**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Warnungslogik**: **Basierend auf** *Anzahl von Ergebnissen*, **Bedingung** *Größer als*, **Schwellenwert** *0*
   - **Auswertung basierend auf**: **Zeitraum (in Minuten)** *30*, **Häufigkeit (in Minuten)** *10*
- **Warnungsdetails definieren** 
   - **Name**: *Datensammlung beendet*
   - **Schweregrad**: *Warning*

Geben Sie eine vorhandene oder neue [Aktionsgruppe](platform/action-groups.md) an, damit Sie benachrichtigt werden, wenn die Protokollwarnung Kriterien erfüllt, und ein Heartbeat für mehr als 15 Minuten ausbleibt.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Welche Firewallanforderungen bestehen für Azure Monitor-Agents?
Ausführliche Informationen zu den Firewallanforderungen finden Sie unter [Netzwerkfirewallanforderungen](platform/log-analytics-agent.md#network-requirements).


## <a name="visualizations"></a>Visualisierungen

### <a name="why-cant-i-see-view-designer"></a>Warum wird der Ansicht-Designer nicht angezeigt?

Der Ansicht-Designer steht im Log Analytics-Arbeitsbereich nur für Benutzer zur Verfügung, denen mindestens Berechtigungen vom Typ „Mitwirkender“ zugewiesen wurden.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurationsprobleme
*Ich benötige Hilfe beim Einrichten von Folgendem:*

* [.NET-App](app/asp-net-troubleshoot-no-data.md)
* [Überwachen einer bereits ausgeführten App](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-Diagnose](platform/diagnostics-extension-to-application-insights.md)
* [Java-Web-App](app/java-troubleshoot.md)

*Ich erhalte keine Daten von meinem Server:*

* [Festlegen von Firewallausnahmen](app/ip-addresses.md)
* [Einrichten eines ASP.NET-Servers](app/monitor-performance-live-website-now.md)
* [Einrichten eines Java-Servers](app/java-agent.md)

*Wie viele Application Insights-Ressourcen soll ich bereitstellen:*

* [Entwerfen Ihrer Application Insights-Bereitstellung: Eine oder mehrere Application Insights-Ressourcen?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Kann ich Application Insights mit ... verwenden?

* [Web-Apps auf einem IIS-Server in der Azure-VM oder Azure-VM-Skalierungsgruppe](app/azure-vm-vmss-apps.md)
* [Web-Apps auf einem IIS-Server – lokal oder auf einem virtuellen Computer](app/asp-net.md)
* [Java-Web-Apps](app/java-get-started.md)
* [Node.js-Apps](app/nodejs.md)
* [Web-Apps in Azure](app/azure-web-apps.md)
* [Cloud Services in Azure](app/cloudservices.md)
* [In Docker ausgeführte App-Server](./azure-monitor-app-hub.yml)
* [Einzelseiten-Web-Apps](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows-Desktop-App](app/windows-desktop.md)
* [Andere Plattformen](app/platforms.md)

### <a name="is-it-free"></a>Ist es kostenlos?

Ja, zur experimentellen Verwendung. Im Basic-Tarif kann Ihre Anwendung kostenlos ein bestimmtes monatliches Datenkontingent senden. Das kostenlose Kontingent ist groß genug, um Entwicklungsaufgaben auszuführen und eine App für eine geringe Anzahl von Benutzern zu veröffentlichen. Sie können eine Obergrenze festlegen, um sicherzustellen, dass nicht mehr als die angegebene Datenmenge verarbeitet wird.

Größere Mengen von Telemetriedaten werden nach GB in Rechnung gestellt. Wir bieten einige Tipps zum [Begrenzen der Gebühren](app/pricing.md).

Im Enterprise-Plan fallen für jeden Tag, an dem die einzelnen Web-Serverknoten Telemetriedaten senden, Gebühren an. Er ist geeignet, wenn Sie in großem Umfang fortlaufenden Export verwenden möchten.

Preisinformationen finden Sie [hier](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Wie hoch sind die Kosten?

* Öffnen Sie die Seite **Nutzung und geschätzte Kosten** in einer Application Insights-Ressource. Dort finden Sie ein Diagramm der kürzlichen Nutzung. Sie können ggf. eine Obergrenze für das Datenvolumen festlegen.
* Öffnen Sie das Blatt für die [Azure-Abrechnung](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview), um Ihre Rechnungen für alle Ressourcen anzuzeigen.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Welche Änderungen nimmt Application Insights in meinem Projekt vor?
Die Details hängen von der Art des Projekts ab. Für eine Webanwendung:

* Fügen Sie folgende Dateien zu Ihrem Projekt hinzu:
  * ApplicationInsights.config
  * ai.js
* Diese NuGet-Pakete werden installiert:
  * *Application Insights API* - die Haupt-API
  * *Application Insights API for Web Applications* - zum Senden von Telemetrie vom Server
  * *Application Insights API for JavaScript Applications* - zum Senden von Telemetrie vom Client
* Die Pakete umfassen folgende Assemblys:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Fügt Elemente ein in:
  * Web.config
  * packages.config
* (Nur neue Projekte – wenn Sie [Application Insights zu einem vorhandenen Webprojekt hinzufügen][start], müssen Sie diesen Schritt manuell durchführen.) Fügen Sie Codeausschnitte in den Client- und Servercode ein, um diese mit der Application Insights-Ressourcen-ID zu initialisieren. Beispielsweise wird in einer MVC-App Code auf der Masterseite „Views/Shared/\_Layout.cshtml“ eingefügt.

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Wie aktualisiere ich von älteren SDK-Versionen?
Informationen hierzu finden Sie in den [Versionshinweisen](app/release-notes.md) für das SDK, das für Ihren Anwendungstyp geeignet ist.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Wie kann ich ändern, an welche Azure-Ressource mein Projekt Daten sendet?
Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf `ApplicationInsights.config`. Wählen Sie dann **Application Insights aktualisieren** aus. Sie können die Daten an eine vorhandene oder neue Ressource in Azure senden. Der Assistent ändert den Instrumentationsschlüssel in ApplicationInsights.config. Dadurch wird bestimmt, wohin das Server-SDK die Daten sendet. Wenn Sie "Alle aktualisieren" deaktivieren, wird auch der Anzeigeort des Schlüssels auf Ihren Webseiten geändert.

### <a name="do-new-azure-regions-require-the-use-of-connection-strings"></a>Erfordern neue Azure-Regionen die Verwendung von Verbindungszeichenfolgen?

Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die [Verbindungszeichenfolge](./app/sdk-connection-string.md) identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>Kann ich `providers('Microsoft.Insights', 'components').apiVersions[0]` in meinen Azure Resource Manager-Bereitstellungen verwenden?

Es wird davon abgeraten, diese Methode zum Auffüllen der API-Version zu verwenden. Die neueste Version kann Vorschaureleases darstellen, die Breaking Changes enthalten können. Auch bei neueren Releases, die keine Vorschauversion darstellen, sind die API-Versionen nicht immer abwärtskompatibel mit vorhandenen Vorlagen. Außerdem ist die API-Version in einigen Fällen möglicherweise nicht für alle Abonnements verfügbar.

### <a name="what-is-status-monitor"></a>Was ist der Statusmonitor?

Eine Desktop-App, die Sie auf Ihrem IIS-Webserver zum Konfigurieren von Application Insights in Web-Apps verwenden können. Sie sammelt keine Telemetriedaten; Sie können sie beenden, wenn Sie keine App konfigurieren. 

[Weitere Informationen](app/monitor-performance-live-website-now.md#questions)

### <a name="what-telemetry-is-collected-by-application-insights"></a>Welche Telemetriedaten werden von Application Insights gesammelt?

Von Server-Web-Apps:

* HTTP-Anforderungen
* [Abhängigkeiten](app/asp-net-dependencies.md) Aufrufe an: SQL-Datenbanken, HTTP-Aufrufe an externe Dienste, Aufrufe an Azure Cosmos DB, Tabellen, Blob Storage und Warteschlangen. 
* [Ausnahmen](app/asp-net-exceptions.md) und Stapelüberwachungen.
* [Leistungsindikatoren:](app/performance-counters.md) Wenn Sie den [Application Insights-Statusmonitor](app/monitor-performance-live-website-now.md), die [Azure-Überwachung für App Services](app/azure-web-apps.md), die [Azure-Überwachung für VMs oder VM-Skalierungsgruppen](app/azure-vm-vmss-apps.md) oder den [Application Insights-collectd-Writer](app/java-collectd.md) verwenden.
* [Benutzerdefinierte Ereignisse und Metriken](app/api-custom-events-metrics.md), die Sie codieren.
* [Ablaufverfolgungsprotokolle](app/asp-net-trace-logs.md), wenn Sie den entsprechenden Sammler konfigurieren.

Über [Clientwebseiten](app/javascript.md):

* [Anzahl von Seitenaufrufen](app/usage-overview.md)
* [AJAX-Aufrufe](app/asp-net-dependencies.md) – Anforderungen aus einem derzeit ausgeführten Skript.
* Daten zum Laden von Seitenansichten
* Anzahl von Benutzern und Sitzungen
* [IDs authentifizierter Benutzer](app/api-custom-events-metrics.md#authenticated-users)

Aus anderen Quellen, sofern Sie sie konfigurieren:

* [Azure-Diagnose](platform/diagnostics-extension-to-application-insights.md)
* [Import in Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kann ich Telemetriedaten filtern oder ändern?

Ja, auf dem Server können Sie Folgendes schreiben:

* Einen Telemetrieprozessor, um Eigenschaften zu filtern oder ausgewählten Telemetrieelementen hinzuzufügen, bevor sie von der App gesendet werden
* Eine Telemetrieinitialisierer, um allen Telemetrieelementen Eigenschaften hinzuzufügen

Weitere Informationen über [ASP.NET](app/api-filtering-sampling.md) und [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Wie werden Daten zu Ort, Land/Region und andere Geolocation-Daten berechnet?

Mit [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) wird die IP-Adresse (IPv4 oder IPv6) des Webclients gesucht.

* Browsertelemetrie: Die IP-Adresse des Absenders wird erfasst.
* Servertelemetrie: Das Application Insights-Modul erfasst die Client-IP-Adresse. Sie wird nicht gesammelt, wenn `X-Forwarded-For` festgelegt ist.
* Weitere Informationen zur Erfassung von IP-Adressen und Geolocation-Daten in Application Insights finden Sie in diesem [Artikel](./app/ip-collection.md).

Sie können in der Konfiguration festlegen, dass `ClientIpHeaderTelemetryInitializer` die IP-Adresse aus einem anderen Header akzeptiert. In manchen Systemen wird sie z.B. von einem Proxy, durch den Lastenausgleich oder das CDN nach `X-Originating-IP` verschoben. [Weitere Informationen](https://apmtips.com/posts/2016-07-05-client-ip-address/)

Sie können [mit Power BI](app/export-power-bi.md ) die Anforderungstelemetriedaten auf einer Karte anzeigen.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Wie lange werden Daten im Portal aufbewahrt? Ist Sicherheit gewährleistet?
Informationen hierzu finden Sie unter [Datensammlung, -aufbewahrung und -speicherung in Application Insights][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Was geschieht mit den Telemetriedaten von Application Insight, wenn ein Server oder Gerät die Verbindung mit Azure verliert?

Alle unsere SDKs, einschließlich des Web-SDKs, beinhalten „zuverlässigen Transport“ oder „stabilen Transport“. Wenn der Server oder das Gerät die Verbindung mit Azure verliert, werden die Telemetriedaten [lokal im Dateisystem gespeichert](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (Server-SDKs) oder im HTML5-Sitzungsspeicher (Web-SDK). Das SDK versucht regelmäßig, diese Telemetriedaten zu senden, bis unser Erfassungsdienst diese als „veraltet“ erachtet (48 Stunden für Protokolle, 30 Minuten für Metriken). Veraltete Telemetriedaten werden verworfen. In einigen Fällen, z. B. wenn der lokale Speicher voll ist, wird kein Wiederholungsversuch unternommen.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Können beim Senden von Telemetriedaten auch personenbezogene Daten übertragen werden?

Dies ist möglich, wenn der Code solche Daten sendet. Es kann auch vorkommen, wenn Variablen in den Stapelüberwachungen personenbezogene Daten enthalten. Ihr Entwicklungsteam sollte Risikobewertungen durchführen, um sicherzustellen, dass personenbezogene Daten ordnungsgemäß behandelt werden. [Weitere Informationen zu Datenaufbewahrung und Datenschutz](app/data-retention-privacy.md).

**Alle** Oktette der Clientwebadresse werden immer auf 0 festgelegt, nachdem die Attribute für den geografischen Standort nachgeschlagen wurden.

Beim [Application Insights JavaScript SDK](app/javascript.md) werden für die Funktion zur automatischen Vervollständigung standardmäßig keine personenbezogenen Daten verwendet. Es kann aber sein, dass personenbezogene Daten, die in Ihrer Anwendung genutzt werden, vom SDK übernommen werden (z. B. vollständige Namen in `window.title` oder Konto-IDs in XHR-URL-Abfrageparametern). Fügen Sie für die Datenmaskierung benutzerdefinierter Daten einen [Telemetrieinitialisierer](app/api-filtering-sampling.md#javascript-web-applications) hinzu.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Mein Instrumentierungsschlüssel ist in meiner Webseitenquelle sichtbar.

* Dies ist in Überwachungslösungen üblich.
* Er kann nicht zum Diebstahl Ihrer Daten verwendet werden.
* Er kann verwendet werden, um Datenschiefe zu bewirken oder Warnungen auszulösen.
* Wir wissen von keinem Kunden, bei dem solche Probleme aufgetreten sind.

Sie haben folgende Möglichkeiten, um das Problem auszuschließen:

* Verwenden Sie zwei verschiedene Instrumentierungsschlüssel (unterschiedliche Application Insights-Ressourcen) für Client- und Serverdaten. oder
* Schreiben Sie einen Proxy, der auf dem Server ausgeführt wird, und lassen Sie den Webclient Daten über diesen Proxy senden.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Wie zeige ich POST-Daten in der Diagnosesuche an?
POST-Daten werden nicht automatisch protokolliert, Sie können jedoch einen TrackTrace-Aufruf verwenden und die Daten in den Nachrichtenparameter einfügen. Die Größenbegrenzung hierfür ist höher als bei Zeichenfolgeneigenschaften, Sie können jedoch nicht danach filtern.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Sollte ich eine einzelne oder mehrere Application Insights-Ressourcen verwenden?

Verwenden Sie eine einzelne Ressource für alle Komponenten oder Rollen in einem einzelnen Geschäftssystem. Verwenden Sie separate Ressourcen für Entwicklungs-, Test- und Releaseversionen und für unabhängige Anwendungen.

* [Konsultieren Sie die Erörterung hier](app/separate-resources.md).
* [Beispiel – Clouddienst mit Worker- und Webrolle](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Wie kann ich den Instrumentationsschlüssel dynamisch ändern?

* [Die Erläuterung finden Sie hier](app/separate-resources.md).
* [Beispiel – Clouddienst mit Worker- und Webrolle](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Was sind Benutzer- und Sitzungszähler?

* Das JavaScript-SDK legt im Webclient ein Benutzercookie zum Identifizieren wiederkehrender Benutzer und ein Sitzungscookie zum Gruppieren von Aktivitäten fest.
* Wenn kein clientseitiges Skript vorhanden ist, können Sie [Cookies auf dem Server festlegen](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Wenn ein realer Benutzer Ihre Website in verschiedenen Browsern, beim InPrivate- oder Inkognito-Browsing oder auf unterschiedlichen Computern verwendet, wird er mehrmals gezählt.
* Um einen angemeldeten Benutzer auf verschiedenen Computern und in verschiedenen Browsern zu identifizieren, fügen Sie einen Aufruf von [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users) hinzu.

### <a name="how-does-application-insights-generate-device-information-browser-os-language-model"></a>Wie werden von Application Insights Geräteinformationen generiert (Browser, Betriebssystem, Sprache, Modell)?

Der Browser übergibt die Benutzer-Agent-Zeichenfolge im HTTP-Header der Anforderung, und vom Application Insights-Erfassungsdienst wird der [UA-Parser](https://github.com/ua-parser/uap-core) verwendet, um die Felder zu generieren, die in den Datentabellen und Benutzeroberflächen angezeigt werden. Dies hat zur Folge, dass diese Felder von Application Insights-Benutzern nicht geändert werden können.

Es kann gelegentlich vorkommen, dass diese Daten fehlen oder ungenau sind, wenn der Benutzer oder das Unternehmen das Senden des Benutzer-Agents in den Browsereinstellungen deaktiviert. Darüber hinaus kann es auch sein, dass die [regulären Ausdrücke des UA-Parsers](https://github.com/ua-parser/uap-core/blob/master/regexes.yaml) ggf. nicht alle Geräteinformationen enthalten oder für Application Insights die aktuellen Updates nicht durchgeführt wurden.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Habe ich alles in Application Insights aktiviert?
| Diese Daten sollten angezeigt werden | So erhalten Sie die Daten | Deshalb benötigen Sie die Daten |
| --- | --- | --- |
| Verfügbarkeitsdiagramme |[Webtests](app/monitor-web-app-availability.md) |Information, ob Ihre Web-App verfügbar ist |
| Leistung der Server-App: Antwortzeiten usw. ... |[Fügen Sie Ihrem Projekt Application Insights hinzu](app/asp-net.md) oder [installieren Sie den AI-Statusmonitor auf dem Server](app/monitor-performance-live-website-now.md) (oder schreiben Sie Ihren eigenen Code zum [Nachverfolgung von Aufrufen](app/api-custom-events-metrics.md#trackdependency)) |Erkennen von Leistungsproblemen |
| Telemetriedaten zu Abhängigkeiten |[Installieren des AI-Statusmonitors auf dem Server](app/monitor-performance-live-website-now.md) |Diagnostizieren von Problemen mit Datenbanken oder anderen externen Komponenten |
| Abrufen von Stapelüberwachungen aus Ausnahmen |[Einfügen von TrackException-Aufrufen in Ihren Code](app/asp-net-exceptions.md) (einige werden jedoch automatisch gemeldet) |Erkennen und Diagnostizieren von Ausnahmen |
| Durchsuchen von Ablaufprotokollen |[Hinzufügen eines Protokollierungsadapters](app/asp-net-trace-logs.md) |Diagnostizieren von Ausnahmen und Leistungsproblemen |
| Grundlegende Clientnutzung: Seitenansichten, Sitzungen, ... |[JavaScript-Initialisierer in Webseiten](app/javascript.md) |Nutzungsanalyse |
| Benutzerdefinierte Metriken auf Clients |[Nachverfolgen von Aufrufen in Webseiten](app/api-custom-events-metrics.md) |Verbessern der Benutzerfreundlichkeit |
| Benutzerdefinierte Metriken auf Servern |[Nachverfolgen von Aufrufen auf dem Server](app/api-custom-events-metrics.md) |Business Intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Warum sind die Zahlen in den Diagrammen „Suchen“ und „Metriken“ unterschiedlich?

Durch [Sampling](app/sampling.md) wird die Anzahl der Telemetrieelemente (Anforderungen, benutzerdefinierte Ereignisse usw.), die tatsächlich von der App an das Portal gesendet werden, verringert. In „Suchen“ wir die Anzahl der tatsächlich empfangenen Elemente angezeigt. In Metrikdiagrammen, in denen die Anzahl der Ereignisse angezeigt wird, wird die Anzahl der ursprünglich eingetretenen Ereignisse angezeigt. 

Jedes übertragene Element verfügt über eine `itemCount`-Eigenschaft, die angibt, wie viele ursprüngliche Ereignisse das Element darstellt. Sie können die folgende Abfrage in Analytics ausführen, um zu sehen, wie Sampling erfolgt:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```

### <a name="how-do-i-move-an-application-insights-resource-to-a-new-region"></a>Wie kann ich eine Application Insights-Ressource in eine neue Region verschieben?

Das Verschieben vorhandener Application Insights-Ressourcen aus einer Region in eine andere wird **derzeit nicht unterstützt**. Verlaufsdaten, die Sie gesammelt haben, **können nicht zu einer neuen Region migriert werden**. Die einzige Problemumgehung zur Erzielung einer Teillösung ist:

1. Erstellen Sie in der neuen Region eine ganz neue Application Insights-Ressource ([klassisch](app/create-new-resource.md) oder [arbeitsbereichsbasiert](./app/create-workspace-resource.md)).
2. Erstellen Sie alle eindeutigen Anpassungen, die speziell für die ursprüngliche Ressource gelten, auf der neuen Ressource neu.
3. Ändern Sie Ihre Anwendung so, dass der [Instrumentierungsschlüssel](app/create-new-resource.md#copy-the-instrumentation-key) oder die [Verbindungszeichenfolge](app/sdk-connection-string.md) der Ressource in der neuen Region verwendet werden.  
4. Vergewissern Sie sich anhand eines Tests, dass mit Ihrer neuen Application Insights-Ressource alles wie erwartet funktioniert. 
5. An diesem Punkt können Sie entweder die ursprüngliche Ressource löschen, was dazu führt, dass **alle Verlaufsdaten verloren gehen**. Oder Sie behalten die ursprüngliche Ressource für die Dauer bei, die in den Einstellungen zur Datenaufbewahrung angegeben ist, um Verlaufsberichte erstellen zu können.

Eindeutige Anpassungen, die für die Ressource in der neuen Region normalerweise manuell neu erstellt oder aktualisiert werden müssen, umfassen beispielsweise Folgendes:

- Neuerstellen von benutzerdefinierten Dashboards und Arbeitsmappen 
- Neuerstellen oder Aktualisieren des Umfangs von benutzerdefinierten Protokoll-/Metrikwarnungen 
- Neuerstellen von Verfügbarkeitswarnungen
- Neuerstellen aller benutzerdefinierten Einstellungen für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), die Ihre Benutzer für den Zugriff auf die neue Ressource benötigen 
- Replizieren der Einstellungen, bei denen es um die Aktivierung für die Erfassungs-Stichprobenerstellung, Datenaufbewahrung, tägliche Obergrenze und benutzerdefinierten Metriken geht Diese Einstellungen werden über den Bereich **Nutzungs- und geschätzte Kosten** gesteuert.
- Alle Integrationen mit API-Schlüsseln, z. B. [Versionsanmerkungen](./app/annotations.md), [Livemetriken und sicherer Steuerkanal](app/live-stream.md#secure-the-control-channel) usw. Sie müssen neue API-Schlüssel generieren und die zugeordnete Integration aktualisieren. 
- Der fortlaufende Export auf klassischen Ressourcen muss neu konfiguriert werden.
- Diagnoseeinstellungen auf arbeitsbereichsbasierten Ressourcen müssen neu konfiguriert werden.

> [!NOTE]
> Falls durch die Ressource, die Sie in einer neuen Region erstellen, eine klassische Ressource ersetzt wird, empfehlen wir Ihnen, sich über die Vorteile der [Erstellung einer neuen arbeitsbereichsbasierten Ressource](app/create-workspace-resource.md) oder alternativ der [Migration Ihrer vorhandenen Ressource zu einer arbeitsbereichsbasierten Ressource](app/convert-classic-resource.md) zu informieren. 

### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Konfigurieren von Application Insights

Sie können mit dem Azure-Ressourcenmonitor [PowerShell-Skripts schreiben](app/powershell.md), um folgende Aufgaben auszuführen:

* Erstellen und Aktualisieren von Application Insights-Ressourcen
* Festlegen des Tarifs
* Abrufen des Instrumentierungsschlüssels
* Hinzufügen einer Metrikwarnung
* Hinzufügen eines Verfügbarkeitstests

Sie können keinen Metrik-Explorer-Bericht oder fortlaufende Exporte einrichten.

#### <a name="querying-the-telemetry"></a>Abfragen der Telemetriedaten

Verwenden Sie die [REST-API](https://dev.applicationinsights.io/) zum Ausführen von [Analytics](./log-query/log-query-overview.md)-Abfragen.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Wie kann ich eine Warnung für ein Ereignis festlegen?

Azure-Warnungen gelten nur für Metriken. Erstellen Sie eine benutzerdefinierte Metrik, die immer einen Schwellenwert überschreitet, wenn das Ereignis eintritt. Legen Sie dann eine Warnung für die Metrik fest. Sie erhalten immer eine Benachrichtigung, wenn die Metrik den Schwellenwert in einer der beiden Richtungen überschreitet. Vor der ersten Überschreitung erhalten Sie keine Benachrichtigung, unabhängig von der Höhe des Anfangswerts. Vor der Benachrichtigung liegt immer eine Wartezeit von einigen Minuten.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Fallen Gebühren für die Datenübertragung zwischen einer Azure-Web-App und Application Insights an?

* Wenn Ihre Azure-Web-App in einem Rechenzentrum gehostet wird, die einen Application Insights-Sammlungsendpunkt enthält, fallen keine Gebühren an. 
* Wenn das Hostrechenzentrum keinen Sammlungsendpunkt enthält, fallen für die Telemetriedaten [Azure-Gebühren für ausgehende Daten](https://azure.microsoft.com/pricing/details/bandwidth/) an.

Dies gilt unabhängig davon, wo Ihre Application Insights-Ressource gehostet wird. Es hängt nur von der Verteilung unserer Endpunkte ab.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Kann ich Telemetriedaten an das Application Insights-Portal senden?

Es wird empfohlen, unsere SDKs und die [SDK-API](app/api-custom-events-metrics.md) zu verwenden. Es gibt Varianten des SDK für verschiedene [Plattformen](app/platforms.md). Diese SDKs behandeln Pufferung, Komprimierung, Drosselung, Wiederholungen usw. Das [Erfassungsschema](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) und das [Endpunktprotokoll](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sind jedoch öffentlich.

### <a name="can-i-monitor-an-intranet-web-server"></a>Kann ich einen Intranetwebserver überwachen?

Ja, aber Sie müssen den Datenverkehr zu unseren Diensten entweder über Firewallausnahmen oder Proxyweiterleitungen zulassen.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Die vollständige Liste der Dienste und IP-Adressen finden Sie [hier](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Firewallausnahme

Ermöglichen Sie Ihrem Webserver das Senden von Telemetriedaten an unsere Endpunkte. 

#### <a name="gateway-redirect"></a>Gateway-Umleitung

Leiten Sie Datenverkehr von Ihrem Server an ein Gateway im Intranet weiter, indem Sie Endpunkte in Ihrer Konfiguration überschreiben. Wenn diese „Endpoint“-Eigenschaften in Ihrer Konfiguration nicht vorhanden sind, verwenden diese Klassen die in der Beispieldatei „ApplicationInsights.config“ unten gezeigten Standardwerte. 

Ihr Gateway muss Datenverkehr an die Basisadresse unseres Endpunkts weiterleiten. Ersetzen Sie in Ihrer Konfiguration die Standardwerte durch `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Beispieldatei „ApplicationInsights.config“ mit Standardendpunkten:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider ist ab v2.6.0 verfügbar.



#### <a name="proxy-passthrough"></a>Proxy-Passthrough

Ein Proxy-Passthrough kann erreicht werden, indem entweder ein Proxy auf Computerebene oder auf Anwendungsebene konfiguriert wird.
Weitere Informationen finden Sie im Dotnet-Artikel zu [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Beispieldatei „Web.config“:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Kann ich auf einem Intranetserver Verfügbarkeitswebtests ausführen?

Unsere [Webtests](app/monitor-web-app-availability.md) werden auf Points of Presence aufgeführt, die über den gesamten Globus verteilt sind. Es gibt zwei Lösungen:

* Tür in der Firewall – Lassen Sie Anforderungen von der [umfangreichen und änderbaren Liste von Webtest-Agents](app/ip-addresses.md) an Ihren Server zu.
* Schreiben Sie eigenen Code, um aus dem Intranet regelmäßig Anforderungen an den Server zu senden. Sie können zu diesem Zweck Visual Studio-Webtests ausführen. Der Tester kann die Ergebnisse mithilfe der TrackAvailability()-API an Application Insights senden.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Wie lange dauert das Sammeln von Telemetriedaten?

Die meisten Application Insights-Daten weisen eine Wartezeit von weniger als 5 Minuten auf. Bei einigen Daten kann der Zeitraum länger sein. Ein typisches Beispiel hierfür sind größere Protokolldateien. Weitere Informationen finden Sie in der [SLA für Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>HTTP 502- und 503-Antworten werden nicht immer von Application Insights erfasst

Die Fehler „502 Ungültiges Gateway“ und „503 Dienst nicht verfügbar“ werden nicht immer von Application Insights erfasst. Wenn nur clientseitiges JavaScript für die Überwachung verwendet wird, entspricht dies dem erwarteten Verhalten, da die Fehlerantwort vor der Seite zurückgegeben wird, die den HTML-Header mit dem gerenderten JavaScript-Codeausschnitt für die Überwachung enthält. 

Wenn die 502- oder 503-Antwort von einem Server mit aktivierter serverseitiger Überwachung gesendet wird, werden die Fehler vom Application Insights SDK erfasst. 

Es gibt jedoch immer noch Fälle, in denen auch bei aktivierter serverseitiger Überwachung auf dem Webserver einer Anwendung ein 502- oder 503-Fehler nicht von Application Insights erfasst wird. Viele moderne Webserver gestatten einem Client keine direkte Kommunikation, sondern wenden stattdessen Lösungen wie Reverseproxys an, um Informationen zwischen dem Client und den Front-End-Webservern zu übergeben. 

In diesem Szenario kann eine 502- oder 503-Antwort aufgrund eines Problems auf Reverseproxyebene an einen Client zurückgegeben und nicht standardmäßig von Application Insights erfasst werden. Um Probleme auf dieser Ebene zu erkennen, müssen Sie möglicherweise Protokolle vom Reverseproxy an Log Analytics weiterleiten und eine benutzerdefinierte Regel erstellen, um nach 502/503-Antworten zu suchen. Weitere Informationen zu häufigen Ursachen von 502- und 503-Fehlern finden Sie im Artikel [Problembehandlung bei HTTP-Fehler „502 Ungültiges Gateway“ und „503 Dienst nicht verfügbar“ in Azure App Service](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Was ist OpenTelemetry?

Ein neuer Open-Source-Standard für Einblicke. Weitere Informationen finden Sie unter [https://opentelemetry.io/](https://opentelemetry.io/).

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Warum investiert Microsoft/Azure Monitor in OpenTelemetry?

Wir sind der Auffassung, dass es unseren Kunden aus drei Gründen besser dient:
   1. Ermöglichen der Unterstützung für weitere Kundenszenarien
   2. Instrumentieren ohne Sorgen über eine Abgrenzung der Hersteller
   3. Steigern der Transparenz und der Kundenbindung

Außerdem entspricht es der Microsoft-Strategie, [Open Source zu fördern](https://opensource.microsoft.com/).

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Welchen zusätzlichen Wert erhalte ich mit OpenTelemetry?

Zusätzlich zu den oben genannten Gründen ist OpenTelemetry in größeren Umgebungen effizienter und bietet Konsistenz bei Entwurf und Konfiguration für alle Sprachen.

### <a name="how-can-i-test-out-opentelemetry"></a>Wie kann ich OpenTelemetry testen?

Registrieren Sie sich unter [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) für den Beitritt zu unserer Azure Monitor Application Insights-Early Adopter-Community.

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Was bedeutet allgemeine Verfügbarkeit im Kontext von OpenTelemetry?

Die Definition der OpenTelemetry-Community für „Allgemein verfügbar“ finden Sie [hier](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302). „Allgemein verfügbar“ bedeutet bei OpenTelemetry jedoch nicht, dass die Features mit den vorhandenen Application Insights-SDKs konform sind. Azure Monitor empfiehlt weiterhin unsere aktuellen Application Insights-SDKs für Kunden, die Features wie [vorab aggregierte Metriken](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), [Livemetriken](app/live-stream.md), [Adaptive Stichprobenerstellung](app/sampling.md#adaptive-sampling), [Profiler](app/profiler-overview.md) und [Debuggen von Momentaufnahmen](app/snapshot-debugger.md) benötigen, bis die Features der OpenTelemetry-SDKs ausgereift sind.

### <a name="can-i-use-preview-builds-in-production-environments"></a>Kann ich Vorschaubuilds in Produktionsumgebungen verwenden?

Dies ist nicht empfehlenswert. Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Worin besteht der Unterschied zwischen dem OpenTelemetry-SDK und automatischer Instrumentierung?

In der OpenTelemetry-Spezifikation wird [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk) definiert. Kurz gesagt, ist „SDK“ ist ein sprachspezifisches Paket, das Telemetriedaten für die verschiedenen Komponenten Ihrer Anwendung sammelt und die Daten über ein Exportprogramm an Azure Monitor sendet.

Das Konzept der automatischen Instrumentierung (manchmal auch mit den Begriffen „Bytecode-Injizierung“, „ohne Code“ oder „Agent-basiert“ bezeichnet) beschreibt die Möglichkeit, Ihre Anwendung zu instrumentieren, ohne den Code zu ändern. Weitere Informationen finden Sie z. B. in der [Infodatei zur automatischen Instrumentierung in OpenTelemetry mit Java](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md).

### <a name="whats-the-opentelemetry-collector"></a>Was ist der OpenTelemetry-Collector?

Der OpenTelemetry-Collector wird in der zugehörigen [GitHub-Infodatei](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector) beschrieben. Derzeit verwendet Microsoft den OpenTelemetry-Collector nicht und verlässt sich auf direkte Exportmodule, die ihre Daten an Azure Monitor Application Insights senden.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Worin besteht der Unterschied zwischen OpenCensus und OpenTelemetry?

[OpenCensus](https://opencensus.io/) ist der Vorgänger von [OpenTelemetry](https://opentelemetry.io/). Mit der Hilfe von Microsoft entstand OpenTelemetry aus einer Zusammenführung von [OpenTracing](https://opentracing.io/) und OpenCensus als einzelner Einblickstandard für die ganze Welt. Das zurzeit [für die Produktion empfohlene Python-SDK](app/opencensus-python.md) von Azure Monitor basiert auf OpenCensus, es sollen jedoch in Zukunft alle SDKs von Azure Monitor auf OpenTelemetry basieren.


## <a name="azure-monitor-for-containers"></a>Azure Monitor für Container

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Wofür steht *Andere Prozesse* unter der Knotenansicht?

**Andere Prozesse** soll Ihnen dabei helfen, die zugrunde liegende Ursache für die hohe Ressourcennutzung auf dem Knoten zu verstehen. Dadurch können Sie zwischen der Nutzung durch Containerprozesse und Nicht-Containerprozesse unterscheiden.

Was sind **Andere Prozesse**? 

Hierbei handelt es sich um Nicht-Containerprozesse, die auf dem Knoten ausgeführt werden.  

Wie werden diese berechnet?

**Andere Prozesse** = *Gesamtauslastung von cAdvisor* - *Auslastung vom Containerprozess*

**Andere Prozesse** umfasst Folgendes:

- Nicht-Containerprozesse in selbstverwaltetem oder verwaltetem Kubernetes 

- Container-Laufzeitprozesse  

- Kubelet  

- Auf dem Knoten ausgeführte Systemprozesse 

- Andere Nicht-Kubernetes-Workloads, die auf Knotenhardware oder der VM ausgeführt werden 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Die Eigenschaftswerte „Image“ und „Name“ werden beim Abfragen der ContainerLog-Tabelle nicht aufgefüllt.

Für die Agent-Version ciprod12042019 und höhere Versionen werden diese beiden Eigenschaften standardmäßig nicht für jede Protokollzeile aufgefüllt, um die Kosten für die erfassten Protokolldaten zu minimieren. Es gibt zwei Optionen zum Abfragen der Tabelle, die diese Eigenschaften mit ihren Werten enthalten:

#### <a name="option-1"></a>Option 1: 

Verknüpfen Sie andere Tabellen, um diese Eigenschaftswerte in die Ergebnisse aufzunehmen.

Ändern Sie Ihre Abfragen, um die Eigenschaften „Image“ und „ImageTag“ aus der Tabelle ```ContainerInventory``` aufzunehmen, indem Sie die ContainerID-Eigenschaft verknüpfen. Sie können die Eigenschaft „Name“ (wie sie zuvor in der Tabelle ```ContainerLog``` angezeigt wurde) aus dem Feld „ContaineName“ der Tabelle „KubepodInventory“ einschließen, indem Sie sie in der ContainerID-Eigenschaft verknüpfen. Dies ist die empfohlene Option.

Das folgende Beispiel zeigt eine ausführliche Abfrage, die veranschaulicht, wie Sie diese Feldwerte mit Verknüpfungen abrufen:

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Option 2:

Aktivieren Sie erneut die Erfassung dieser Eigenschaften für jede Containerprotokollzeile.

Eignet sich die erste Option nicht, weil die Abfrage geändert werden muss, können Sie die Erfassung dieser Felder erneut aktivieren. Aktivieren Sie dazu die Einstellung ```log_collection_settings.enrich_container_logs``` in der Agent-Konfigurationszuordnung, wie unter [Konfigurieren der Datensammlung von Azure Monitor für Container-Agent](insights/container-insights-agent-config.md) beschrieben.

> [!NOTE]
> Die zweite Option wird für große Cluster mit mehr als 50 Knoten nicht empfohlen, da dabei API-Serveraufrufe von jedem Knoten im Cluster für die Ausführung dieser Anreicherung generiert werden. Diese Option erhöht darüber hinaus die Datenmenge für jede erfasste Protokollzeile.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Kann ich in Grafana gesammelte Metriken anzeigen?

Azure Monitor für Container unterstützt das Anzeigen von Metriken, die in Ihrem Log Analytics Arbeitsbereich in Grafana-Dashboards gespeichert sind. Wir haben eine Vorlage bereitgestellt, die Sie aus dem [Dashboard-Repository](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) von Grafana herunterladen können, um Ihnen den Einstieg zu erleichtern und Ihnen zu zeigen, wie Sie zusätzliche Daten aus Ihren überwachten Clustern zum Visualisieren in benutzerdefinierten Grafana-Dashboards abfragen können. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Kann ich meinen AKS-Engine-Cluster mit Azure Monitor für Container überwachen?

Azure Monitor für Container unterstützt die Überwachung von Containerworkloads, die für in Azure gehostete AKS-Engine-Cluster (ehemals ACS-Engine-Cluster) bereitgestellt werden. Ausführlichere Informationen sowie einen Überblick über die Schritte zum Aktivieren der Überwachung für dieses Szenario finden Sie unter [Using Azure Monitor for containers for AKS-engine](https://github.com/microsoft/OMS-docker/tree/aks-engine) (Verwenden von Azure Monitor für Container für AKS-Engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Warum werden in meinem Log Analytics-Arbeitsbereich keine Daten angezeigt?

Wenn in Ihrem Log Analytics-Arbeitsbereich nicht täglich zu einer bestimmten Zeit Daten angezeigt werden, haben Sie vielleicht den Standardgrenzwert von 500MB oder die tägliche Obergrenze erreicht, die angegeben wird, um die Menge der täglich zu sammelnden Daten zu steuern. Wenn der Grenzwert für den Tag erreicht ist, wird die Datensammlung beendet und erst am nächsten Tag fortgesetzt. Um Ihre Datennutzung zu überprüfen und eine Aktualisierung auf einen anderen Tarif basierend auf Ihren erwarteten Nutzungsmustern vorzunehmen, lesen Sie [Protokollieren von Datennutzung und Kosten](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Welche Zustände von Containern sind in der Tabelle ContainerInventory angegeben?

Die Tabelle „ContainerInventory“ enthält Informationen zu beendeten und ausgeführten Containern. Die Tabelle wird von einem Workflow innerhalb des Agent ausgefüllt, der den Docker nach allen Containern abfragt (ausgeführte und beendete) und diese Daten an den Log Analytics-Arbeitsbereich weiterleitet.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Wie behebe ich den Fehler *Abonnementregistrierung fehlt*?

Wenn Sie die Fehlermeldung **Abonnementregistrierung für Microsoft.OperationsManagement fehlt** erhalten, können Sie den Fehler beheben, indem Sie den Ressourcenanbieter **Microsoft.OperationsManagement** in dem Abonnement registrieren, in dem der Arbeitsbereich definiert ist. Die Dokumentation für diesen Vorgang finden Sie [hier](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-kubernetes-rbac-enabled-aks-clusters"></a>Gibt es Unterstützung für Kubernetes RBAC-fähige AKS-Cluster?

Die Containerüberwachungslösung bietet keine Unterstützung für Kubernetes RBAC, doch ist dies bei Azure Monitor für Container der Fall. Die Seite mit den Lösungsdetails zeigt möglicherweise nicht die richtigen Informationen auf den Blättern an, die Daten für diese Cluster anzeigen.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Wie aktiviere ich die Protokollsammlung für Container im Kube-System-Namespace über Helm?

Die Protokollsammlung von Containern im Kube-System-Namespace ist standardmäßig deaktiviert. Die Protokollsammlung kann durch das Festlegen einer Umgebungsvariablen auf dem Omsagent aktiviert werden. Weitere Informationen finden Sie auf der GitHub-Seite [Azure Monitor für Container](https://aka.ms/azuremonitor-containers-helm-chart). 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Wie aktualisiere ich den Omsagent auf die neueste Version?

Informationen zum Upgrade des Agent, finden Sie unter [Agentverwaltung](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Wie aktiviere ich die mehrzeilige Protokollierung?

Derzeit unterstützt Azure Monitor für Container keine mehrzeilige Protokollierung, aber es gibt Workarounds. Sie können alle Dienste so konfigurieren, dass sie im JSON-Format schreiben, und dann schreibt Docker/Moby sie als eine einzelne Zeile.

Beispielsweise können Sie Ihr Protokoll als JSON-Objekt umschließen, wie im folgenden Beispiel für eine Beispiel node.js-Anwendung gezeigt:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Diese Daten sehen in Azure Monitor für Protokolle bei der Abfrage wie im folgenden Beispiel aus:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Eine ausführliche Betrachtung des Problems finden Sie unter folgendem [GitHub-Link](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Wie behebe ich Azure AD-Fehler, wenn ich Liveprotokolle aktiviere? 

Es wird möglicherweise der folgende Fehler angezeigt: **The reply url specified in the request does not match the reply urls configured for the application: '<application ID\>'** . (Die in der Anforderung angegebene Antwort-URL entspricht nicht den für die Anwendung konfigurierten Antwort-URLs: <Anwendungs-ID>.) Die Lösung für dieses Problem finden Sie im Artikel [Anzeigen von Containerdaten in Echtzeit mit Azure Monitor für Container](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Warum kann ich Cluster nach dem Onboarding nicht aktualisieren?

Wenn Sie nach der Aktivierung von Azure Monitor für Container für einen AKS-Cluster den Log Analytics-Arbeitsbereich löschen, an den der Cluster seine Daten sendete, tritt beim Versuch, den Cluster zu aktualisieren, ein Fehler auf. Um dieses Problem zu umgehen, müssen Sie die Überwachung deaktivieren und anschließend mit Verweis auf einen anderen gültigen Arbeitsbereich in Ihrem Abonnement neu aktivieren. Wenn Sie versuchen, das Clusterupgrade erneut auszuführen, sollte es erfolgreich durchgeführt und abgeschlossen werden.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Welche Ports und Domänen muss ich für den Agent öffnen bzw. zulassen?

Die Proxy- und Firewall-Konfigurationsinformationen, die für den Container-Agent mit den Clouds „Azure“, „Azure US Government“ und „Azure China 21Vianet“ erforderlich sind, finden Sie unter den [Netzwerkfirewallanforderungen](insights/container-insights-onboard.md#network-firewall-requirements).


## <a name="azure-monitor-for-vms"></a>Azure Monitor für VMs

### <a name="can-i-onboard-to-an-existing-workspace"></a>Kann ich ein Onboarding zu einem vorhandenen Arbeitsbereich ausführen?
Wenn Ihre virtuellen Computer bereits mit einem Log Analytics-Arbeitsbereich verbunden sind, können Sie diesen Arbeitsbereich nach dem Onboarding von Azure Monitor für VMs weiterhin verwenden, vorausgesetzt, er befindet sich in einer der [unterstützten Regionen](insights/vminsights-configure-workspace.md#supported-regions).


### <a name="can-i-onboard-to-a-new-workspace"></a>Kann ich ein Onboarding zu einem neuen Arbeitsbereich ausführen? 
Wenn Ihre VMs derzeit nicht mit einem vorhandenen Log Analytics-Arbeitsbereich verbunden sind, müssen Sie einen neuen Arbeitsbereich zum Speichern Ihrer Daten erstellen. Die Erstellung eines neuen Standardarbeitsbereichs erfolgt automatisch, wenn Sie eine einzelne Azure-VM im Azure-Portal für Azure Monitor for VMs konfigurieren.

Wenn Sie die skriptbasierte Methode verwenden möchten, finden Sie die entsprechenden Schritte im Artikel [Aktivieren von Azure Monitor für VMs mit Azure PowerShell oder einer Resource Manager-Vorlage](./insights/vminsights-enable-powershell.md). 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wie gehe ich vor, wenn meine VM bereits an einen vorhandenen Arbeitsbereich berichtet?
Wenn Sie bereits Daten von Ihren VMs sammeln, haben Sie sie möglicherweise schon für das Melden von Daten an einen vorhandenen Log Analytics-Arbeitsbereich konfiguriert.  Sofern sich dieser Arbeitsbereich in einer der von uns unterstützten Regionen befindet, können Sie Azure Monitor for VMs für diesen bereits vorhandenen Arbeitsbereich aktivieren.  Wenn sich der von Ihnen bereits verwendete Arbeitsbereich nicht in einer der von uns unterstützten Regionen befindet, können Sie derzeit kein Onboarding von Azure Monitor für VMs ausführen.  Wir arbeiten aktiv daran, weitere Regionen zu unterstützen.


### <a name="why-did-my-vm-fail-to-onboard"></a>Warum ist das Onboarding meiner VM nicht gelungen?
Beim Onboarding einer Azure-VM im Azure-Portal werden die folgenden Schritte ausgeführt:

* Es wird ein Log Analytics-Standardarbeitsbereich erstellt, wenn diese Option ausgewählt war.
* Der Log Analytics-Agent wird auf Azure-VMs mithilfe einer VM-Erweiterung installiert, wenn festgestellt wird, dass er erforderlich ist.  
* Der Dependency-Agent der Azure Monitor for VM-Zuordnung wird auf Azure-VMs mithilfe einer Erweiterung installiert, wenn festgestellt wird, dass er erforderlich ist. 

Während des Onboardingprozesses überprüfen wir den Status jeder der oben genannten Komponenten, um Ihnen im Portal einen Benachrichtigungsstatus zurückzugeben. Die Konfiguration des Arbeitsbereichs und die Agentinstallation nehmen normalerweise 5 bis 10 Minuten in Anspruch. Bis zur Anzeige von Überwachungsdaten im Portal vergehen weitere 5 bis 10 Minuten.  

Wenn Sie das Onboarding eingeleitet haben und Meldungen angezeigt werden, die besagen, dass für die VM Onboarding ausgeführt werden muss, sehen Sie bis zu 30 Minuten bis zum Abschluss dieses Vorgangs durch die VM vor. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>In den Leistungsdiagrammen für meine VM werden einige oder alle Daten nicht angezeigt
Unsere Leistungsdiagramme wurden so aktualisiert, dass sie in der Tabelle *InsightsMetrics* gespeicherten Daten verwenden.  Um Daten in diesen Diagrammen anzuzeigen, müssen Sie ein Upgrade durchführen, um die neue VM Insights-Lösung zu verwenden.  Weitere Informationen finden Sie in unseren [häufig gestellten Fragen zur allgemeinen Verfügbarkeit](insights/vminsights-ga-release-faq.md).

Wenn Sie in der Datenträgertabelle oder in einigen der Leistungsdiagramme keine Leistungsdaten sehen, wurden Ihre Leistungsindikatoren im Arbeitsbereich möglicherweise nicht konfiguriert. Führen Sie das folgende [PowerShell-Skript](./insights/vminsights-enable-powershell.md) aus, um das Problem zu beheben.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Wie unterscheidet sich das Zuordnungsfeature von Azure Monitor for VMs von der Dienstzuordnung?
Das Zuordnungsfeature von Azure Monitor for VMs basiert auf der Dienstzuordnung, weist aber die folgenden Unterschiede auf:

* Auf die Zuordnungsansicht kann über das VM-Blatt und über Azure Monitor for VMs unter Azure Monitor zugegriffen werden.
* Auf die Verbindungen in der Zuordnung kann jetzt geklickt werden. Sie zeigen dann eine Ansicht der Verbindungsmetrikdaten für die ausgewählte Verbindung in der Seitenleiste an.
* Es gibt eine neue API, die zum Erstellen der Zuordnungen verwendet wird und eine bessere Unterstützung für komplexere Zuordnungen bietet.
* Überwachte VMs sind jetzt im Clientgruppenknoten enthalten, und das Ringdiagramm zeigt den Anteil der überwachten und nicht überwachten VMs in der Gruppe an.  Sie können außerdem bei ausgeklappter Gruppe zum Filtern der Liste der Computer verwendet werden.
* Überwachte VMs sind jetzt in den Serverport-Gruppenknoten enthalten, und das Ringdiagramm zeigt den Anteil der überwachten und nicht überwachten Computer in der Gruppe an.  Sie können außerdem bei ausgeklappter Gruppe zum Filtern der Liste der Computer verwendet werden.
* Das Format der Zuordnung wurde aktualisiert, um eine bessere Konsistenz mit der App-Übersicht aus Application Insights zu erreichen.
* Die Seitenleisten wurden aktualisiert und weisen nicht den gesamten Integrationssatz auf, der in der Dienstzuordnung unterstützt wurde – Updateverwaltung, Änderungsnachverfolgung, Sicherheit und Service Desk. 
* Die Option zum Auswählen von Gruppen und Computern für die Zuordnung wurde aktualisiert und unterstützt jetzt Abonnements, Ressourcengruppen Skalierungsgruppen von Azure-VMs und Clouddienste.
* Sie können im Zuordnungsfeature von Azure Monitor for VMs keine neuen Computergruppen der Dienstzuordnung erstellen.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Warum werden in meinen Leistungsdiagrammen gepunktete Linien angezeigt?
Dies kann aus einer Reihe von Gründen passieren.  In Fällen, in denen eine Lücke in der Datensammlung besteht, stellen wir die Linien als gepunktet dar.  Wenn Sie die Erfassungshäufigkeit der Daten für die aktivierten Leistungsindikatoren geändert haben (in der Standardeinstellung werden Daten alle 60 Sekunden gesammelt), werden gepunktete Linien im Diagramm angezeigt, wenn Sie einen kleinen Zeitbereich für das Diagramm gewählt haben und Ihre Erfassungshäufigkeit geringer als die im Diagramm verwendete Bucketgröße ist (beispielsweise, wenn die Erfassungshäufigkeit 10 Minuten beträgt und die einzelnen Buckets im Diagramm 5 Minuten groß sind).  In diesem Fall sollte das Wählen eines größeren Zeitbereichs für die Darstellung bewirken, dass die Diagrammlinien als durchgezogene Linien statt als Punkte angezeigt werden.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Werden in Azure Monitor for VMs Gruppen unterstützt?
Ja. Nachdem Sie den Dependency-Agent installiert haben, sammeln wir Informationen aus den VMs, um Gruppen auf der Grundlage von Abonnement, Ressourcengruppe, VM-Skalierungsgruppe und Clouddiensten anzuzeigen.  Wenn Sie die Dienstzuordnung verwendet und Computergruppen erstellt haben, werden diese ebenfalls angezeigt.  Computergruppen werden außerdem im Gruppenfilter angezeigt, wenn Sie sie für den angezeigten Arbeitsbereich erstellt haben. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Wie kann ich anzeigen, welche Faktoren die Linie für das 95. Quantil in den Leistungsaggregatdiagrammen beeinflussen?
Standardmäßig ist die Liste so sortiert, dass die VMs mit dem höchsten Wert für das 95. Quantil für die ausgewählte Metrik angezeigt werden, mit Ausnahme des Diagramms für den verfügbaren Arbeitsspeicher, das die Computer mit dem niedrigsten Wert für das 5. Quantil anzeigt.  Durch Klicken auf das Diagramm wird die **Top-N-Listenansicht** angezeigt; die entsprechende Metrik ist ausgewählt.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Wie behandelt das Zuordnungsfeature doppelt vorhandene IPs in verschiedenen Vnets und Subnetzen?
Wenn Sie in Subnetzen und Vnets doppelt vorhandene IP-Adressbereiche für VMs oder Azure-VM-Skalierungsgruppen verwenden, kann das dazu führen, dass die Azure Monitor for VMs-Zuordnung falsche Informationen anzeigt. Dies ist ein bekanntes Problem, und wir untersuchen Optionen, dieses Verhalten zu verbessern.

### <a name="does-map-feature-support-ipv6"></a>Unterstützt das Zuordnungsfeature IPv6?
Das Zuordnungsfeature unterstützt aktuell nur IPv4, und wir untersuchen derzeit die Unterstützung für IPv6. Wir unterstützen ferner IPv4 mit Tunnelung in IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wenn ich eine Zuordnung für eine Ressourcengruppe oder eine andere große Gruppe lade, ist die Zuordnung schwierig zu betrachten.
Zwar haben wir Verbesserungen an der Zuordnung bei der Behandlung großer und komplexer Konfigurationen vorgenommen, es ist uns aber bewusst, dass eine Zuordnung viele Knoten und Verbindungen sowie Knoten aufweisen kann, die als Cluster fungieren.  Wir sind weiterhin bestrebt, die Unterstützung einer besseren Skalierbarkeit voranzutreiben.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Warum sieht das Netzwerkdiagramm auf der Registerkarte „Leistung“ anders aus als das Netzwerkdiagramm auf der Azure-VM-Übersichtsseite?

Die Übersichtsseite für eine Azure-VM zeigt Diagramme auf der Grundlage der vom Host gemessenen Aktivität in der Gast-VM an.  Für das Netzwerkdiagramm in der Azure-VM-Übersicht wird nur Netzwerkdatenverkehr angezeigt, der in Rechnung gestellt wird.  Dies schließt Datenverkehr zwischen virtuellen Netzwerken nicht ein.  Die für Azure Monitor für VMs angezeigten Daten und Diagramme basieren auf Daten aus der Gast-VM, und das Netzwerkdiagramm zeigt den gesamten eingehenden und ausgehenden TCP/IP-Verkehr für die VM an, einschließlich des Datenverkehrs zwischen virtuellen Netzwerken.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Wie wird die Antwortzeit für Daten gemessen, die in VMConnection gespeichert und im Verbindungsbereich und in den Arbeitsmappen angezeigt werden?

Die Antwortzeit ist eine Schätzung. Da wir den Code der Anwendung nicht instrumentieren, wissen wir nicht genau, wann eine Anforderung beginnt und wann die Antwort empfangen wird. Stattdessen beobachten wir Daten, die über eine Verbindung gesendet werden und dann Daten, die über diese Verbindung zurückkommen. Unser Agent verfolgt diese Sende- und Empfangsvorgänge und versucht, sie zu koppeln: eine Folge von Sendevorgängen, gefolgt von einer Folge von Empfangsvorgängen, wird als Anforderung/Antwort-Paar interpretiert. Die Zeit zwischen diesen Vorgängen ist die Antwortzeit. Sie umfasst die Netzwerklatenz und die Serververarbeitungszeit.

Diese Schätzung funktioniert gut für Protokolle, die auf Anforderung/Antwort basieren: Eine einzelne Anforderung wird über die Verbindung gesendet, und eine einzelne Antwort wird empfangen. Dies ist der Fall bei HTTP(S) (ohne Pipelining), wird aber bei anderen Protokollen nicht erfüllt.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Gibt es im Free-Tarif von Log Analytics Einschränkungen?
Wenn Sie Azure Monitor über den *Free*-Tarif mit einem Log Analytics-Arbeitsbereich konfiguriert haben, unterstützt das Azure Monitor for VMs-Zuordnungsfeature nur fünf Computer, die mit dem Arbeitsbereich verbunden sind. Wenn fünf VMs mit einem kostenlosen Arbeitsbereich verbunden sind und Sie eine der VMs trennen und später mit einer neuen VM verbinden, wird die neue VM nicht überwacht oder auf der Seite „Zuordnen“ angezeigt.  

Unter dieser Bedingung werden Sie zur Aktion **Jetzt testen** aufgefordert, wenn Sie die VM aufrufen und im linken Bereich auf **Insights** klicken, auch wenn dieser Dienst bereits auf der VM installiert wurde.  Sie werden jedoch nicht zu Optionen aufgefordert, die normalerweise angezeigt werden würden, wenn diese VM nicht in Azure Monitor für VMs integriert wurde. 


## <a name="next-steps"></a>Nächste Schritte
Wenn Ihre Frage hier nicht beantwortet wurde, finden Sie in den folgenden Foren weitere Fragen und Antworten.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Allgemeines Feedback zu Azure Monitor finden Sie im [Feedbackforum](https://feedback.azure.com/forums/34192--general-feedback).
