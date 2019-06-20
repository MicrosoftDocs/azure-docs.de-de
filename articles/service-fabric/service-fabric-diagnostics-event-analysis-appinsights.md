---
title: Azure Service Fabric-Ereignisanalyse mit Application Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Ereignisse unter Verwendung von Application Insights zur Überwachung und Diagnose von Azure Service Fabric-Clustern visualisiert und analysiert werden.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4c620bbb0e17abfacb504866230786a971ff409
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60393204"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Ereignisanalyse und Visualisierung mit Application Insights

Application Insights gehört zu Azure Monitor und ist eine erweiterbare Plattform für die Überwachung und Diagnose von Anwendungen. Sie enthält ein leistungsfähiges Analyse- und Abfragetool, ein anpassbares Dashboard und Visualisierungen sowie weitere Optionen, z.B. automatisierte Warnungen. Die Integration von Application Insights in Service Fabric umfasst Toolumgebungen für Visual Studio und das Azure-Portal sowie spezifische Service Fabric-Metriken, sodass Sie eine umfassende, sofort einsatzbereite Protokollierung erhalten. Mit Application Insights werden zwar viele Protokolle automatisch für Sie erstellt und erfasst, es empfiehlt sich jedoch, Ihren Anwendungen eine weitergehende benutzerdefinierte Protokollierung hinzuzufügen, um eine umfassendere Diagnoseumgebung zu erstellen.

Dieser Artikel geht auf folgende allgemeine Fragen ein:

* Wie kann ich mich über die Vorgänge in meiner Anwendung und in meinen Diensten informieren und Telemetriedaten sammeln?
* Wie behebe ich Probleme mit meiner Anwendung (insbesondere Probleme bei der Kommunikation zwischen Diensten)?
* Wie erhalte ich Metriken zur Leistung meiner Dienste wie Seitenladezeit, HTTP-Anforderungen und Ähnliches?

