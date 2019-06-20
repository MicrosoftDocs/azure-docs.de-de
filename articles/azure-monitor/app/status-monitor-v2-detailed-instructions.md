---
title: Ausführliche Anweisungen für Azure-Statusmonitor v2 | Microsoft-Dokumentation
description: Ausführliche Anweisungen für die ersten Schritte mit Azure-Statusmonitor v2. Überwachen der Websiteleistung ohne erneute Bereitstellung der Website Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
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
ms.openlocfilehash: 9525d18b51954f32fd819a2efdefca61b39bc185
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688482"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Ausführliche Anweisungen für Azure-Statusmonitor v2

Diese Dokument erläutert die Integration in den PowerShell-Katalog und das Herunterladen des ApplicationMonitor-Moduls. Wir haben die am häufigsten verwendeten Parameter für die ersten Schritte dokumentiert.
Für den Fall, dass kein Internetzugang zur Verfügung steht, haben wir auch manuelle Anweisungen beigefügt.

> [!IMPORTANT]
> Statusmonitor v2 ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="instrumentation-key"></a>Instrumentierungsschlüssel

Zum Einstieg benötigen Sie einen Instrumentierungsschlüssel. Weitere Informationen finden Sie unter [Erstellen einer Application Insights-Ressource](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Führen Sie PowerShell als Administrator mit der Ausführungsrichtlinie mit erhöhten Rechten aus.

**Als Administrator ausführen**: 
- Beschreibung: PowerShell benötigen Administratorrechten, um Änderungen an Ihrem Computer vorzunehmen.

**Die Auftragsausführungsrichtlinie**:
- Beschreibung: Standardmäßig werden laufende PowerShell-Skripts deaktiviert. Es wird empfohlen, RemoteSigned-Skripts nur für den aktuellen Bereich zuzulassen.
- Referenz: [Informationen zu Ausführungsrichtlinien](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) und [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Optionale Parameter:
    - `-Force` Damit wird die Bestätigungsaufforderung übersprungen.

**Fehlerbeispiele:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Voraussetzungen für PowerShell

Überprüfen Sie Ihre aktuelle Version von PowerShell, indem Sie den Befehl ausführen: `$PSVersionTable`.
Der Befehl erstellt die folgende Ausgabe:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Diese Anleitung wurde auf einem Windows 10-Computer mit den oben genannten Versionen geschrieben und getestet.

## <a name="prerequisites-for-powershell-gallery"></a>Voraussetzungen für den PowerShell-Katalog

Diese Schritte bereiten Ihren Server darauf vor, Module aus dem PowerShell-Katalog herunterzuladen.

> [!NOTE] 
> Die Unterstützung für den PowerShell-Katalog ist in Windows 10, Windows Server 2016 und PowerShell 6 enthalten. Informationen für ältere Versionen finden Sie in diesem Dokument: [Installieren von PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Führen Sie PowerShell als Administrator mit der Ausführungsrichtlinie mit erhöhten Rechten aus.
2. NuGet-Paketanbieter 
    - Beschreibung: Dieser Anbieter ist erforderlich, um mit NuGet-basierten Repositorys wie PowerShellGallery zu interagieren.
    - Referenz: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Optionale Parameter:
        - `-Proxy` Gibt einen Proxyserver für die Anforderung an.
        - `-Force` Damit wird die Bestätigungsaufforderung übersprungen. 
    
    Sie erhalten diese Eingabeaufforderung, wenn NuGet nicht eingerichtet ist:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Vertrauenswürdige Repositorys
    - Beschreibung: Standardmäßig ist der PowerShell-Katalog ein nicht vertrauenswürdiges Repository.
    - Referenz: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Optionale Parameter:
        - `-Proxy` Gibt einen Proxyserver für die Anforderung an.

    Sie erhalten diese Eingabeaufforderung, wenn der PowerShell-Katalog nicht vertrauenswürdig ist:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Sie können diese Änderung bestätigen und alle PSRepositories überprüfen, indem Sie das cmd: `Get-PSRepository` ausführen.

4. PowerShellGet-Version 
    - Beschreibung: Dieses Modul enthält die Tools, mit denen andere Module aus dem PowerShell-Katalog abgerufen werden können. v1.0.0.0.1 wird mit Windows 10 und Windows Server ausgeliefert. Version 1.6.0 ist die erforderliche Mindestversion. Um zu überprüfen, welche Version installiert ist, führen Sie den Befehl: `Get-Command -Module PowerShellGet` aus.
    - Referenz: [Installieren von PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Optionale Parameter:
        - `-Proxy` Gibt einen Proxyserver für die Anforderung an.
        - `-Force` Damit wird die Warnung „bereits installiert“ ignoriert, und die aktuelle Version installiert.

    Sie erhalten diesen Fehler, wenn Sie nicht die neueste Version von PowerShellGet verwenden:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Starten Sie PowerShell neu. Es ist nicht möglich, die neue Version in der aktuellen Sitzung zu laden. Alle neuen Powershell-Sitzungen werden mit dem neuesten PowerShellGet geladen.

## <a name="download--install-via-powershell-gallery"></a>Herunterladen und Installieren über PowerShell-Katalog

Mit diesen Schritten wird das Az.ApplicationMonitor-Modul aus dem PowerShell-Katalog heruntergeladen.

1. Die Voraussetzungen für den PowerShell-Katalog müssen erfüllt sein.
2. Führen Sie PowerShell als Administrator mit der Ausführungsrichtlinie mit erhöhten Rechten aus.
3. Installieren des Az.ApplicationMonitor-Moduls
    - Referenz: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Optionale Parameter:
        - `-Proxy` Gibt einen Proxyserver für die Anforderung an.
        - `-AllowPrerelease` Dies ermöglicht die Installation von Alpha- und Beta-Versionen.
        - `-AcceptLicense` Damit wird die Eingabeaufforderung „Lizenz annehmen“ übersprungen.
        - `-Force` Dadurch wird die Warnung „Nicht vertrauenswürdiges Repository“ ignoriert.

## <a name="download--install-manually-offline-option"></a>Manuelles Herunterladen und Installieren (Offline-Option)

Wenn Sie aus irgendeinem Grund keine Verbindung zum PowerShell-Modul herstellen können, können Sie das Az.ApplicationMonitor-Modul manuell herunterladen und installieren.

### <a name="manually-download-the-latest-nupkg"></a>Manuelles Herunterladen des neuesten NuGet-Pakets

1. Navigieren Sie zu: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Wählen Sie die neueste Version aus dem Versionsverlauf aus.
3. Suchen Sie „Installationsoptionen“ und wählen Sie „Manueller Download“.

### <a name="option-1-install-into-powershell-modules-directory"></a>Option 1: Installieren des PowerShell-Modulverzeichnisses
Installieren Sie das manuell heruntergeladene PowerShell-Modul in einem PowerShell-Verzeichnis, damit es von PowerShell-Sitzungen erkannt werden kann.
Weitere Informationen finden Sie unter [Installieren eines PowerShell-Moduls](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Entpacken von nupkg als ZUP-Datei mit Expand-Archiv (v1.0.1.0)

- Beschreibung: Die Basisversion der Microsoft.PowerShell.Archive (v1.0.1.0) kann keine Nupkg-Dateien extrahieren. Benennen Sie die Datei mit der Erweiterung „.zip“ um.
- Referenz: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Entpacken von nupkg mit Expand-Archiv (v1.1.0.0)

- Beschreibung: Verwenden Sie eine aktuelle Version von Expand-Archive, um Nupkgs zu entpacken, ohne die Erweiterung umzubenennen. 
- Referenz: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) und [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Option 2: Entpacken und manuell importieren
Installieren Sie das manuell heruntergeladene PowerShell-Modul in einem PowerShell-Verzeichnis, damit es von PowerShell-Sitzungen erkannt werden kann.
Weitere Informationen finden Sie unter [Installieren eines PowerShell-Moduls](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Wenn Sie in ein anderes Verzeichnis installieren, müssen Sie das Modul manuell mit [Import-Modul](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6) importieren.

> [!IMPORTANT] 
> Die Installation installiert DLLs über relative Pfade. Speichern Sie den Inhalt dieses Pakets in Ihrem vorgesehenen Runtime-Verzeichnis und bestätigen Sie, dass die Zugriffsrechte Lese-, aber keine Schreibvorgänge erlauben.

1. Ändern Sie die Erweiterung auf „.zip“ und extrahieren Sie den Inhalt des Pakets in Ihr gewünschtes Installationsverzeichnis.
2. Suchen Sie den Pfad zu „Az.ApplicationMonitor.psd1“.
3. Führen Sie PowerShell als Administrator mit der Ausführungsrichtlinie mit erhöhten Rechten aus. 
4. Laden Sie das Modul mit dem folgenden Befehl: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Proxy

Wenn Sie einen Computer in Ihrem privaten Intranet überwachen, ist es notwendig, den HTTP-Datenverkehr über einen Proxy zu leiten.

Die PowerShell-Befehle zum Herunterladen und Installieren von des Az.ApplicationMonitor-Moduls aus dem PowerShell-Katalog unterstützen einen `-Proxy`-Parameter.
Beachten Sie die obigen Anweisungen, wenn Sie Ihre Installationsskripte schreiben.

Das Application Insights SDK muss die Telemetrie Ihrer Anwendung an Microsoft senden. Wir empfehlen, die Proxyeinstellungen für Ihre Anwendung in Ihrer web.config-Datei zu konfigurieren. Weitere Informationen finden Sie in [Application Insights: Häufig gestellte Fragen: Proxy-PassThrough](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Aktivieren der Überwachung 

Cmd: `Enable-ApplicationInsightsMonitoring`

Lesen Sie unsere [API-Referenz](status-monitor-v2-api-enable-monitoring.md) für eine ausführliche Beschreibung der Verwendung dieses Cmdlets. 



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
