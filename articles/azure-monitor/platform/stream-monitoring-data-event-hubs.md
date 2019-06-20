---
title: Streamen von Azure-Überwachungsdaten an Event Hubs
description: Erfahren Sie, wie Sie Ihre Azure-Überwachungsdaten an einen Event Hub streamen, um die Daten in einem SIEM- oder Analysetool von Partnern abzurufen.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 72d744808d6b52ccd151645c97005bfdfe1a5541
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243467"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-for-consumption-by-an-external-tool"></a>Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool

In diesem Artikel wird das Einrichten der verschiedenen Schichten an Daten aus der Azure-Umgebung erläutert, die für die Erfassung durch ein externes Tool an einen einzelnen Event Hubs-Namespace oder Event Hub gesendet werden sollen.

> [!VIDEO https://www.youtube.com/embed/SPHxCgbcvSw]

## <a name="what-data-can-i-send-into-an-event-hub"></a>Welche Daten kann ich an einen Event Hub senden?

In Ihrer Azure-Umgebung gibt es mehrere Schichten von Überwachungsdaten, weshalb die Zugriffsmethode für Daten aus verschiedenen Schichten geringfügig variiert. Diese Schichten lassen sich in der Regel wie folgt definieren:

- **Überwachungsdaten zu Anwendungen:** Daten zur Leistung und Funktionalität des von Ihnen geschriebenen Codes, den Sie in Azure ausführen. Zu Anwendungsüberwachungsdaten zählen z.B. Leistungsablaufverfolgungen, Anwendungsprotokolle und benutzerspezifische Telemetriedaten. Anwendungsüberwachungsdaten werden in der Regel auf einer der folgenden Arten gesammelt:
  - Durch Instrumentieren des Codes mit einem SDK wie dem [Application Insights-SDK](../../azure-monitor/app/app-insights-overview.md)
  - Durch Ausführen eines Überwachungs-Agents, der nach neuen Anwendungsprotokollen auf dem Computer lauscht, auf dem Ihre Anwendung ausgeführt wird (z. B. der [Azure-Diagnose-Agent für Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) oder [Azure-Diagnose-Agent für Linux](../../virtual-machines/extensions/diagnostics-linux.md))
- **Überwachungsdaten zum Gast-BS:** Daten zum Betriebssystem, unter dem die Anwendung ausgeführt wird. Zu Überwachungsdaten zum Gast-BS zählen z.B. das Linux-Syslog oder Windows-Systemereignisse. Um diesen Typ von Daten zu sammeln, müssen Sie einen Agent wie den [Azure-Diagnose-Agent für Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) oder [Azure-Diagnose-Agent für Linux](../../virtual-machines/extensions/diagnostics-linux.md) installieren.
- **Überwachungsdaten zur Azure-Ressource:** Daten zum Betrieb einer Azure-Ressource. Bei einigen Arten von Azure-Ressourcen wie virtuellen Computern gibt es ein Gast-BS und Anwendungen, die für die Überwachung innerhalb dieses Azure-Diensts vorgesehen sind. Bei anderen Azure-Ressourcen wie Netzwerksicherheitsgruppen sind Überwachungsdaten zu Ressourcen in der höchsten Datenschicht verfügbar (da weder ein Gast-BS noch eine Anwendung in diesen Ressourcen ausgeführt wird). Diese Daten können mithilfe von [Ressourcendiagnoseeinstellungen](./../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) gesammelt werden.
- **Überwachungsdaten zum Azure-Abonnement:** Daten zum Betrieb und zur Verwaltung eines Azure-Abonnements sowie Daten zur Integrität und zum Betrieb von Azure selbst. Das [Aktivitätsprotokoll](./../../azure-monitor/platform/activity-logs-overview.md) enthält die meisten Überwachungsdaten zum Abonnement, z. B. zu Dienstintegritätsvorfällen und Azure Resource Manager-Überwachungen. Sie können diese Daten mit einem Protokollprofil sammeln.
- **Überwachungsdaten zu Azure-Mandanten:** Daten zum Betrieb von Azure-Diensten auf Mandantenebene, z. B. Azure Active Directory. Azure Active Directory-Überwachungen und -Anmeldungen sind Beispiele für Mandantenüberwachungsdaten. Diese Daten können mithilfe einer Diagnoseeinstellung für Mandanten gesammelt werden.

Daten aus beliebigen Schichten können an einen Event Hub gesendet und in ein Partnertool geladen werden. Einige Datenquellen können dazu konfiguriert werden, Daten direkt an einen Event Hub zu senden, während möglicherweise ein anderer Prozess wie z.B. eine Logik-App benötigt wird, um die erforderlichen Daten abzurufen. In den nächsten Abschnitten wird beschrieben, wie Sie Daten aus den einzelnen Schichten zum Streamen an einen Event Hub konfigurieren können. Bei der Erläuterung der Schritte wird davon ausgegangen, dass bereits zu überwachende Ressourcen in dieser Schicht vorhanden sind.

## <a name="set-up-an-event-hubs-namespace"></a>Einrichten eines Event Hubs-Namespace

Bevor Sie beginnen, müssen Sie [einen Event Hubs-Namespace und einen Event Hub erstellen](../../event-hubs/event-hubs-create.md). Dieser Namespace und Event Hub sind das Ziel für sämtliche Ihrer Überwachungsdaten. Ein Event Hubs-Namespace ist eine logische Gruppierung von Event Hubs mit derselben Zugriffsrichtlinie, er ähnelt damit einem Speicherkonto mit mehreren Blobs. Beachten Sie einige Details über den Event Hubs-Namespace und die Event Hubs, die Sie erstellen:
* Es wird empfohlen, einen Event Hubs-Standardnamespace zu verwenden.
* In der Regel ist nur eine Durchsatzeinheit erforderlich. Wenn Sie aufgrund steigender Protokollnutzung zentral hochskalieren müssen, können Sie zu einem späteren Zeitpunkt stets manuell die Anzahl der Durchsatzeinheiten für den Namespace erhöhen oder die automatische Aufstockung aktivieren.
* Über die Anzahl der Durchsatzeinheiten können Sie den Durchsatz für Ihre Event Hubs erhöhen. Über die Anzahl von Partitionen können Sie den Verbrauch vieler Consumer parallelisieren. Eine einzelne Partition kann bis zu 20 MBit/s oder ungefähr 20.000 Nachrichten pro Sekunde verarbeiten. Je nach Tool, das die Daten nutzt, ist es auch möglich, Daten von mehreren Partitionen zu verarbeiten. Wenn Sie nicht sicher sind, welche Anzahl von Partitionen Sie festlegen sollten, werden zu Beginn vier Partitionen empfohlen.
* Es wird empfohlen, dass Sie die Nachrichtenvermerkdauer für Ihren Event Hub auf 7 Tage festlegen. Wenn Ihr Tool mehr als einen Tag ausfällt, wird dadurch sichergestellt, dass das Tool dort fortfahren kann, wo es aufgehört hatte (für bis zu 7 Tage alte Ereignisse).
* Es wird empfohlen, die Standardconsumergruppe für Ihren Event Hub zu verwenden. Es ist nur dann erforderlich, andere Consumergruppen zu erstellen oder eine separate Consumergruppe zu verwenden, wenn Sie zwei verschiedenen Tools verwenden möchten, um dieselben Daten vom selben Event Hub zu verarbeiten.
* Für das Azure-Aktivitätsprotokoll wählen Sie einen Event Hubs-Namespace aus. Azure Monitor erstellt dann in diesem Namespace den Event Hub „insights-logs-operationallogs“. Für andere Protokolltypen können Sie entweder einen vorhandenen Event Hub verwenden (sodass Sie den gleichen Event Hub „insights-logs-operationallogs“ wiederverwenden können) oder Azure Monitor für jede Protokollkategorie einen Event Hub erstellen lassen.
* In der Regel müssen die Ports 5671 und 5672 auf dem Computer offen sein, der die Daten vom Event Hub verarbeitet.

Lesen Sie auch die Informationen unter [Azure Event Hubs – häufig gestellte Fragen](../../event-hubs/event-hubs-faq.md).

## <a name="azure-tenant-monitoring-data"></a>Überwachungsdaten zu Azure-Mandanten

Überwachungsdaten zu Azure-Mandanten sind derzeit nur für Azure Active Directory verfügbar. Sie können die [Azure Active Directory-Berichterstellungsdaten](../../active-directory/reports-monitoring/overview-reports.md) verwenden, die den Verlauf der Anmeldeaktivität und des Überwachungspfads von Änderungen beinhaltet, die innerhalb eines bestimmten Mandanten vorgenommen wurden.

### <a name="azure-active-directory-data"></a>Azure Active Directory-Daten

Um Daten aus dem Azure Active Directory-Protokoll an einen Event Hubs-Namespace zu senden, richten Sie eine Mandantendiagnoseeinstellung für Ihren AAD-Mandanten ein. [Befolgen Sie diese Anleitung](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), um eine Diagnoseeinstellung für Mandanten einzurichten.

## <a name="azure-subscription-monitoring-data"></a>Überwachungsdaten zum Azure-Abonnement

Überwachungsdaten zum Azure-Abonnement finden Sie im [Azure-Aktivitätsprotokoll](./../../azure-monitor/platform/activity-logs-overview.md). Dieses umfasst die Erstellungs-, Aktualisierungs- und Löschvorgänge im Resource Manager, Änderungen in [Azure Service Health](../../service-health/service-health-overview.md) mit Auswirkungen auf Ihre Abonnementressourcen, [Resource Health](../../service-health/resource-health-overview.md)-Statusübergänge und diverse andere Ereignistypen auf Abonnementebene. [In diesem Artikel werden alle Kategorien von Ereignissen erläutert, die im Azure-Aktivitätsprotokoll angezeigt werden](./../../azure-monitor/platform/activity-log-schema.md).

### <a name="activity-log-data"></a>Aktivitätsprotokolldaten

Um Daten aus dem Azure-Aktivitätsprotokoll an Event Hubs-Namespaces zu senden, richten Sie ein Protokollprofil für Ihr Abonnement ein. [Befolgen Sie die Schritte in diesem Handbuch](./activity-logs-stream-event-hubs.md) zum Einrichten eines Protokollprofils für Ihr Abonnement. Führen Sie diesen Vorgang für jedes zu überwachende Abonnement durch.

> [!TIP]
> Derzeit können Sie mithilfe eines Protokollprofils nur einen Event Hubs-Namespace auswählen, in dem ein Event Hub mit dem Namen „insights-operational-logs“ erstellt wird. Es ist noch nicht möglich, einen eigenen Event Hub-Namen in einem Protokollprofil anzugeben.

## <a name="azure-resource-metrics-and-diagnostics-logs"></a>Azure-Ressourcenmetriken und -Diagnoseprotokolle

Azure-Ressourcen geben zwei Arten von Überwachungsdaten aus:
1. [Ressourcendiagnoseprotokolle](diagnostic-logs-overview.md)
2. [Metriken](data-platform.md)

Beide Arten von Daten werden mithilfe einer Ressourcendiagnoseeinstellung an einen Event Hub gesendet. [Befolgen Sie die Schritte in diesem Handbuch](diagnostic-logs-stream-event-hubs.md) zum Einrichten einer Ressourcendiagnoseeinstellung für eine bestimmte Ressource. Legen Sie für jede Ressource, für die Protokolle gesammelt werden sollen, eine Ressourcendiagnoseeinstellung fest.

> [!TIP]
> Mit Azure Policy können Sie [durch den DeployIfNotExists-Effekt in der Richtlinienregel](../../governance/policy/concepts/definition-structure.md#policy-rule) sicherstellen, dass alle Ressourcen innerhalb eines bestimmten Bereichs stets mit einer Diagnoseeinstellung eingerichtet werden.

## <a name="guest-os-data"></a>Daten zum Gastbetriebssystem

Sie müssen einen Agent zum Senden von Überwachungsdaten zu Gast-BS an einen Event Hub installieren. Unter Windows oder Linux geben Sie die an den Event Hub zu sendenden Daten sowie den Event Hub an, an den die Daten in einer Konfigurationsdatei gesendet werden sollen, und übergeben die Konfigurationsdatei an den Agent, der auf der VM ausgeführt wird.

### <a name="linux-data"></a>Linux-Daten

Mit dem [Azure-Diagnose-Agent für Linux](../../virtual-machines/extensions/diagnostics-linux.md) können Überwachungsdaten von einem Linux-Computer an einen Event Hub gesendet werden. Hierzu wird der Event Hub als Senke in den geschützten JSON-Einstellungen Ihrer LAD-Konfigurationsdatei. [In diesem Artikel finden Sie weitere Informationen zum Hinzufügen der Event Hub-Senke zu Ihrem Azure-Diagnose-Agent für Linux](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings).

> [!NOTE]
> Das Streaming von Überwachungsdaten zu Gast-BS an einen Event Hub kann nicht im Portal eingerichtet werden. Sie müssen die Konfigurationsdatei stattdessen manuell bearbeiten.

### <a name="windows-data"></a>Windows-Daten

Mit dem [Azure-Diagnose-Agent für Windows](./../../azure-monitor/platform/diagnostics-extension-overview.md) können Überwachungsdaten von einem Windows-Computer an einen Event Hub gesendet werden. Hierzu wird der Event Hub im privateConfig-Abschnitt der WAD-Konfigurationsdatei als Senke hinzugefügt. [In diesem Artikel finden Sie weitere Informationen zum Hinzufügen der Event Hub-Senke zu Ihrem Azure-Diagnose-Agent für Windows](./../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

> [!NOTE]
> Das Streaming von Überwachungsdaten zu Gast-BS an einen Event Hub kann nicht im Portal eingerichtet werden. Sie müssen die Konfigurationsdatei stattdessen manuell bearbeiten.

## <a name="application-monitoring-data"></a>Überwachungsdaten zu Anwendungen

Für Überwachungsdaten zu Anwendungen muss Ihr Code mit einem SDK instrumentiert sein. Deshalb gibt es keine allgemeine Lösung, um Überwachungsdaten zu Anwendungen an einen Event Hub in Azure weiterzuleiten. Allerdings ist [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ein Dienst, der zum Sammeln von Azure-Daten auf Anwendungsebene verwendet werden kann. Wenn Sie Application Insights verwenden, können Sie Überwachungsdaten folgendermaßen an einen Event Hub streamen:

1. [Richten Sie den fortlaufenden Export](../../azure-monitor/app/export-telemetry.md) der Application Insights-Daten in ein Speicherkonto ein.

2. Richten Sie eine per Timer ausgelöste Logik-App ein, die [Daten aus Blob Storage abruft](../../connectors/connectors-create-api-azureblobstorage.md#add-action) und [mithilfe von Push als Nachricht an den Event Hub überträgt](../../connectors/connectors-create-api-azure-event-hubs.md#add-action).

## <a name="what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub"></a>Wie kann ich die an meinen Event Hub gesendeten Überwachungsdaten nutzen?

Die Weiterleitung Ihrer Überwachungsdaten an einen Event Hub mit Azure Monitor ermöglicht eine einfache Integration in SIEM- und Überwachungstools von Partnern. Die meisten Tools benötigen die Event Hub-Verbindungszeichenfolge und bestimmte Berechtigungen für Ihr Azure-Abonnement zum Lesen von Daten aus dem Event Hub. Im Folgenden wird eine nicht erschöpfende Liste von Tools mit der Azure Monitor-Integration aufgeführt:

* **IBM QRadar**: Das DSM und Event Hub-Protokoll von Microsoft Azure sind zum Download auf der [Website des IBM-Supports](https://www.ibm.com/support) erhältlich. Weitere Informationen zur Integration in Azure finden Sie [hier](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
* **Splunk**: Abhängig vom Splunk-Setup gibt es zwei Ansätze:
    1. [Das Azure Monitor-Add-On für Splunk](https://splunkbase.splunk.com/app/3534/) steht in der Splunkbase und in einem Open Source-Projekt zur Verfügung. Die Dokumentation finden Sie [hier](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk).
    2. Wenn Sie in Ihrer Splunk-Instanz ein Add-On nicht installieren können (etwa bei Verwendung eines Proxys oder bei Ausführung in Splunk Cloud), können Sie diese Ereignisse an die HTTP-Ereignissammlung von Splunk weiterleiten. Verwenden Sie dazu [diese Funktion, die durch neue Nachrichten im Event Hub ausgelöst wird](https://github.com/Microsoft/AzureFunctionforSplunkVS).
* **SumoLogic**: Anweisungen zum Einrichten von SumoLogic für die Nutzung von Daten aus einem Event Hub sind [hier](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) verfügbar.
* **ArcSight**: Der intelligente Azure Event Hub-Connector von ArcSight wird im Rahmen [dieser ArcSight-Kollektion von intelligenten Connectors](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852) zur Verfügung gestellt.
* **Syslog-Server:** Wenn Sie Azure Monitor-Daten direkt an einen Syslog-Server streamen möchten, können Sie sich [dieses GitHub-Repository](https://github.com/miguelangelopereira/azuremonitor2syslog/) ansehen.

## <a name="next-steps"></a>Nächste Schritte
* [Archivieren Sie das Aktivitätsprotokoll in einem Speicherkonto.](../../azure-monitor/platform/archive-activity-log.md)
* [Lesen Sie die Übersicht über das Azure-Aktivitätsprotokoll.](../../azure-monitor/platform/activity-logs-overview.md)
* [Richten Sie eine Warnung ein, die auf einem Aktivitätsprotokollereignis basiert.](../../azure-monitor/platform/alerts-log-webhook.md)


