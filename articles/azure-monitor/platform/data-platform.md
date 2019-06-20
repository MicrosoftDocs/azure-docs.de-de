---
title: Azure Monitor-Datenplattform | Microsoft-Dokumentation
description: Die von Azure Monitor gefassten Überwachungsdaten unterteilen sich in Metriken, die einfach strukturiert und imstande sind, Szenarien nahezu in Echtzeit zu unterstützen, und Protokolle, die für die erweiterte Analyse verwendet werden.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8883c1e7f2874e1e2e61b8eca122f2ec294c7849
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60808928"
---
# <a name="azure-monitor-data-platform"></a>Azure Monitor-Datenplattform

Um bei den heutigen komplexen Computingumgebungen mit verteilten Anwendungen, die sowohl auf Clouddiensten als auch lokalen Diensten basieren, für Transparenz zu sorgen, müssen Betriebsdaten auf jeder Ebene und von jeder Komponente des verteilten Systems gesammelt werden. Sie müssen in der Lage sein, umfassende Einblicke in diese Daten zu erhalten und sie in einer zentralen Konsole mit verschiedenen Perspektiven zu konsolidieren, um der Vielzahl von Beteiligten in Ihrer Organisation gerecht zu werden.

[Azure Monitor](../overview.md) sammelt und aggregiert Daten aus einer Vielzahl von Quellen auf einer gemeinsamen Datenplattform, wo diese zur Analyse, Visualisierung und Ausgabe von Warnungen verwendet werden können. Durch eine konsistente Benutzeroberfläche für Daten aus mehreren Quellen erhalten Sie umfassende Einblicke in alle überwachten Ressourcen und das sogar mit Daten von anderen Diensten, die ihre Daten in Azure Monitor speichern.


