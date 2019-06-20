---
title: 'API-Referenz für Azure-Statusmonitor v2: Festlegen der Konfiguration | Microsoft-Dokumentation'
description: API-Referenz für Statusmonitor v2 Set-ApplicationInsightsMonitoringConfig. Überwachen der Websiteleistung ohne erneute Bereitstellung der Website Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
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
ms.openlocfilehash: 971fea76a23859f32437a1698b6d3094113737a1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255092"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>API für Statusmonitor v2: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

In diesem Dokument wird ein Cmdlet beschrieben, das als Teil des [PowerShell-Moduls „Az.ApplicationMonitor“](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/) zur Verfügung gestellt wird.

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>BESCHREIBUNG

Legen Sie die Konfigurationsdatei fest, ohne eine vollständige Neuinstallation durchzuführen. Starten Sie IIS neu, damit Ihre Änderungen wirksam werden.

> [!IMPORTANT] 
> Dieses Cmdlet erfordert eine PowerShell-Sitzung mit Administratorberechtigungen.


## <a name="examples"></a>Beispiele

### <a name="example-with-single-instrumentation-key"></a>Beispiel mit einem einzigen Instrumentierungsschlüssel
In diesem Beispiel werden alle Anwendungen auf dem aktuellen Computer einem einzigen Instrumentierungsschlüssel zugewiesen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Beispiel mit Instrumentierungsschlüsselzuordnung
In diesem Beispiel 
- findet `MachineFilter` den aktuellen Computer unter Verwendung des Platzhalters `'.*'`.
- stellt `AppFilter='WebAppExclude'` einen `null`-Instrumentierungsschlüssel bereit. Diese App wird nicht instrumentiert.
- weist `AppFilter='WebAppOne'` diese spezifische App einem eindeutigen Instrumentierungsschlüssel zu.
- weist `AppFilter='WebAppTwo'` ebenfalls diese spezifische App einem eindeutigen Instrumentierungsschlüssel zu.
- Abschließend verwendet `AppFilter` auch den Platzhalter `'.*'`, um alle anderen Web-Apps zu finden, die nicht von den früheren Regeln gefunden wurden, und weist einen Standard-Instrumentierungsschlüssel zu.
- Leerzeichen nur zur besseren Lesbarkeit hinzugefügt.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parameter 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Erforderlich.** Verwenden Sie diesen Parameter, um einen einzelnen iKey für die Verwendung durch alle Anwendungen auf dem Zielcomputer bereitzustellen.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Erforderlich.** Verwenden Sie diesen Parameter, um mehrere iKeys sowie eine Zuordnung bereitzustellen, welche Apps welchen iKey verwenden. Sie können ein einzelnes Installationsskript für mehrere Computer erstellen, indem Sie den MachineFilter festlegen. 

> [!IMPORTANT] 
> Anwendungen werden anhand der Regeln in der Reihenfolge gesucht, in der diese Regeln bereitgestellt werden. Daher sollten Sie die spezifischsten Regeln zuerst und die generischsten Regeln zuletzt angeben.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** ist ein erforderliches c#-RegEx des Computer- oder VM-Namens.
    - „.*“ findet alle
    - „ComputerName“ findet nur Computer mit exakt diesem Namen.
- **AppFilter** ist ein erforderliches c#-RegEx des Computer- oder VM-Namens.
    - „.*“ findet alle
    - „ApplicationName“ findet nur IIS-Anwendungen mit exakt diesem Namen.
- **InstrumentationKey** ist erforderlich, um die Überwachung der Anwendungen zu aktivieren, die den beiden obigen Filtern entsprechen.
    - Behalten Sie für diesen Wert Null bei, wenn Sie Regeln definieren möchten, um die Überwachung auszuschließen.


### <a name="-verbose"></a>-Verbose
**Allgemeiner Parameter.** Verwenden Sie diesen Parameter, um detaillierte Protokolle auszugeben.


## <a name="output"></a>Output

Standardmäßig keine Ausgabe.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Beispielhafte ausführliche Ausgabe beim Festlegen der Konfigurationsdatei mit -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Beispielhafte ausführliche Ausgabe beim Festlegen der Konfigurationsdatei mit -InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Nächste Schritte

  Anzeigen der Telemetrie:
 - [Untersuchen Sie Metriken](../../azure-monitor/app/metrics-explorer.md) für die Überwachung von Leistung und Auslastung.
- [Durchsuchen Sie Ereignisse und Protokolle](../../azure-monitor/app/diagnostic-search.md), um Probleme zu diagnostizieren.
- [Verwenden Sie Analytics](../../azure-monitor/app/analytics.md) für erweiterte Abfragen.
- [Erstellen Sie Dashboards.](../../azure-monitor/app/overview-dashboard.md)
 
 Hinzufügen weiterer Telemetrieelemente:
 - [Erstellen Sie Webtests](monitor-web-app-availability.md), um sicherzustellen, dass Ihre Website live bleibt.
- [Fügen Sie Webclient-Telemetriedaten hinzu](../../azure-monitor/app/javascript.md), um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe einfügen zu können.
- [Fügen Sie Ihrem Code das Application Insights SDK hinzu](../../azure-monitor/app/asp-net.md), um Ablaufverfolgungs- und Protokollaufrufe einfügen zu können.
 
 Weitere Möglichkeiten zur Verwendung von Statusmonitor v2:
 - Verwenden Sie unsere Anleitung für die [Problembehandlung](status-monitor-v2-troubleshoot.md) von Statusmonitor v2.
 - [Rufen Sie die Konfiguration ab](status-monitor-v2-api-get-config.md), um sicherzustellen, dass Ihre Einstellungen korrekt erfasst wurden.
 - [Rufen Sie den Status ab](status-monitor-v2-api-get-status.md), um die Überwachung zu überprüfen.
