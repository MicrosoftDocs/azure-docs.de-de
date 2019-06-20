---
title: Einheitliche Benachrichtigung und Überwachung in Azure Monitor tritt an die Stelle von klassischer Benachrichtigung und Überwachung
description: Übersicht zur Einstellung der klassischen Überwachungsdienste und -funktionen, die bisher im Azure-Portal unter Benachrichtigungen (klassisch) angezeigt wurden. Die klassische Benachrichtigung und Überwachung beinhaltet klassische Metrikwarnungen für Azure-Ressourcen, klassische Metrikwarnungen für Application Insights, klassische Web-Testwarnungen für Application Insights, klassische benutzerdefinierte metrikbasierte Warnungen für Application Insights und klassische Warnungen für Application Insights SmartDetection v1
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 82e6f28e00de725042a3d764a898b3c6486e1110
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66023198"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Einheitliche Benachrichtigung und Überwachung in Azure Monitor tritt an die Stelle von klassischer Benachrichtigung und Überwachung

Azure Monitor hat sich zu einem vereinheitlichen, voll ausgestatteten Überwachungsdienst entwickelt, der nun ressourcenübergreifend einzelne Metriken und Benachrichtigungen unterstützt. Weitere Informationen finden Sie in unserem [Blogbeitrag zu Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Die neuen Überwachungs- und Benachrichtigungsplattformen von Azure sind schneller, intelligenter und erweiterbar. Dabei halten sie mit der zunehmenden Verbreitung von Cloud Computing Schritt und folgen der Philosophie der Microsoft Intelligent Cloud. 

Da die neue Azure-Plattform zur Überwachung und Benachrichtigung nun verfügbar ist, stellen wir die „klassische“ Überwachungs- und Benachrichtigungsplattform – die im Abschnitt *Klassische Warnungen anzeigen* der Azure-Benachrichtigungen gehostet wird – **zum August 2019 in öffentlichen Azure-Clouds ein**. Die [Azure Government-Cloud](../../azure-government/documentation-government-welcome.md) ist davon nicht betroffen.

> [!NOTE]
> Aufgrund einer Verzögerung beim Rollout des Migrationstools wurde der Deaktivierungstermin vom ursprünglich angekündigten Datum, dem 30. Juni 2019, auf den [31. August 2019 verschoben](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/).

 ![Klassische Warnung im Azure-Portal](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Wir empfehlen Ihnen, sich mit der neuen Plattform vertraut zu machen und damit zu beginnen, Warnungen auf dieser neu zu erstellen. Für Kunden mit einer großen Anzahl Warnungen [bringen wir phasenweise](alerts-understand-migration.md#rollout-phases) ein [Tool zum freiwilligen Migrieren](alerts-using-migration-tool.md) heraus, um die bestehenden klassischen Warnungen ohne Unterbrechungen oder Zusatzkosten auf das neue Warnungssystem umzustellen.

> [!IMPORTANT]
> Im Aktivitätsprotokoll erstellte klassische Warnungsregeln werden nicht als veraltet eingestuft und nicht migriert. Alle im Aktivitätsprotokoll erstellten klassischen Warnungsregeln können nun über „Azure Monitor – Warnungen“ unverändert aufgerufen und verwendet werden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md). Warnungen zur Dienstintegrität können im neuen Abschnitt „Dienstintegrität“ unverändert aufgerufen und verwendet werden. Weitere Informationen finden Sie unter [Warnungen zu Dienstintegritätsbenachrichtigungen](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Einheitliche Metriken und Benachrichtigungen in Application Insights

Die neuere Metrikplattform von Azure Monitor unterstützt jetzt auch Überwachung aus Application Insights. Diese Umstellung bedeutet, dass Application Insights sich jetzt in Aktionsgruppen einhängt und damit weit mehr Optionen als die bisherigen E-Mail- und Webhook-Aufrufe bietet. Warnungen können jetzt Sprachanrufe, Azure Functions, Logik-Apps, SMS und ITSM-Tools wie ServiceNow und Automation-Runbooks auslösen. Mit der fast in Echtzeit erfolgenden Überwachung und Benachrichtigung können Benutzer von Application Insights dank der neuen Plattform die gleiche Technologie nutzen, die auch übergreifend die Überwachung in anderen Azure-Ressourcen unterstützt und den Unterbau bei der Überwachung von Microsoft-Produkten bildet.

Die neue einheitliche Überwachung und Benachrichtigung für Application Insights wird diese Punkte umfassen:

- **Application Insights-Plattformmetriken**: Beliebte vorkonfigurierte Metriken aus dem Application Insights-Produkt. Weitere Informationen finden Sie in diesem Artikel zur Verwendung von [Plattformmetriken für Application Insights im neuen Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights-Verfügbarkeits- und Web-Test**: Bietet die Möglichkeit, die Reaktionsfähigkeit und Verfügbarkeit Ihrer Web-App oder Ihres Servers zu beurteilen. Weitere Informationen finden Sie in diesem Artikel zum Verwenden von [Availability Tests and Alerts for Application Insights on new Azure Monitor](../../azure-monitor/app/monitor-web-app-availability.md) (Verfügbarkeitstests und Benachrichtigungen für Application Insights im neuen Azure Monitor).
- **Application Insights benutzerdefinierte Metriken**: Sie können eigene Metriken für Überwachung und Benachrichtigung definieren und ausgeben. Weitere Informationen finden Sie in diesem Artikel über [Custom Metric for Application Insights on new Azure Monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) (Benutzerdefinierte Metriken für Application Insights im neuen Azure Monitor).
- **Application Insights Fehleranomalien (Teil der intelligenten Erkennung)** : Benachrichtigt Sie automatisch und nahezu in Echtzeit, wenn es bei Ihrer Web-App zu einer ungewöhnlichen Zunahme von Fehlern bei HTTP-Anforderungen oder Abhängigkeitsaufrufen kommt. Application Insights Fehleranomalien (Teil der intelligenten Erkennung) wird als Bestandteil des neuen Azure Monitors in Kürze verfügbar sein, und wir aktualisieren dieses Dokument mit Links zum nächsten Entwicklungsschritt, sobald er in den kommenden Monaten vertrieben wird.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Einheitliche Metriken und Benachrichtigungen für andere Azure-Ressourcen

Die nächste Generation von Benachrichtigung und mehrdimensionaler Überwachung für Azure-Ressourcen ist seit März 2018 verfügbar. Die neuere Metrikplattform und die Benachrichtigungsfunktionalität ist jetzt schneller und bietet nahezu Echtzeitfähigkeit. Noch wichtiger ist, dass die Benachrichtigungen der neueren Metrikplattform mehr Granularität bieten, da die neue Plattform optional Dimensionen aufweist, die es Ihnen erlauben, mit Datenschnitten und Filtern bestimmte Wertkombinationen, Bedingungen oder Betriebsumstände zu suchen. Wie alle Benachrichtigungen im neuen Azure Monitor lassen sich die neuen Metrikwarnungen jetzt mithilfe von Aktionsgruppen besser erweitern: Benachrichtigungen können jenseits von E-Mail oder Webhook auch SMS, Sprache, Azure Functions, Automation-Runbooks und mehr verwenden. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Metrikwarnungen mit Azure Monitor](../../azure-monitor/platform/alerts-metric.md).
Die neueren Metriken für Azure-Ressourcen sind in diesen Formen verfügbar:

- **Azure Monitor-Standard-Plattformmetriken**: beliebte, vorkonfigurierte Metriken aus verschiedenen Azure-Diensten und Produkten. Weitere Informationen finden Sie in diesen Artikeln über [Unterstützte Metriken in Azure Monitor](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) und [Unterstützte Metrikwarnungen in Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Azure Monitor benutzerdefinierte Metriken**: Metriken aus benutzerdefinierten Quellen, einschließlich des Azure-Diagnose-Agents. Weitere Informationen finden Sie in diesem Artikel über [Benutzerdefinierte Metriken in Azure Monitor](../../azure-monitor/platform/metrics-custom-overview.md). Mithilfe benutzerdefinierter Metriken können Sie auch vom [Microsoft Azure-Diagnose-Agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) und vom [InfluxData Telegraf-Agent](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md) erfasste Metriken veröffentlichen.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Einstellung der klassischen Überwachungs- und Benachrichtigungsplattform

Wie bereits zuvor erwähnt, wird die klassische Überwachungs- und Benachrichtigungsplattform, die aktuell im Bereich [Warnungen (klassisch)](../../azure-monitor/platform/alerts-classic.overview.md) des Azure-Portals zur Verfügung steht, in den kommenden Monaten eingestellt und durch das neuere System ersetzt.
Die ältere klassische Überwachung und Benachrichtigung wird am 31. August 2019 eingestellt. Dies gilt auch für die zugehörigen APIs, die Schnittstelle zum Azure-Portal und die enthaltenen Dienste. Insbesondere werden diese Funktionen als veraltet markiert:

- Alte (klassische) Metriken und Warnungen für Azure-Ressourcen, die aktuell im Abschnitt [Warnungen (klassisch)](../../azure-monitor/platform/alerts-classic.overview.md) im Azure-Portal zur Verfügung stehen, auf die als Ressource unter [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) zugegriffen wird
- Ältere (klassische) Plattform- und benutzerdefinierte Metriken für Application Insights sowie Benachrichtigungen für sie, die aktuell im Bereich [Warnungen (klassisch)](../../azure-monitor/platform/alerts-classic.overview.md) im Azure-Portal verfügbar sind, auf die als Ressource unter [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) zugegriffen wird
- Ältere (klassische) Fehleranomaliebenachrichtigungen, die im Azure-Portal als [Intelligente Erkennung in Application Insights](../../azure-monitor/app/proactive-diagnostics.md) verfügbar sind; die dafür konfigurierten Warnungen werden im Bereich [Warnungen (klassisch)](../../azure-monitor/platform/alerts-classic.overview.md) im Azure-Portal angezeigt

Alle klassischen Überwachungs- und Warnungssysteme, einschließlich der entsprechenden [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md), [CLI](../../azure-monitor/platform/alerts-classic-portal.md), [Azure-Portalseite](../../azure-monitor/platform/alerts-classic-portal.md) und der [Ressourcenvorlage](../../azure-monitor/platform/alerts-enable-template.md), können bis Ende Juni 2019 weiterhin verwendet werden. 

Ab Ende August 2019 gilt Folgendes für Azure Monitor:

- Klassische Überwachungs- und Warnungsdienste werden außer Betrieb genommen und sind für die Erstellung neuer Warnungsregeln nicht mehr verfügbar.
- Alle Warnungsregeln, die in „Warnungen (klassisch)“ nach Juni 2019 noch vorhanden sind, werden auch weiterhin Benachrichtigungen ausführen und auslösen. Sie können jedoch nicht geändert werden.
- Ab September 2019 werden Warnungsregeln des klassischen Überwachungs- und Warnungsdiensts, die migriert werden können, von Microsoft automatisch auf ihr Äquivalent in der neuen Azure-Überwachungsplattform verschoben. Dabei handelt es sich um einen nahtlosen Prozess ohne Ausfallzeiten, bei dem für Kunden keine Überwachungslücken auftreten.
- Warnungsregeln, die auf die neue Warnungsplattform migriert werden, bieten die Überwachungsabdeckung wie bisher, lösen aber die Benachrichtigung mit neuen Nutzlasten aus. Alle E-Mail-Adressen, Webhookendpunkte oder Logik-App-Links, die mit einer klassischen Warnungsregel verbunden sind, werden bei der Migration übernommen, verhalten sich aber möglicherweise nicht richtig, da die Warnungsnutzlast auf der neuen Plattform eine andere ist.
- Einige [klassische Warnungsregeln, die nicht automatisch migriert werden können](alerts-understand-migration.md#which-classic-alert-rules-can-be-migrated) und manuelle Aktionen von Benutzern erfordern, werden weiterhin bis Juni 2020 ausgeführt.

> [!IMPORTANT]
> Microsoft Azure Monitor hat in Phasen ein [Tool zum freiwilligen Migrieren](alerts-using-migration-tool.md) herausgebracht, mit dem Kunden ihre klassischen Warnungsregeln bald zur neuen Plattform migrieren können. Ab September 2019 wird die Ausführung des Tools für alle noch vorhandenen klassischen Warnungsregeln erzwungen, die migriert werden können. Kunden müssen sicherstellen, dass die Automatisierung der Nutzung der Nutzlast klassischer Warnungsregeln für die Verarbeitung der neuen Nutzlast aus [Einheitlichen Metriken und Warnungen in Application Insights](#unified-metrics-and-alerts-in-application-insights) oder [Einheitlichen Metriken und Warnungen für andere Azure-Ressourcen](#unified-metrics-and-alerts-for-other-azure-resources) nach der Migration der klassischen Warnungsregeln angepasst wird. Weitere Informationen finden Sie unter [Vorbereiten Ihrer Logik-Apps und Runbooks für die Migration klassischer Warnungsregeln](alerts-prepare-migration.md).

Wir stellen ein Migrationstool bereit, das Ihnen die freiwillige Migration Ihrer Benachrichtigungen aus dem Bereich [Warnungen (klassisch)](../../azure-monitor/platform/alerts-classic.overview.md) des Azure-Portals zu den neuen Azure-Benachrichtigungen erlaubt. Alle Regeln, die in Warnungen (klassisch) konfiguriert sind und zum neuen Azure Monitor migriert werden, bleiben kostenlos und ohne Berechnung. Ebenso fallen für migrierte klassische Warnungsregeln keine Kosten für das Pushen von Benachrichtigungen per E-Mail, Webhook oder Logik-App an. Die Verwendung der neueren Benachrichtigungs- oder Aktionstypen (wie etwa SMS, Sprachanruf, ITSM-Integration usw.) wird jedoch in Rechnung gestellt, unabhängig davon, ob sie zu migrierten oder zu neuen Benachrichtigungen hinzugefügt werden. Weitere Informationen finden Sie unter [Azure Monitor-Preise](https://azure.microsoft.com/pricing/details/monitor/).

Darüber hinaus werden die Folgenden im Geltungsbereich der [Azure Monitor-Preise](https://azure.microsoft.com/pricing/details/monitor/) kostenpflichtig:

- Alle neuen (nicht migrierten) Warnungsregeln, die über die kostenlosen Einheiten auf der neuen Azure Monitor-Plattform erstellt werden
- Alle über die in Azure Monitor inbegriffenen kostenlosen Einheiten hinaus erfassten und aufbewahrten Daten
- Alle mehrfachen Web-Tests, die von Application Insights ausgeführt werden
- Alle benutzerdefinierten Metriken, die über die inbegriffenen kostenlosen Einheiten hinaus in Azure Monitor gespeichert werden

Dieser Artikel wird fortlaufend mit Links und Details zur neuen Überwachungs- und Benachrichtigungsfunktionalität von Azure sowie zur Verfügbarkeit von Tools aktualisiert, die Benutzer bei der Umstellung auf die neue Azure Monitor-Plattform unterstützen.


## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [neuen einheitlichen Azure Monitor](../../azure-monitor/overview.md).
* Weitere Informationen über die neuen [Azure-Warnungen](../../azure-monitor/platform/alerts-overview.md).
