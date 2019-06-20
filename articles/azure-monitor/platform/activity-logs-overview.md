---
title: Übersicht über das Azure-Aktivitätsprotokoll
description: Es wird beschrieben, was das Azure-Aktivitätsprotokoll ist und wie Sie es nutzen können, um Informationen zu den Ereignissen in Ihrem Azure-Abonnement zu erhalten.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 31ce00e21952a28850f91e6288c94898c676b3e8
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66808164"
---
# <a name="overview-of-azure-activity-log"></a>Übersicht über das Azure-Aktivitätsprotokoll

Das **Azure-Aktivitätsprotokoll** gewährt Einblick in Ereignisse auf Abonnementebene, die in Azure aufgetreten sind. Dies schließt einen Datenbereich von Azure Resource Manager-Betriebsdaten bis hin zu Aktualisierungen für Dienstintegritätsereignisse ein. Das Aktivitätsprotokoll wurde bisher als _Überwachungsprotokoll_ oder _Betriebsprotokoll_ bezeichnet, da die Verwaltungskategorie Ereignisse der Steuerungsebene für Ihre Abonnements enthält. 

Verwenden Sie das Aktivitätsprotokoll zum Ermitteln der Antworten auf die Fragen _Was_, _Wer_ und _Wann_ für alle Schreibvorgänge (PUT, POST, DELETE), die für die Ressourcen Ihres Abonnements durchgeführt wurden. Sie können auch den Status des Vorgangs und andere relevante Eigenschaften verstehen. 

Das Aktivitätsprotokoll umfasst keine Lesevorgänge (GET) oder Vorgänge für Ressourcen, die das klassische Modell/RDFE-Modell verwenden.

## <a name="comparison-to-diagnostic-logs"></a>Vergleich mit Diagnoseprotokollen
Es gibt jeweils ein Aktivitätsprotokoll für jedes Azure-Abonnement. Es enthält Daten zu den externen Vorgängen einer Ressource („Steuerungsebene“). [Diagnoseprotokolle](diagnostic-logs-overview.md) werden von einer Ressource ausgegeben und enthalten Informationen zu den Vorgängen dieser Ressource („Datenebene“). Sie müssen die Diagnoseeinstellungen für jede Ressource aktivieren.

