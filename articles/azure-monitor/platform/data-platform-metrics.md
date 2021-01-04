---
title: Metriken in Azure Monitor | Microsoft-Dokumentation
description: Es werden Metriken in Azure Monitor beschrieben, bei denen es sich um einfache Überwachungsdaten handelt, mit denen Szenarien vom Typ „Nahezu in Echtzeit“ unterstützt werden können.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: f64a91e3b285c265296c361366a10443eda18201
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489428"
---
# <a name="azure-monitor-metrics-overview"></a>Überblick über Metriken in Azure Monitor
Metriken in Azure Monitor sind ein Feature von Azure Monitor, das numerische Daten aus [überwachten Ressourcen](../monitor-reference.md) in einer Zeitreihendatenbank erfasst. Metriken sind numerische Werte, die in regelmäßigen Abständen erfasst werden und einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Metriken in Azure Monitor sind einfach gehalten und unterstützen Szenarien vom Typ „Nahezu in Echtzeit“, sodass sie besonders für Warnungen und die schnelle Erkennung von Problemen hilfreich sind. Sie können sie interaktiv mit dem Metrik-Explorer analysieren, sich proaktiv mit einer Warnung benachrichtigen lassen, wenn ein Wert einen Schwellenwert überschreitet, oder sie in einer Arbeitsmappe oder einem Dashboard visualisieren.


> [!NOTE]
> Metriken in Azure Monitor machen die eine Hälfte der Datenplattform aus, die Azure Monitor unterstützt. Die andere Hälfte besteht in [Azure Monitor-Protokollen](data-platform-logs.md), die Protokoll- und Leistungsdaten erfassen und organisieren. Mithilfe einer umfassenden Abfragesprache können diese Protokolle analysiert werden. Metriken sind einfacher gehalten als Daten in Azure Monitor-Protokollen und unterstützen Quasi-Echtzeit-Szenarios, sodass sie besonders für Warnungen und die schnelle Erkennung von Problemen hilfreich sind. In Metriken können jedoch nur numerische Daten in einer bestimmten Struktur gespeichert werden, während dies in Protokollen für eine Vielzahl verschiedener Datentypen mit jeweils eigener Struktur möglich ist. Sie können auch komplexe Analysen von Protokolldaten mithilfe von Protokollabfragen durchführen, die für die Analyse von Metrikdaten nicht verwendet werden können.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Welche Möglichkeiten haben Sie mit Azure Monitor-Metriken?
In der folgenden Tabelle sind die unterschiedlichen Optionen zur Nutzung von Metriken in Azure Monitor aufgeführt.

