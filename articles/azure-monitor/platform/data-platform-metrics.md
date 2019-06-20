---
title: Metriken in Azure Monitor | Microsoft-Dokumentation
description: Es werden Metriken in Azure Monitor beschrieben, bei denen es sich um einfache Überwachungsdaten handelt, mit denen Szenarien vom Typ „Nahezu in Echtzeit“ unterstützt werden können.
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
ms.openlocfilehash: 319def9072dbf38eff44ca4459428f1ef99a2b54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64867960"
---
# <a name="metrics-in-azure-monitor"></a>Metriken in Azure Monitor

> [!NOTE]
> Die Azure Monitor-Datenplattform basiert auf zwei fundamentalen Datentypen: Metriken und Protokolle. In diesem Artikel werden Metriken beschrieben. Unter [Protokolle in Azure Monitor](data-platform-logs.md) finden Sie eine ausführliche Beschreibung von Protokollen, und unter [Azure Monitor-Datenplattform](data-platform.md) finden Sie einen Vergleich.

Metriken in Azure Monitor sind einfach gehalten und unterstützen Szenarien vom Typ „Nahezu in Echtzeit“, sodass sie besonders für Warnungen und die schnelle Erkennung von Problemen hilfreich sind. In diesem Artikel wird beschrieben, wie Metriken strukturiert sind und welche Möglichkeiten Sie damit haben. Außerdem sind die verschiedenen Datenquellen angegeben, in denen Daten in Metriken gespeichert sind.

## <a name="what-are-metrics"></a>Was sind Metriken?
Metriken sind numerische Werte, die einen Aspekt eines Systems zu einem bestimmten Zeitpunkt beschreiben. Metriken werden in regelmäßigen Abständen erfasst und sind gut für Warnmeldungen geeignet, da sie häufig abgefragt werden können und mit relativ einfacher Logik schnell eine Warnung ausgelöst werden kann.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Welche Möglichkeiten haben Sie mit Azure Monitor-Metriken?
In der folgenden Tabelle sind die unterschiedlichen Optionen zur Nutzung von Metrikdaten in Azure Monitor aufgeführt.

