---
title: 'Azure Application Insights-Telemetriedatenmodell: Ablaufverfolgungstelemetrie | Microsoft-Dokumentation'
description: Application Insights-Datenmodell für Ablaufverfolgungstelemetrie
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: df85aafc81b199610c02f0faecb06e804fda24bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60899284"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Ablaufverfolgungstelemetrie: Application Insights-Datenmodell

Ablaufverfolgungstelemetrie stellt (in [Application Insights](../../azure-monitor/app/app-insights-overview.md)) Ablaufverfolgungsanweisungen im Format `printf` dar, die sich für eine Textsuche eignen. `Log4Net`, `NLog` und andere textbasierte Protokolldateieinträge werden in Instanzen dieses Typs übersetzt. Die Ablaufverfolgung weist für die Erweiterbarkeit keine Messungen auf.

## <a name="message"></a>`Message`

Ablaufverfolgungsmeldung.

Max. Länge: 32768 Zeichen

## <a name="severity-level"></a>Schweregrad

Schweregrad der Ablaufverfolgung. Möglicher Wert: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Benutzerdefinierte Eigenschaften

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Untersuchen von .NET-Ablaufverfolgungsprotokollen in Application Insights](../../azure-monitor/app/asp-net-trace-logs.md).
- [Untersuchen von Java-Ablaufverfolgungsprotokollen in Application Insights](../../azure-monitor/app/java-trace-logs.md).
- Lesen Sie die Informationen zu den Application Insights-Typen und zum Datenmodell unter [Datenmodell](data-model.md).
- [Schreiben benutzerdefinierter Telemetriedaten für die Ablaufverfolgung](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)
- Lesen Sie die Informationen zu den von Application Insights unterstützten [Plattformen](../../azure-monitor/app/platforms.md).
