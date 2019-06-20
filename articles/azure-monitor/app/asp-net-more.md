---
title: Optimale Nutzung von Azure Application Insights | Microsoft-Dokumentation
description: Nachdem Sie die ersten Schritte mit Application Insights gemacht haben, finden Sie hier eine Zusammenfassung der Funktionen, mit denen Sie sich beschäftigen können.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7e62efb0b64b0b882b36126af4a1f9d520d92475
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230640"
---
# <a name="more-telemetry-from-application-insights"></a>Weitere Telemetriedaten aus Application Insights
Nachdem Sie [Application Insights zu Ihrem .ASP NET-Code hinzugefügt haben](../../azure-monitor/app/asp-net.md), können Sie einige weitere Einstellungen vornehmen, um noch mehr Telemetriedaten zu erhalten. 

| Aktion | Ergebnis|
|---|---|
|(IIS-Server) [Installieren Sie den Statusmonitor](https://go.microsoft.com/fwlink/?LinkId=506648) auf jedem Servercomputer.<br/>(Azure-Web-Apps) Öffnen Sie in der Azure-Systemsteuerung für die Web-App das Blatt „Application Insights“.| [**Leistungsindikatoren**](../../azure-monitor/app/performance-counters.md)<br/>[**Ausnahmen**](asp-net-exceptions.md) – ausführliche Stapelüberwachungen<br/>[**Abhängigkeiten**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Fügen Sie den JavaScript-Codeausschnitt zu Webseiten hinzu](../../azure-monitor/app/javascript.md).|[Seitenleistung](../../azure-monitor/app/usage-overview.md), Browserausnahmen, AJAX-Leistung. Benutzerdefinierte clientseitige Telemetriedaten.|
|[Erstellen Sie Verfügbarkeitswebtests](../../azure-monitor/app/monitor-web-app-availability.md).|Sie erhalten Benachrichtigungen, wenn Ihre Website nicht mehr verfügbar ist.|
|[Stellen Sie sicher, dass „buildinfo.config“](https://msdn.microsoft.com/library/dn449058.aspx) von MSBuild generiert wird.|[Anmerkungen zu Builds in Metrikdiagrammen](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Schreiben Sie benutzerdefinierte Ereignisse und Metriken](../../azure-monitor/app/api-custom-events-metrics.md).|Anzahl der Geschäftsereignisse und Metriken, Nachverfolgung ausführlicher Informationen zur Nutzung und vieles mehr.|
|[Erstellen Sie ein Profil Ihrer Livewebsite](https://aka.ms/AIProfilerPreview).|Detaillierte Funktionszeitangaben aus Ihrer Live-Web-App|