![Vergleich: Aktivitätsprotokolle und Diagnoseprotokolle](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Das Azure-Aktivitätsprotokoll ist in erster Linie für Aktivitäten bestimmt, die in Azure Resource Manager stattfinden. Es verfolgt keine Ressourcen nach, die das klassische Modell/RDFE-Modell verwenden. Einige klassische Ressourcentypen weisen einen Proxyressourcenanbieter in Azure Resource Manager auf (z.B. Microsoft.ClassicCompute). Wenn Sie mithilfe dieser Proxyressourcenanbieter über Azure Resource Manager mit einem klassischen Ressourcentyp interagieren, werden die Vorgänge im Aktivitätsprotokoll aufgeführt. Wenn Sie mit einem klassischen Ressourcentyp außerhalb der Azure Resource Manager-Proxys interagieren, werden Ihre Aktionen nur in das Vorgangsprotokoll aufgenommen. Das Vorgangsprotokoll kann in einem separaten Abschnitt des Portals durchsucht werden.

## <a name="activity-log-retention"></a>Speicherung von Aktivitätsprotokollen
Aktivitätsprotokollereignisse werden 90 Tage lang gespeichert. Zum Speichern dieser Daten über einen längeren Zeitraum müssen Sie sie [in Azure Monitor erfassen](activity-log-collect.md) oder [in Speicher oder Event Hubs exportieren](activity-log-export.md).

## <a name="view-the-activity-log"></a>Anzeigen des Aktivitätsprotokolls
Zeigen Sie das Aktivitätsprotokoll für alle Ressourcen über das Menü **Monitor** im Azure-Portal an. Das Aktivitätsprotokoll für eine bestimmte Ressource können Sie über die Option **Aktivitätsprotokoll** im Menü dieser Ressource anzeigen. Sie können Datensätze von Aktivitätsprotokollen auch mit PowerShell, der CLI oder der REST-API abrufen.  Weitere Informationen finden Sie unter [Anzeigen und Abrufen von Azure-Aktivitätsprotokollereignissen](activity-log-view.md).

![Anzeigen des Aktivitätsprotokolls](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Erfassen des Aktivitätsprotokolls in Azure Monitor
Erfassen Sie das Aktivitätsprotokoll in einem Log Analytics-Arbeitsbereich in Azure Monitor, um es mit anderen Überwachungsdaten zu analysieren und die Daten länger als 90 Tage aufzubewahren. Weitere Informationen finden Sie unter [Erfassen und Analysieren von Azure-Aktivitätsprotokollen im Log Analytics-Arbeitsbereich in Azure Monitor](activity-log-collect.md).

![Abfragen des Aktivitätsprotokolls](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>Exportieren des Aktivitätsprotokolls
Exportieren Sie das Aktivitätsprotokoll zur Archivierung in Azure Storage, oder streamen Sie es an einen Event Hub zur Erfassung durch einen Drittanbieterdienst oder eine benutzerdefinierte Analyselösung. Weitere Informationen finden Sie unter [Exportieren des Azure-Aktivitätsprotokolls](activity-log-export.md). Sie können auch Aktivitätsprotokollereignisse in Power BI analysieren, indem Sie das [**Power BI-Inhaltspaket**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/) verwenden.

## <a name="alert-on-activity-log"></a>Warnung im Aktivitätsprotokoll
Sie können eine Warnung erstellen, die ausgegeben wird, wenn im Aktivitätsprotokoll bestimmte Ereignisse eintreten, indem Sie eine [Aktivitätsprotokollwarnung](activity-log-alerts.md) verwenden. Außerdem können Sie eine Warnung mit einer [Protokollabfrage](alerts-log-query.md) erstellen, wenn Ihr Aktivitätsprotokoll über eine Verbindung mit einem Log Analytics-Arbeitsbereich verfügt. Für Protokollabfragenwarnungen werden aber Kosten berechnet. Für Aktivitätsprotokollwarnungen fallen dagegen keine Kosten an.

## <a name="categories-in-the-activity-log"></a>Kategorien im Aktivitätsprotokoll
Jedes Ereignis im Aktivitätsprotokoll verfügt über eine bestimmte Kategorie. Die Kategorien sind in der folgenden Tabelle beschrieben. Umfassende Informationen zu den Schemas dieser Kategorien finden Sie unter [Ereignisschema des Azure-Aktivitätsprotokolls](activity-log-schema.md). 

| Category (Kategorie) | BESCHREIBUNG |
|:---|:---|
| Administrative | Enthält die Datensätze aller Erstellungs-, Aktualisierungs-, Lösch- und Aktionsvorgänge, die über Resource Manager ausgeführt wurden. Beispiele für Verwaltungsereignisse sind das _Erstellen des virtuellen Computers_ und das _Löschen der Netzwerksicherheitsgruppe_.<br><br>Jede Aktion, die von einem Benutzer oder einer Anwendung mit Resource Manager durchgeführt wird, wird als Vorgang basierend auf einem bestimmten Ressourcentyp modelliert. Wenn der Vorgangstyp _Schreiben_, _Löschen_ oder _Aktion_ lautet, werden die Datensätze zum Start und zum Erfolg oder Fehler dieses Vorgangs in der Kategorie „Verwaltung“ aufgezeichnet. Verwaltungsereignisse umfassen außerdem alle Änderungen an der rollenbasierten Zugriffssteuerung in einem Abonnement. |
| Dienstintegrität | Enthält Datensätze zu allen Incidents im Zusammenhang mit der Dienstintegrität, die in Azure aufgetreten sind. Beispiel für ein Service Health-Ereignis: _Ausfallzeiten bei SQL Azure in der Region „USA, Osten“_ . <br><br>Es gibt fünf Typen von Service Health-Ereignissen: _Aktion erforderlich_, _Unterstützte Wiederherstellung_, _Incident_, _Wartung_, _Informationen_ und _Sicherheit_. Diese Ereignisse werden nur erstellt, wenn Sie über eine Ressource im Abonnement verfügen, die vom Ereignis betroffen wäre.
| Resource Health | Enthält Datensätze zu allen Ereignissen im Zusammenhang mit der Ressourcenintegrität, die für Ihre Azure-Ressourcen aufgetreten sind. Ein Beispiel für ein Resource Health-Ereignis ist _Integritätsstatus des virtuellen Computers ist zu „Nicht verfügbar“ gewechselt_.<br><br>Resource Health-Ereignisse können über einen von vier Integritätsstatus verfügen: _Verfügbar_, _Nicht verfügbar_, _Heruntergestuft_ und _Unbekannt_. Darüber hinaus können Resource Health-Ereignisse kategorisiert werden. Hierbei sind die Kategorien _Von der Plattform initiiert_ und _Vom Benutzer initiiert_ verfügbar. |
| Warnung | Enthält den Datensatz mit den Aktivierungen für Azure-Warnungen. Ein Beispiel für ein Warnungsereignis ist _CPU-Auslastung auf ‚myVM‘ liegt in den letzten 5 Minuten über 80_.|
| Autoscale | Enthält Datensätze zu Ereignissen im Zusammenhang mit der Engine für die Autoskalierung – basierend auf den Einstellungen für die Autoskalierung, die Sie in Ihrem Abonnement definiert haben. Ein Beispiel für ein Ereignis der Autoskalierung ist _Fehler beim automatischen zentralen Hochskalieren_. |
| Empfehlung | Enthält Empfehlungsereignisse von Azure Advisor. |
| Sicherheit | Enthält den Datensatz, der von allen Warnungen in Azure Security Center generiert wurde. Ein Beispiel für ein Sicherheitsereignis ist _Verdächtige Datei mit doppelter Erweiterung ausgeführt_. |
| Richtlinie | Enthält Datensätze aller Aktionsvorgänge für Auswirkungen, die von Azure Policy ausgeführt werden. Beispiele für Policy-Ereignisse sind _Überwachen_ und _Ablehnen_. Jede Aktion, die von Policy ausgeführt wird, ist als ein Vorgang für eine Ressource modelliert. |


## <a name="next-steps"></a>Nächste Schritte

* [Create a log profile to export the Azure Activity Log](activity-log-export.md) (Erstellen eines Protokollprofils zum Exportieren des Azure-Aktivitätsprotokolls)
* [Stream the Azure Activity Log to Event Hubs (Streamen des Azure-Aktivitätsprotokolls auf Event Hubs)](activity-logs-stream-event-hubs.md)
* [Archivieren des Aktivitätsprotokolls im Speicher](archive-activity-log.md)

