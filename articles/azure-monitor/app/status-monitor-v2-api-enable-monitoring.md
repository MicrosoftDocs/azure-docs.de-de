---
title: 'API-Referenz für Azure-Statusmonitor v2: Aktivieren der Überwachung | Microsoft-Dokumentation'
description: API-Referenz für Statusmonitor v2. Enable-ApplicationInsightsMonitoring. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
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
ms.openlocfilehash: 5393bf854b0bff40ee5e5ad78fc4865d22cd8334
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514355"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API für Statusmonitor v2: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

In diesem Artikel wird ein Cmdlet des [PowerShell-Moduls „Az.ApplicationMonitor“](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/) beschrieben.

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und wird für Produktionsworkloads nicht empfohlen. Einige Features werden möglicherweise nicht unterstützt oder bieten möglicherweise nur eingeschränkte Funktionen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>BESCHREIBUNG

Ermöglicht die Aktivierung der Überwachung von IIS-Apps auf einem Zielcomputer ohne Code.

Dieses Cmdlet ändert die IIS-Datei „applicationHost.config“ und legt einige Registrierungsschlüssel fest.
Es erstellt ferner die Datei „applicationinsights.ikey.config“, die den Instrumentierungsschlüssel definiert, der von jeder App verwendet wird.
IIS lädt beim Start das RedfieldModule. Dieses fügt das Application Insights SDK in Anwendungen ein, während diese Anwendungen gestartet werden.
Starten Sie IIS neu, damit die Änderungen wirksam werden.

Nachdem Sie die Überwachung aktiviert haben, empfiehlt es sich, [Live Metrics Stream](live-stream.md) zu verwenden, um schnell zu überprüfen, ob Ihre App Telemetrie an uns sendet.


> [!NOTE] 
> - Für den Einstieg benötigen Sie einen Instrumentierungsschlüssel. Weitere Informationen finden Sie unter [Erstellen einer Ressource](create-new-resource.md#copy-the-instrumentation-key).
> - Dieses Cmdlet erfordert, dass Sie unsere Lizenz- und Datenschutzbestimmungen durchlesen und akzeptieren.

> [!IMPORTANT] 
> Dieses Cmdlet erfordert eine PowerShell-Sitzung mit Administratorrechten und einer Ausführungsrichtlinie mit erhöhten Rechten. Weitere Informationen finden Sie unter [Ausführen von PowerShell als Administrator mit einer Ausführungsrichtlinie mit erhöhten Rechten](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy).

## <a name="examples"></a>Beispiele

### <a name="example-with-a-single-instrumentation-key"></a>Beispiel mit einem einzigen Instrumentierungsschlüssel
In diesem Beispiel wird allen Apps auf dem aktuellen Computer ein einziger Instrumentierungsschlüssel zugewiesen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Beispiel mit Zuordnung des Instrumentierungsschlüssels
In diesem Beispiel:
- `MachineFilter` sucht mithilfe des Platzhalters `'.*'` nach dem aktuellen Computer.
- `AppFilter='WebAppExclude'` gibt `null` als Instrumentierungsschlüssel an. Die angegebene App wird nicht instrumentiert.
- `AppFilter='WebAppOne'` weist der angegebenen App einen eindeutigen Instrumentierungsschlüssel zu.
- `AppFilter='WebAppTwo'` weist der angegebenen App einen eindeutigen Instrumentierungsschlüssel zu.
- Abschließend verwendet `AppFilter` auch den Platzhalter `'.*'`, um alle anderen Web-Apps zu suchen, die nicht von den früheren Regeln gefunden wurden, und weist einen Standardinstrumentierungsschlüssel zu.
- Zur besseren Lesbarkeit werden Leerzeichen hinzugefügt.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parameter

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Erforderlich.** Verwenden Sie diesen Parameter, um einen einzelnen Instrumentierungsschlüssel für die Verwendung durch alle Apps auf dem Zielcomputer bereitzustellen.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Erforderlich.** Verwenden Sie diesen Parameter, um mehrere Instrumentierungsschlüssel und eine Zuordnung der von jeder einzelnen App verwendeten Instrumentierungsschlüssel bereitzustellen.
Sie können ein einzelnes Installationsskript für mehrere Computer erstellen, indem Sie `MachineFilter` festlegen.

> [!IMPORTANT]
> Apps werden anhand der Regeln in der Reihenfolge gesucht, in der diese Regeln bereitgestellt werden. Daher sollten Sie die spezifischsten Regeln zuerst und die allgemeinsten Regeln zuletzt angeben.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** ist ein erforderlicher regulärer C#-Ausdruck des Computer- oder VM-Namens.
    - „.*“ findet alle
    - „ComputerName“ findet nur Computer mit dem exakten angegebenen Namen.
- **AppFilter** ist ein erforderlicher regulärer C#-Ausdruck des Computer- oder VM-Namens.
    - „.*“ findet alle
    - „ApplicationName“ findet nur IIS-Apps mit dem exakten angegebenen Namen.
- **InstrumentationKey** ist erforderlich, um die Überwachung der Apps zu aktivieren, die den beiden oben genannten Filtern entsprechen.
    - Behalten Sie für diesen Wert NULL bei, wenn Sie Regeln zum Ausschließen der Überwachung definieren möchten.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Optional.** Verwenden Sie diesen Schalter, um die Instrumentierungs-Engine zu aktivieren, sodass sie Ereignisse und Nachrichten über die Vorgänge bei der Ausführung eines verwalteten Prozesses sammelt. Diese Ereignisse und Nachrichten umfassen Abhängigkeitsergebniscodes, HTTP-Verben und SQL-Befehlstext.

Die Instrumentierungs-Engine erfordert Aufwand und ist standardmäßig deaktiviert.

### <a name="-acceptlicense"></a>-AcceptLicense
**Optional.** Verwenden Sie diesen Schalter, um die Lizenz- und Datenschutzbestimmungen in monitorlosen Installationen anzunehmen.

### <a name="-verbose"></a>-Verbose
**Allgemeiner Parameter.** Verwenden Sie diesen Parameter, um detaillierte Protokolle auszugeben.

### <a name="-whatif"></a>-WhatIf 
**Allgemeiner Parameter.** Verwenden Sie diesen Parameter, um Ihre Eingabeparameter zu testen und zu überprüfen, ohne die Überwachung tatsächlich zu aktivieren.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Beispielausgabe für eine erfolgreiche Aktivierung

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
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
