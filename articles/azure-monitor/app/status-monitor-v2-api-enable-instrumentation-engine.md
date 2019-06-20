---
title: 'API-Referenz für Azure-Statusmonitor v2: Aktivieren der Instrumentierungs-Engine | Microsoft-Dokumentation'
description: API-Referenz für Statusmonitor v2. Enable-InstrumentationEngine. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514382"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API für Statusmonitor v2: Enable-InstrumentationEngine (v0.2.1-alpha)

In diesem Artikel wird ein Cmdlet des [PowerShell-Moduls „Az.ApplicationMonitor“](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/) beschrieben.

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Einige Features werden möglicherweise nicht unterstützt oder bieten möglicherweise nur eingeschränkte Funktionen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>BESCHREIBUNG

Aktiviert die Instrumentierungs-Engine durch Festlegen einiger Registrierungsschlüssel.
Starten Sie IIS neu, damit die Änderungen wirksam werden.

Die Instrumentierungs-Engine kann Daten ergänzen, die mit .NET SDKs gesammelt wurden.
Es werden Ereignisse und Nachrichten gesammelt, die die Ausführung eines verwalteten Prozesses beschreiben. Diese Ereignisse und Nachrichten umfassen Abhängigkeitsergebniscodes, HTTP-Verben und SQL-Befehlstext.

Aktivieren Sie die Instrumentierungs-Engine in den folgenden Fällen:
- Sie haben mithilfe des Cmdlets „Enable“ bereits die Überwachung, aber nicht die Instrumentierungs-Engine aktiviert.
- Sie haben Ihre App mithilfe der .NET SDKs manuell instrumentiert und möchten zusätzliche Telemetriedaten sammeln.

> [!IMPORTANT] 
> Dieses Cmdlet erfordert eine PowerShell-Sitzung mit Administratorberechtigungen.

> [!NOTE] 
> - Dieses Cmdlet erfordert, dass Sie unsere Lizenz- und Datenschutzbestimmungen durchlesen und akzeptieren.
> - Die Instrumentierungs-Engine erfordert zusätzlichen Aufwand und ist standardmäßig deaktiviert.

## <a name="examples"></a>Beispiele

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parameter

### <a name="-acceptlicense"></a>-AcceptLicense
**Optional.** Verwenden Sie diesen Schalter, um die Lizenz- und Datenschutzbestimmungen in monitorlosen Installationen anzunehmen.

### <a name="-verbose"></a>-Verbose
**Allgemeiner Parameter.** Verwenden Sie diesen Parameter, um detaillierte Protokolle auszugeben.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Beispielausgabe nach erfolgreicher Aktivierung der Instrumentierungs-Engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Rufen Sie die Konfiguration ab](status-monitor-v2-api-get-config.md), um sicherzustellen, dass Ihre Einstellungen korrekt erfasst wurden.
 - [Rufen Sie den Status ab](status-monitor-v2-api-get-status.md), um die Überwachung zu überprüfen.