In diesem Artikel erfahren Sie, wie Sie in Application Insights Erkenntnisse gewinnen und Probleme behandeln. Informationen zum Einrichten und Konfigurieren von Application Insights mit Service Fabric finden Sie in [diesem Tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Überwachen in Application Insights

Application Insights bietet bereits standardmäßig umfassende Service Fabric-Funktionen. Auf der Übersichtsseite stellt Application Insights wichtige Informationen zu Ihrem Dienst bereit (beispielsweise zur Antwortzeit und zur Anzahl verarbeiteter Anforderungen). Wenn Sie am oberen Rand auf die Suchschaltfläche klicken, wird eine Liste mit Anforderungen angezeigt, die kürzlich in Ihrer Anwendung ausgeführt wurden. Darüber hinaus werden hier ggf. nicht erfolgreiche Anforderungen angezeigt, und Sie können die aufgetretenen Fehler diagnostizieren.

![Application Insights-Übersicht](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

Die Liste im rechten Bereich der vorherigen Abbildung enthält hauptsächlich zwei Arten von Einträgen: Anforderungen und Ereignisse. Anforderungen sind an die API der App gerichtete Aufrufe (in diesem Fall über HTTP-Anforderungen). Ereignisse sind benutzerdefinierte Ereignisse und fungieren als Telemetrie, die Sie an einer beliebigen Stelle in Ihrem Code hinzufügen können. Weitere Informationen zur Instrumentierung Ihrer Anwendungen finden Sie unter [Application Insights-API für benutzerdefinierte Ereignisse und Metriken](../azure-monitor/app/api-custom-events-metrics.md). Wenn Sie auf eine Anforderung klicken, werden weitere Details angezeigt, wie in der folgenden Abbildung zu sehen. Sie umfassen unter anderem Service Fabric-spezifische Daten, die im Application Insights Service Fabric-NuGet-Paket erfasst werden. Diese Informationen können zur Problembehandlung und zur Ermittlung des Anwendungszustands herangezogen sowie in Application Insights durchsucht werden.

![Application Insights-Anforderungsdetails](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights bietet eine eigene Ansicht zum Abfragen sämtlicher eingehender Daten. Klicken Sie am oberen Rand der Übersichtsseite auf „Metrik-Explorer“, um zum Application Insights-Portal zu gelangen. Hier können Sie unter Verwendung der Kusto-Abfragesprache Abfragen für die zuvor erwähnten benutzerdefinierten Ereignisse sowie für Anforderungen, Ausnahmen, Leistungsindikatoren und weitere Metriken ausführen. Das folgende Beispiel zeigt alle Anforderungen der letzten Stunde:

![Application Insights-Anforderungsdetails](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Weitere Informationen zu den Funktionen des Application Insights-Portals finden Sie in der [Dokumentation zum Application Insights-Portal](../azure-monitor/app/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-eventflow"></a>Konfigurieren von Application Insights mit EventFlow

Wenn Sie EventFlow zum Aggregieren von Ereignissen verwenden, müssen Sie das NuGet-Paket `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` importieren. Der Abschnitt *outputs* von *eventFlowConfig.json* muss folgenden Code enthalten:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Stellen Sie sicher, dass Sie die erforderlichen Änderungen in Ihren Filtern vornehmen und alle anderen Eingaben (zusammen mit den jeweiligen NuGet-Paketen) einfügen.

## <a name="application-insights-sdk"></a>Application Insights SDK

Es empfiehlt sich, EventFlow und WAD als Aggregationslösungen zu verwenden, da diese einen modularen Ansatz für die Diagnose und Überwachung bieten. Das heißt, wenn Sie die Ausgaben von EventFlow ändern möchten, ist keine Änderung der eigentlichen Instrumentierung, sondern lediglich eine einfache Änderung der Konfigurationsdatei erforderlich. Wenn Sie sich jedoch für die Verwendung von Application Insights entscheiden und nicht planen, auf eine andere Plattform umzusteigen, empfiehlt es sich ggf., das neue SDK von Application Insights zu verwenden, um Ereignisse zu aggregieren und an Application Insights zu senden. Das bedeutet, dass Sie EventFlow nicht mehr zum Senden der Daten an Application Insights konfigurieren müssen, sondern stattdessen das Service Fabric-NuGet-Paket von Application Insights installieren. Details zu diesem Paket finden Sie [hier](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

Unter [Application Insights-Unterstützung für Microservices und Container](https://azure.microsoft.com/blog/app-insights-microservices/) werden einige der neuen Features beschrieben, an denen gearbeitet wird (derzeit noch in der Betaversion) und mit denen Sie über umfangreichere vordefinierte Überwachungsoptionen mit Application Insights verfügen. Dazu gehören die Abhängigkeitsnachverfolgung (wird beim Erstellen einer AppMap aller Ihrer Dienste und Anwendungen in einem Cluster und der zugehörigen Kommunikation verwendet) und eine bessere Korrelation der Ablaufverfolgungen, die von Ihren Diensten stammen (trägt zur besseren Lokalisierung eines Problems im Workflow einer Anwendung oder eines Diensts bei).

Wenn Sie beim Entwickeln in .NET voraussichtlich einige der Service Fabric-Programmiermodelle verwenden und Application Insights als Plattform zum Visualisieren und Analysieren von Ereignis- und Protokolldaten verwenden möchten, empfiehlt es sich, das Application Insights SDK für den Workflow der Überwachung und Diagnose zu verwenden. Informationen zu den ersten Schritten mit Application Insights zum Sammeln und Anzeigen Ihrer Protokolle finden Sie [hier](../azure-monitor/app/asp-net-more.md) und [hier](../azure-monitor/app/asp-net-trace-logs.md).

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigieren zur Application Insights-Ressource im Azure-Portal

Wenn Sie Application Insights als Ausgabe für die Ereignisse und Protokolle konfiguriert haben, sollten nach wenigen Minuten Informationen in der Application Insights-Ressource angezeigt werden. Navigieren Sie zur Application Insights-Ressource, um zum Dashboard für die Ressource zu gelangen. Klicken Sie auf der Application Insights-Taskleiste auf **Suchen**, um die zuletzt empfangenen Ablaufverfolgungen anzuzeigen und zu filtern.

*Metrik-Explorer* ist ein nützliches Tool zum Erstellen von benutzerdefinierten Dashboards basierend auf den Metriken, die Ihre Anwendungen, Dienste und Cluster melden. Informationen zum Einrichten einiger benutzerdefinierter Diagramme basierend auf Ihren gesammelten Daten finden Sie unter [Untersuchen von Metriken in Application Insights](../azure-monitor/app/metrics-explorer.md).

Durch Klicken auf **Analytics** gelangen Sie zum Application Insights Analytics-Portal, in dem Sie Ereignisse und Ablaufverfolgungen in größerem Umfang und mit mehr Optionen abfragen können. Weitere Informationen hierzu finden Sie unter [Analytics in Application Insights](../azure-monitor/app/analytics.md).

## <a name="next-steps"></a>Nächste Schritte

* [Richten Sie Warnungen in AI ein](../azure-monitor/app/alerts.md), um Benachrichtigungen zu Änderungen der Leistung oder Nutzung zu erhalten.
* Die [intelligente Erkennung in Application Insights](../azure-monitor/app/proactive-diagnostics.md) führt eine proaktive Analyse der an Application Insights gesendeten Telemetriedaten durch, um Sie vor potenziellen Leistungsproblemen zu warnen.