|  |  |
|:---|:---|
| Analysieren | Verwenden Sie den [Metrik-Explorer](metrics-charts.md) zum Analysieren von erfassten Metriken in einem Diagramm und Vergleichen von Metriken verschiedener Ressourcen. |
| Visualisieren | Heften Sie ein Diagramm aus dem Metrik-Explorer an ein [Azure-Dashboard](../learn/tutorial-app-dashboards.md) an.<br>Erstellen Sie eine [Arbeitsmappe](../app/usage-workbooks.md) zum Kombinieren mehrerer Datasets in einem interaktiven Bericht. Exportieren Sie die Ergebnisse einer Abfrage für [Grafana](grafana-plugin.md), um die Dashboardfunktionen zu nutzen und die Daten mit anderen Datenquellen zu kombinieren. |
| Warnung | Konfigurieren einer [Warnungsregel für eine Metrik](alerts-metric.md), die eine Benachrichtigung sendet oder eine [automatisierte Aktion](action-groups.md) ausführt, sobald der Metrikwert einen Schwellenwert überschreitet. |
| Automatisieren |  Verwenden von [Autoskalierung](autoscale-overview.md), um Ressourcen basierend auf einem Schwellenwert, der von einem Metrikwert über- oder unterschritten wird, herauf- oder herabzusetzen. |
| Export | [Weiterleiten von Metriken an Protokolle](diagnostic-logs-stream-log-store.md), um Daten in Azure Monitor-Metriken zusammen mit Daten in Azure Monitor-Protokollen zu analysieren und Metrikwerte länger als 93 Tage zu speichern.<br>Streamen von Metriken an einen [Event Hub](stream-monitoring-data-event-hubs.md), um sie an externe Systeme zu leiten. |
| Gerätehandle | Zugreifen auf Metrikwerte über eine Befehlszeile mit [PowerShell-Cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights).<br>Zugreifen auf Metrikwerte über eine benutzerdefinierte Anwendung per [REST-API](rest-api-walkthrough.md).<br>Zugreifen auf Metrikwerte über eine Befehlszeile per [CLI](/cli/azure/monitor/metrics). |
| Archivieren | [Archivieren](..//learn/tutorial-archive-data.md) des Leistungs- oder Integritätsverlaufs Ihrer Ressourcen zu Kompatibilitäts-/Überwachungszwecken oder zur Offline-Berichterstellung. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Wie sind Daten in Azure Monitor-Metriken strukturiert?
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

## <a name="interacting-with-azure-monitor-metrics"></a>Interagieren mit Azure Monitor-Metriken
Verwenden Sie den [Metrik-Explorer](metrics-charts.md), um die Daten in Ihrer Metrikdatenbank interaktiv zu analysieren und die Werte mehrerer Metriken im zeitlichen Verlauf in Diagrammen darzustellen. Sie können die Diagramme an ein Dashboard anheften, um sie mit anderen Visualisierungen anzuzeigen. Zudem haben Sie die Möglichkeit, Metriken mit der [REST-API für die Azure-Überwachung](rest-api-walkthrough.md) abzurufen.

![Metrik-Explorer](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Quellen von Azure Monitor-Metriken
Es gibt drei grundlegende Quellen von Metriken, die von Azure Monitor erfasst werden. Nachdem diese Metriken in der Azure Monitor-Metrikdatenbank erfasst wurden, können sie unabhängig von ihrer Quelle zusammen ausgewertet werden.

**Plattformmetriken** werden von Azure-Ressourcen erstellt und geben Ihnen einen Einblick in ihre Integrität und Leistung. Jeder Ressourcentyp erstellt einen [eigenen Satz von Metriken](metrics-supported.md); dazu ist keinerlei Konfiguration erforderlich. Plattformmetriken werden von Azure-Ressourcen minütlich erfasst, sofern in der Definition der Metrik nichts anderes angegeben ist. 

**Gastbetriebssystem-Metriken** werden über das Gastbetriebssystem eines virtuellen Computers erfasst. Aktivieren Sie Gastbetriebssystem-Metriken für virtuelle Windows-Computer per [Windows-Diagnoseerweiterung (WAD)](../platform/diagnostics-extension-overview.md) und für virtuelle Linux-Computer per [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/).

**Anwendungsmetriken** werden von Application Insights für Ihre überwachten Anwendungen erstellt und helfen Ihnen beim Erkennen von Leistungsproblemen und beim Nachverfolgen von Trends in der Nutzung Ihrer Anwendung. Dies beinhaltet solche Werte wie die _Serverantwortzeit_ und _Browserausnahmen_.

**Benutzerdefinierte Metriken** sind Metriken, die Sie zusätzlich zu den automatisch verfügbaren Standardmetriken definieren. Sie können [benutzerdefinierte Metriken in Ihrer Anwendung definieren](../app/api-custom-events-metrics.md), die von Application Insights überwacht wird, oder benutzerdefinierte Metriken für einen Azure-Dienst erstellen, indem Sie die [API für benutzerdefinierte Metriken](metrics-store-custom-rest-api.md) verwenden.

## <a name="retention-of-metrics"></a>Aufbewahrung von Metriken
Für die meisten Ressourcen in Azure werden Metriken für 93 Tage gespeichert. Es gibt einige Ausnahmen:
  * **Klassische Gastbetriebssystemmetriken**. Klassische Gastbetriebssystemmetriken werden 14 Tage lang aufbewahrt. Für eine längere Aufbewahrung empfehlen wir die Verwendung neuer Gastbetriebssystemmetriken, die mit der [Windows-Diagnoseerweiterung (WAD)](../platform/diagnostics-extension-overview.md) und für virtuelle Linux-Computer mit dem [InfluxData Telegraf Agent](https://www.influxdata.com/time-series-platform/telegraf/) gesammelt werden.
  * **Auf Application Insights-Protokollen basierende Metriken**. [Protokollbasierte Metriken](../app/pre-aggregated-metrics-log-metrics.md) werden im Hintergrund in Protokollabfragen übersetzt. Ihre Aufbewahrung entspricht der Aufbewahrung von Ereignissen in den zugrunde liegenden Protokollen. Für die Application Insights-Ressourcen werden die Protokolle 90 Tage lang gespeichert. 

> [!NOTE]
> Sie können [Plattformmetriken für Azure Monitor-Ressourcen an einen Log Analytics-Arbeitsbereich senden](diagnostic-logs-stream-log-store.md), um Informationen zu langfristigen Trends zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die [Azure Monitor-Datenplattform](data-platform.md).
- Erfahren Sie mehr über [Protokolldaten in Azure Monitor](data-platform-logs.md).
- Erfahren Sie mehr über die für verschiedene Ressourcen in Azure [verfügbaren Überwachungsdaten](data-sources.md).
