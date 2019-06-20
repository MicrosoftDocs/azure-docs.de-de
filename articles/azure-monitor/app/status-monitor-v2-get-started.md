---
title: Erste Schritte mit Azure Statusmonitor v2 | Microsoft-Dokumentation
description: Schnellstartanleitung für Statusmonitor v2 Überwachen der Websiteleistung ohne erneute Bereitstellung der Website Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
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
ms.openlocfilehash: f39eb7dbe74c194af43a99f69858215d9d7d251b
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688770"
---
# <a name="getting-started-with-status-monitor-v2"></a>Erste Schritte mit Statusmonitor v2

Dieses Dokument enthält die Schnellstartbefehle, die für die meisten Umgebungen geeignet sein dürften. Diese Anweisungen richten sich nach dem für das Verteilen von Updates verwendeten PowerShell-Katalog. Diese Befehle unterstützen den `-Proxy`-Parameter von PowerShell.

Beachten Sie unsere Seite mit [ausführlichen Anleitungen](status-monitor-v2-detailed-instructions.md). Dort finden Sie eine Erläuterung dieser Befehle, Anweisungen für die Anpassung sowie Informationen zur Problembehandlung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="download--install-via-powershell-gallery"></a>Herunterladen und Installieren über PowerShell-Katalog

### <a name="install-prerequisites"></a>Installieren der erforderlichen Komponenten
Ausführen von PowerShell als Administrator
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Beenden von PowerShell

### <a name="install-status-monitor-v2"></a>Installieren von Statusmonitor v2
Ausführen von PowerShell als Administrator
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Aktivieren der Überwachung
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Manuelles Herunterladen und Installieren (Offline-Option)
### <a name="manual-download"></a>Manuelles Herunterladen
Laden Sie hier die neueste Version des Moduls manuell herunter: https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Extrahieren und Installieren von Statusmonitor v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Aktivieren der Überwachung
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

Sonstige Verwendung von Statusmonitor v2:

- Beachten Sie die [ausführlichen Anleitungen](status-monitor-v2-detailed-instructions.md), dort finden Sie eine Erläuterung der in diesem Handbuch verwendeten Befehle.
- Verwenden Sie unsere Anleitung für die [Problembehandlung](status-monitor-v2-troubleshoot.md) von Statusmonitor v2.