![Azure Monitor – Übersicht](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Daten für Transparenz in Azure Monitor
Metriken, Protokolle und verteilte Ablaufverfolgungen werden allgemein als die drei Säulen der Transparenz bezeichnet. Hierbei handelt es sich um die verschiedenen Arten von Daten, die von einem Überwachungstool gesammelt und analysiert werden müssen, um ausreichende Transparenz eines überwachten Systems zu bieten. Transparenz kann erreicht werden, indem Daten von mehreren Säulen korreliert und Daten übergreifend für die gesamte Gruppe überwachter Ressourcen aggregiert werden. Da Azure Monitor Daten aus mehreren Quellen zusammen speichert, können die Daten mithilfe einer gemeinsamen Gruppe von Tools korreliert und analysiert werden. Zusätzlich zum Hosten von Daten für andere Dienste werden Daten auch übergreifend für mehrere Azure-Abonnements und -Mandanten korreliert.

Azure-Ressourcen generieren eine erhebliche Menge an Überwachungsdaten. Azure Monitor konsolidiert diese Daten zusammen mit Überwachungsdaten aus anderen Quellen entweder auf einer Metrik- oder einer Protokollplattform. Diese sind jeweils für bestimmte Überwachungsszenarien optimiert und unterstützen verschiedene Funktionen in Azure Monitor. Für die Nutzung von Funktionen wie Datenanalysen, Visualisierungen oder Warnungen müssen Sie die Unterschiede kennen, damit Sie das erforderliche Szenario auf die effizienteste und kostengünstigste Weise implementieren können. Dienste zum Gewinnen von Erkenntnissen in Azure Monitor, z.B. [Application Insights](../app/app-insights-overview.md) oder [Azure Monitor für VMs](../insights/vminsights-overview.md) bieten Analysetools, mit denen Sie sich auf das jeweilige Überwachungsszenario konzentrieren können, ohne die Unterschiede zwischen den beiden Datentypen verstehen zu müssen. 


### <a name="metrics"></a>metrics
[Metriken](data-platform-metrics.md) sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Sie werden in regelmäßigen Abständen gesammelt und durch einen Zeitstempel, einen Namen, einen Wert und eine oder mehrere definierende Bezeichnungen identifiziert. Metriken können mithilfe einer Vielzahl von Algorithmen aggregiert, mit anderen Metriken verglichen und in Bezug auf Trends im zeitlichen Verlauf analysiert werden. 

Metriken in Azure Monitor werden in einer Zeitreihendatenbank gespeichert, die für das Analysieren von Daten mit Zeitstempel optimiert ist. Dadurch sind Metriken besonders für Warnungen und das schnelle Erkennen von Problemen geeignet. Sie bieten Informationen über die Leistung Ihres Systems, müssen aber in der Regel mit Protokollen kombiniert werden, um die zugrunde liegende Ursache von Problemen zu identifizieren.

Metriken sind für die interaktive Analyse im Azure-Portal mit [Metrik-Explorer](../app/metrics-explorer.md) verfügbar. Sie können einem [Azure-Dashboard](../learn/tutorial-app-dashboards.md) zur Visualisierung in Kombination mit anderen Daten hinzugefügt und für [Warnungen](alerts-metric.md) nahezu in Echtzeit verwendet werden.

Weitere Informationen zu Azure Monitor-Metriken, einschließlich ihrer Datenquellen, finden Sie unter [Metriken in Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Protokolle
[Protokolle](data-platform-logs.md) sind Ereignisse, die im System aufgetreten sind. Sie können verschiedene Arten von Daten enthalten und in strukturierter Form oder als Freitext mit einem Zeitstempel vorliegen. Sie können sporadisch als Ereignisse in der Umgebung erstellt werden und Protokolleinträge generieren. Ein stark ausgelastetes System wird in der Regel ein größeres Protokollvolume erzeugen.

Protokolle in Azure Monitor werden in einem Log Analytics-Arbeitsbereich basierend auf [Azure Data Explorer](/azure/data-explorer/) gespeichert, der eine leistungsstarke Analyse-Engine und eine [umfangreiche Abfragesprache](/azure/kusto/query/) bereitstellt. Protokolle bieten in der Regel ausreichende Informationen zum Bereitstellen des vollständigen Kontexts des zu identifizierenden Problems und sind beim Ermitteln der zugrunde liegenden Ursache von Problemen hilfreich.

> [!NOTE]
> Es ist wichtig, zwischen Azure Monitor-Protokollen und Quellen von Protokolldaten in Azure zu unterscheiden. Beispielsweise werden Ereignisse auf Abonnementebene in Azure in ein [Aktivitätsprotokoll](activity-logs-overview.md) geschrieben, das Sie über das Azure Monitor-Menü anzeigen können. Die meisten Ressourcen schreiben Betriebsinformationen in ein [Diagnoseprotokoll](diagnostic-logs-overview.md), das Sie an verschiedene Speicherorte weiterleiten können. Azure Monitor-Protokolle ist eine Protokolldatenplattform, die Aktivitätsprotokolle und Diagnoseprotokolle zusammen mit anderen Überwachungsdaten erfasst, um umfassende Analysen für die gesamte Gruppe von Ressourcen zu ermöglichen.


 Sie können mit [Log Analytics](../log-query/portals.md) im Azure-Portal interaktiv mit [Protokollabfragen](../log-query/log-query-overview.md) arbeiten oder die Ergebnisse einem [Azure-Dashboard](../learn/tutorial-app-dashboards.md) zur Visualisierung in Kombination mit anderen Daten hinzufügen. Sie können auch [Protokollwarnungen](alerts-log.md) erstellen, die eine Warnung basierend auf den Ergebnissen einer geplanten Abfrage auslösen.

Weitere Informationen zu Azure Monitor-Protokollen, einschließlich ihrer Datenquellen, finden Sie unter [Protokolle in Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Verteilte Ablaufverfolgungen
Ablaufverfolgungen sind Reihen verknüpfter Ereignisse, die einer Benutzeranforderung durch ein verteiltes System folgen. Hiermit können das Verhalten von Anwendungscode und die Leistung verschiedener Transaktionen bestimmt werden. Während Protokolle häufig von einzelnen Komponenten eines verteilten Systems erstellt werden, misst eine Ablaufverfolgung den Betrieb und die Leistung Ihrer Anwendung über die gesamte Reihe von Komponenten.

Die verteilte Ablaufverfolgung in Azure Monitor wird mit dem [Application Insights SDK](../app/distributed-tracing.md) aktiviert, und Ablaufverfolgungsdaten werden mit anderen Anwendungsprotokolldaten gespeichert, die von Application Insights gesammelt werden. Dadurch stehen sie für dieselben Analysetools wie andere Protokolldaten bereit, einschließlich Protokollabfragen, Dashboards und Warnungen.

Weitere Informationen zur verteilten Ablaufverfolgung finden Sie unter [Was ist die verteilte Ablaufverfolgung?](../app/distributed-tracing.md)


## <a name="compare-azure-monitor-metrics-and-logs"></a>Vergleich von Azure Monitor-Metriken und -Protokollen

Die folgende Tabelle enthält einen Vergleich zwischen Metriken und Protokollen in Azure Monitor.

| Attribut  | metrics | Protokolle |
|:---|:---|:---|
| Vorteile | Einfach strukturiert und für Szenarien nahezu in Echtzeit geeignet, z.B. Warnungen. Ideal für das schnelle Erkennen von Problemen. | Mit umfangreicher Abfragesprache analysiert. Ideal für tiefgehende Analysen und Identifizierung der zugrunde liegenden Ursache. |
| Daten | Nur numerische Werte | Text oder numerische Daten |
| Strukturdefinition | Standardsatz von Eigenschaften, z.B. Stichprobenzeit, überwachte Ressource, ein numerischer Wert. Einige Metriken umfassen mehrere Dimensionen für die weitere Definition. | Eindeutiger Satz von Eigenschaften je nach Protokolltyp. |
| Sammlung | Werden in regelmäßigen Abständen gesammelt. | Werden möglicherweise sporadisch gesammelt, wenn Ereignisse das Erstellen eines Datensatzes auslösen. |
| Ansicht im Azure-Portal | Metrik-Explorer | Log Analytics |
| Enthaltene Datenquellen | Von Azure-Ressourcen gesammelte Plattformmetriken<br>Von Application Insights überwachte Anwendungen<br>Benutzerdefiniert nach Anwendung oder API | Anwendungs- und Diagnoseprotokolle<br>Überwachungslösungen<br>Agents und VM-Erweiterungen<br>Anwendungsanforderungen und -ausnahmen<br>Azure Security Center<br>Datensammler-API |

## <a name="collect-monitoring-data"></a>Sammeln von Überwachungsdaten
Verschiedene [Datenquellen für Azure Monitor](data-sources.md) schreiben entweder in einen Log Analytics-Arbeitsbereich (Protokolle), die Azure Monitor-Metrikdatenbank (Metriken) oder beides. Einige Quellen schreiben direkt in diese Datenspeicher, während andere möglicherweise an einen anderen Speicherort schreiben (z.B. Azure-Speicher) und eine Konfiguration erfordern, um Protokolle oder Metriken auszufüllen. 

Eine Liste der verschiedenen Datenquellen, die den jeweiligen Typ ausfüllen, finden Sie unter [Metriken in Azure Monitor](data-platform-metrics.md) und [Protokolle in Azure Monitor](data-platform-logs.md).


## <a name="stream-data-to-external-systems"></a>Streamen von Daten zu externen Systemen
Über die Verwendung der Tools in Azure für die Analyse von Überwachungsdaten hinaus besteht bei Ihnen möglicherweise die Anforderung, diese auch an ein externes Tool wie ein SIEM-Produkt (Security Information and Event Management) weiterzuleiten. Diese Weiterleitung erfolgt normalerweise mithilfe von [Azure Event Hubs](/azure/event-hubs/) direkt von den überwachten Ressourcen aus. Einige Datenquellen können dazu konfiguriert werden, Daten direkt an einen Event Hub zu senden, während Sie einen anderen Prozess wie z.B. eine Logik-App verwenden können, um die erforderlichen Daten abzurufen. Ausführlichere Informationen finden Sie unter [Streamen von Azure-Überwachungsdaten an einen Event Hub für die Verwendung durch ein externes Tool](stream-monitoring-data-event-hubs.md).



## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich ausführlicher über [Metriken in Azure Monitor](data-platform-metrics.md).
- Informieren Sie sich ausführlicher über [Protokolle in Azure Monitor](data-platform-logs.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](data-sources.md).
