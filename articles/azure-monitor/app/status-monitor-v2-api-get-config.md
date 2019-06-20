---
title: 'API-Referenz für Azure-Statusmonitor v2: Abrufen der Konfiguration | Microsoft-Dokumentation'
description: API-Referenz für Statusmonitor v2. Get-ApplicationInsightsMonitoringConfig. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 1f7061c9823ddc8ff7f8f42976041f1c9ff68fc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514396"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>API für Statusmonitor v2: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

In diesem Artikel wird ein Cmdlet des [PowerShell-Moduls „Az.ApplicationMonitor“](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/) beschrieben.

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Einige Features werden möglicherweise nicht unterstützt oder bieten möglicherweise nur eingeschränkte Funktionen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>BESCHREIBUNG

Ruft die Konfigurationsdatei ab und gibt die Werte in der Konsole aus.

> [!IMPORTANT] 
> Dieses Cmdlet erfordert eine PowerShell-Sitzung mit Administratorberechtigungen.

## <a name="examples"></a>Beispiele

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parameter

Es müssen keine Parameter angegeben werden.

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>Aus der Konfigurationsdatei ausgelesene Beispielausgabe

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Nächste Schritte

  Anzeigen der Telemetrie:
 - [Untersuchen Sie Metriken](../../azure-monitor/app/metrics-explorer.md) zum Überwachen der Leistung und Nutzung.
- [Durchsuchen Sie Ereignisse und Protokolle](../../azure-monitor/app/diagnostic-search.md), um Probleme zu diagnostizieren.
- Verwenden Sie [Analytics](../../azure-monitor/app/analytics.md) für erweiterte Abfragen.
- [Erstellen Sie Dashboards](../../azure-monitor/app/overview-dashboard.md).
 
 Hinzufügen weiterer Telemetrieelemente:
 - [Erstellen Sie Webtests](monitor-web-app-availability.md), um sicherzustellen, dass Ihre Website live bleibt.
- [Fügen Sie Webclient-Telemetriedaten hinzu](../../azure-monitor/app/javascript.md), um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe zu aktivieren.
- [Fügen Sie Ihrem Code das Application Insights SDK hinzu](../../azure-monitor/app/asp-net.md), um Ablaufverfolgungs- und Protokollaufrufe einfügen zu können.
 
 Weitere Möglichkeiten zur Verwendung von Statusmonitor v2:
 - Verwenden Sie unsere Anleitung für die [Problembehandlung](status-monitor-v2-troubleshoot.md) von Statusmonitor v2.
 - Nehmen Sie mithilfe des Cmdlets [Set config](status-monitor-v2-api-set-config.md) Änderungen an der Konfiguration vor.
