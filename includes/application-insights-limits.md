---
title: Includedatei
description: Includedatei
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 31ff6ae66b13eb5e5abcde94e8d3df7f6e32f63c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66130810"
---
Es gibt einige Grenzwerte hinsichtlich der Anzahl von Metriken und Ereignissen pro Anwendung (d. h. pro Instrumentationsschlüssel). Die Beschränkungen hängen von dem von Ihnen ausgewählten [Tarif](https://azure.microsoft.com/pricing/details/application-insights/) ab.

| Resource | Standardlimit | Hinweis
| --- | --- | --- |
| Gesamtdaten pro Tag | 100 GB | Die Datenmenge kann durch Festlegen einer Obergrenze reduziert werden. Wird eine höhere Datenmenge benötigt, können Sie den Grenzwert im Portal auf bis zu 1.000 GB erhöhen. Bei Kapazitäten über 1.000 GB senden Sie eine E-Mail an AIDataCap@microsoft.com.
| Drosselung | 32.000 Ereignisse/s | Das Limit wird eine Minute lang gemessen.
| Beibehaltung von Daten | 90 Tage | Hierbei handelt es sich um eine Ressource für [Suche](../articles/azure-monitor/app/diagnostic-search.md), [Analyse](../articles/azure-monitor/app/analytics.md) und [Metrik-Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| [Webtests in mehreren Schritten](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) mit detaillierter Ergebnisaufbewahrung | 90 Tage | Diese Ressource liefert detaillierte Ergebnisse der einzelnen Schritte.
| Maximale Ereignisgröße | 64.000 |
| Eingenschaft und Länge der Namen von Metriken | 150 | Siehe [Typschemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Zeichenfolgenlänge des Eigenschaftswerts | 8.192 | Siehe [Typschemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Länge von Ablaufverfolgungs- und Ausnahmebenachrichtigungen | 32.768  | Siehe [Typschemas](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| [Verfügbarkeitstests](../articles/azure-monitor/app/monitor-web-app-availability.md) Anzahl pro App | 100 |
| Vermerkdauer von [Profiler](../articles/azure-monitor/app/profiler.md)-Daten | 5 Tage |
| Pro Tag gesendete [Profiler](../articles/azure-monitor/app/profiler.md)-Daten | 10 GB |

Weitere Informationen zu Preisen und Kontingenten für Application Insights finden Sie [hier](../articles/azure-monitor/app/pricing.md).