|  |  |
|:---|:---|
| **Analysieren** | Verwenden Sie den [Metrik-Explorer](metrics-charts.md) zum Analysieren von erfassten Metriken in einem Diagramm und Vergleichen von Metriken verschiedener Ressourcen. |
| **Warnung** | Konfigurieren einer [Warnungsregel für eine Metrik](alerts-metric.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](action-groups.md) ausführt, sobald der Metrikwert einen Schwellenwert überschreitet. |
| **Visualisieren** | Heften Sie ein Diagramm aus dem Metrik-Explorer an ein [Azure-Dashboard](../learn/tutorial-app-dashboards.md) an.<br>Erstellen Sie eine [Arbeitsmappe](./workbooks-overview.md) zum Kombinieren mehrerer Datasets in einem interaktiven Bericht. Exportieren Sie die Ergebnisse einer Abfrage für [Grafana](grafana-plugin.md), um die Dashboardfunktionen zu nutzen und die Daten mit anderen Datenquellen zu kombinieren. |
| **Automatisieren** |  Verwenden von [Autoskalierung](autoscale-overview.md), um Ressourcen basierend auf einem Schwellenwert, der von einem Metrikwert über- oder unterschritten wird, herauf- oder herabzusetzen. |
| **Abrufen** | Zugreifen auf Metrikwerte über eine Befehlszeile mit [PowerShell-Cmdlets](/powershell/module/az.applicationinsights).<br>Zugreifen auf Metrikwerte über eine benutzerdefinierte Anwendung per [REST-API](rest-api-walkthrough.md).<br>Zugreifen auf Metrikwerte über eine Befehlszeile per [CLI](/cli/azure/monitor/metrics). |
| **Export** | [Weiterleiten von Metriken an Protokolle](./resource-logs.md#send-to-azure-storage), um Daten in Azure Monitor-Metriken zusammen mit Daten in Azure Monitor-Protokollen zu analysieren und Metrikwerte länger als 93 Tage zu speichern.<br>Streamen von Metriken an einen [Event Hub](stream-monitoring-data-event-hubs.md), um sie an externe Systeme zu leiten. |
| **Archivieren** | [Archivieren](./platform-logs-overview.md) des Leistungs- oder Integritätsverlaufs Ihrer Ressourcen zu Kompatibilitäts-/Überwachungszwecken oder zur Offline-Berichterstellung. |

![Übersicht der Metriken](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Datensammlung
Es gibt drei grundlegende Quellen von Metriken, die von Azure Monitor erfasst werden. Nachdem diese Metriken in der Azure Monitor-Metrikdatenbank erfasst wurden, können sie unabhängig von ihrer Quelle zusammen ausgewertet werden.

**Azure-Ressourcen:** Plattformmetriken werden von Azure-Ressourcen erstellt und geben Ihnen einen Einblick in ihre Integrität und Leistung. Jeder Ressourcentyp erstellt einen [eigenen Satz von Metriken](metrics-supported.md); dazu ist keinerlei Konfiguration erforderlich. Plattformmetriken werden von Azure-Ressourcen minütlich erfasst, sofern in der Definition der Metrik nichts anderes angegeben ist. 

**Anwendungen**: Metriken werden von Application Insights für Ihre überwachten Anwendungen erstellt und helfen Ihnen beim Erkennen von Leistungsproblemen und beim Nachverfolgen von Trends in der Nutzung Ihrer Anwendung. Dies beinhaltet solche Werte wie die _Serverantwortzeit_ und _Browserausnahmen_.

**Agents für virtuelle Computer:** Metriken werden über das Gastbetriebssystem eines virtuellen Computers erfasst. Aktivieren Sie Gastbetriebssystem-Metriken für virtuelle Windows-Computer per [Windows-Diagnoseerweiterung (WAD)](./diagnostics-extension-overview.md) und für virtuelle Linux-Computer per [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Benutzerdefinierte Metriken:** Sie können Metriken zusätzlich zu den automatisch verfügbaren Standardmetriken definieren. Sie können [benutzerdefinierte Metriken in Ihrer Anwendung definieren](../app/api-custom-events-metrics.md), die von Application Insights überwacht wird, oder benutzerdefinierte Metriken für einen Azure-Dienst erstellen, indem Sie die [API für benutzerdefinierte Metriken](metrics-store-custom-rest-api.md) verwenden.

- Unter [Was wird von Azure Monitor überwacht?](../monitor-reference.md) finden Sie eine vollständige Liste der Datenquellen, die Daten an eine Log Analytics-Metrik senden.

## <a name="metrics-explorer"></a>Metrik-Explorer
Verwenden Sie den [Metrik-Explorer](metrics-charts.md), um die Daten in Ihrer Metrikdatenbank interaktiv zu analysieren und die Werte mehrerer Metriken im zeitlichen Verlauf in Diagrammen darzustellen. Sie können die Diagramme an ein Dashboard anheften, um sie mit anderen Visualisierungen anzuzeigen. Zudem haben Sie die Möglichkeit, Metriken mit der [REST-API für die Azure-Überwachung](rest-api-walkthrough.md) abzurufen.

![Metrik-Explorer](media/data-platform/metrics-explorer.png)

- Unter [Erste Schritte mit dem Azure-Metrik-Explorer](metrics-getting-started.md) finden Sie Informationen zu den ersten Schritten mit dem Metrik-Explorer.

## <a name="data-structure"></a>Datenstruktur
Mit Azure Monitor-Metriken erfasste Daten werden in einer Zeitreihendatenbank gespeichert, die für das Analysieren von Daten mit Zeitstempel optimiert ist. Jede Gruppe von Metrikwerten stellt eine Zeitreihe mit den folgenden Eigenschaften dar:

* Zeitpunkt, zu dem der Wert erfasst wurde
* Ressource, auf die sich der Wert bezieht
* Namespace, der wie eine Kategorie für die Metrik fungiert
* Metrikname
* Eigentlicher Wert
* Einige Metriken weisen ggf. mehrere Dimensionen auf, wie unter [Mehrdimensionale Metriken](#multi-dimensional-metrics) beschrieben. Benutzerdefinierte Metriken können über bis zu 10 Dimensionen verfügen.

## <a name="multi-dimensional-metrics"></a>Mehrdimensionale Metriken
Eine Schwierigkeit besteht bei Metrikdaten darin, dass häufig nur eingeschränkte Informationen zur Verfügung stehen, wenn es um die Bereitstellung von Kontext für erfasste Werte geht. In Azure Monitor wird dieses Problem mit mehrdimensionalen Metriken gelöst. Dimensionen einer Metrik sind Name/Wert-Paare, die zusätzliche Daten zum Beschreiben des Metrikwerts enthalten. Eine Metrik namens _Verfügbarer Speicherplatz_ kann beispielsweise eine Dimension mit dem Namen _Laufwerk_ und den Werten _C:_ und _D:_ aufweisen. In diesem Fall kann der verfügbare Speicherplatz entweder für beide Laufwerke oder für jedes Laufwerk separat angezeigt werden.

Im folgenden Beispiel werden zwei Datasets für eine hypothetische Metrik namens _Netzwerkdurchsatz_ dargestellt. Das erste Dataset weist keine Dimensionen auf. Das zweite Dataset enthält Werte mit den zwei Dimensionen _IP-Adresse_ und _Richtung_:

### <a name="network-throughput"></a>Netzwerkdurchsatz

| Timestamp     | Metrikwert |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1\.331,8 KBit/s |
| 8/9/2017 8:15 | 1\.141,4 KBit/s |
| 8/9/2017 8:16 | 1\.110,2 KBit/s |

Diese nichtdimensionale Metrik kann nur grundlegende Fragen wie etwa Folgende beantworten: „Wie hoch war mein Netzwerkdurchsatz zu einem bestimmten Zeitpunkt?“.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Netzwerkdurchsatz mit zwei Dimensionen („IP“ und „Richtung“)

| Timestamp     | Dimension „IP“   | Dimension „Richtung“ | Metrikwert|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Send"    | 646,5 KBit/s |
| 8/9/2017 8:14 | IP="192.168.5.2" | Direction="Receive" | 420,1 KBit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Send"    | 150,0 KBit/s |
| 8/9/2017 8:14 | IP="10.24.2.15"  | Direction="Receive" | 115,2 KBit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Send"    | 515,2 KBit/s |
| 8/9/2017 8:15 | IP="192.168.5.2" | Direction="Receive" | 371,1 KBit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Send"    | 155,0 KBit/s |
| 8/9/2017 8:15 | IP="10.24.2.15"  | Direction="Receive" | 100,1 KBit/s |

Diese Metrik kann Fragen wie die folgenden beantworten: „Wie hoch war der Netzwerkdurchsatz für jede IP-Adresse?“ und „Wie viele Daten wurden gesendet und empfangen?“. Mehrdimensionale Metriken enthalten im Gegensatz zu nichtdimensionalen Metriken zusätzlich einen Analyse- und Diagnosewert.

## <a name="retention-of-metrics"></a>Aufbewahrung von Metriken
Für die meisten Ressourcen in Azure werden Metriken für 93 Tage gespeichert. Es gibt einige Ausnahmen:

**Gastbetriebssystemmetriken**
-   **Klassische Gastbetriebssystemmetriken:** Dies ist Leistungsindikatoren, die von der [Diagnose-Erweiterung für Windows (WAD)](./diagnostics-extension-overview.md) oder der [Diagnose-Erweiterung für Linux (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) erfasst und an ein Azure Storage-Konto weitergeleitet werden. Die Vermerkdauer für diese Metriken beträgt 14 Tage.
-   **An Azure Monitor-Metriken gesendete Gastbetriebssystemmetriken:** Dies sind Leistungsindikatoren, die von der [Diagnose-Erweiterung für Windows (WAD)](diagnostics-extension-overview.md) erfasst und an die [Azure Monitor-Datensenke](diagnostics-extension-overview.md#data-destinations) gesendet werden. Alternativ können sie auch über den [InfluxData Telegraf-Agent](https://www.influxdata.com/time-series-platform/telegraf/) auf Linux-Computern gesendet werden. Die Vermerkdauer für diese Metriken beträgt 93 Tage.
-   **Vom Log Analytics-Agent erfasste Gastbetriebssystemmetriken:** Dies sind Leistungsindikatoren, die vom Log Analytics-Agent erfasst und an einen Log Analytics-Arbeitsbereich gesendet werden. Die Vermerkdauer für diese Metriken beträgt 31 Tage. Sie kann auf bis zu 2 Jahre verlängert werden.

**Auf Application Insights-Protokollen basierende Metriken**. 
- [Protokollbasierte Metriken](../app/pre-aggregated-metrics-log-metrics.md) werden im Hintergrund in Protokollabfragen übersetzt. Ihre Aufbewahrung entspricht der Aufbewahrung von Ereignissen in den zugrunde liegenden Protokollen. Für die Application Insights-Ressourcen werden die Protokolle 90 Tage lang gespeichert.


> [!NOTE]
> Sie können [Plattformmetriken für Azure Monitor-Ressourcen an einen Log Analytics-Arbeitsbereich senden](./resource-logs.md#send-to-azure-storage), um Informationen zu langfristigen Trends zu erhalten.





## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Azure Monitor-Datenplattform](data-platform.md).
- Erfahren Sie mehr über [Protokolldaten in Azure Monitor](data-platform-logs.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](data-sources.md).

