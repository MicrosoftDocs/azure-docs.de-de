---
title: PowerShell-Modul AADCloudSyncTools für die Azure AD Connect-Cloudbereitstellung
description: In diesem Artikel wird das Installieren des Agents für die Azure AD Connect-Cloudbereitstellung beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba71d3eedbb75780c20499daa890556574494a35
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348136"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-provisioning"></a>PowerShell-Modul AADCloudSyncTools für die Azure AD Connect-Cloudbereitstellung

Mit der Veröffentlichung von Update 2 der öffentlichen Vorschauversion hat Microsoft das PowerShell-Modul AADCloudSyncTools eingeführt.  Dieses Modul stellt eine Reihe nützlicher Tools bereit, die Sie zum Verwalten Ihrer Bereitstellungen der Azure AD Connect-Cloudsynchronisierung verwenden können.

## <a name="pre-requisites"></a>Voraussetzungen
Die folgenden Voraussetzungen müssen erfüllt sein:
- Dieses Modul verwendet die MSAL-Authentifizierung, daher muss das Modul MSAL.PS installiert sein. Es ist nicht mehr von Azure AD oder Azure AD Preview abhängig.   Führen Sie zur Überprüfung in einem Administratorfenster von PowerShell `Get-module MSAL.PS` aus. Wenn das Modul ordnungsgemäß installiert ist, erhalten Sie eine Antwort.  Sie können für die Installation der aktuellen Version von MSAL.PS `Install-AADCloudSyncToolsPrerequisites` verwenden.
- Das PowerShell-Modul AzureAD.  Einige der Cmdlets benötigen Teile des PowerShell-Moduls AzureAD.  Führen Sie zur Überprüfung in einem Administratorfenster von PowerShell `Get-module AzureAD` aus. Sie sollten eine Antwort erhalten.  Sie können zum Installieren der aktuellen Version des PowerShell-Moduls AzureAD `Install-AADCloudSyncToolsPrerequisites` verwenden.
- Das Installieren von Modulen aus PowerShell kann die Verwendung von TLS 1.2 erzwingen.  Um sicherzustellen, dass Sie Module installieren können, legen Sie Folgendes fest: \
`[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 `

## <a name="install-the-aadcloudsynctools-powershell-module"></a>Installieren des PowerShell-Moduls AADCloudSyncTools
Führen Sie die folgenden Schritte aus, um das Modul AADCloudSyncTools zu installieren und zu verwenden:

1.  Öffnen Sie Windows PowerShell mit Administratorrechten.
2.  Geben Sie `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` ein, und drücken Sie die EINGABETASTE.
3.  Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ``` powershell
    Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"
    ```
3.  Drücken Sie die EINGABETASTE.
4.  Um zu überprüfen, ob das Modul installiert wurde, geben Sie folgenden Befehl ein (Sie können ihn auch kopieren und einfügen):
    ```powershell
    Get-module AADCloudSyncTools
    ```
5.  Jetzt sollten Informationen zum Modul angezeigt werden.
6.  Führen Sie anschließend Folgendes aus:
    ``` powershell
    Install-AADCloudSyncToolsPrerequisites
    ```
7.  Damit werden die PowerShell-Get-Module installiert.  Schließen Sie das PowerShell-Fenster.
8.  Öffnen Sie Windows PowerShell mit Administratorrechten.
9.  Importieren Sie das Modul erneut wie in Schritt 3 beschrieben.
10. Ausführen von `Install-AADCloudSyncToolsPrerequisites` zur Installation der Module MSAL und AzureAD
11. Alle Voraussetzungen sollten erfolgreich installiert sein. ![Installieren von Modulen](media/reference-powershell/install-1.png)

## <a name="aadcloudsynctools--cmdlets"></a>Cmdlets in AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Verwendet das Modul AzureAD, um eine Verbindung mit Azure AD herzustellen, und das Modul MSAL.PS, um ein Token für Microsoft Graph anzufordern


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exportiert alle Problembehandlungsdaten und packt sie in eine komprimierte Datei. Dies geschieht folgendermaßen:
 1. Startet eine ausführliche Ablaufverfolgung mit Start-AADCloudSyncToolsVerboseLogs.  Diese Überwachungsprotokolle finden Sie im Ordner C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Erfasst drei Minuten lang ein Ablaufverfolgungsprotokoll.
   Sie können mit „-TracingDurationMins“ eine andere Zeit angeben oder mit „-SkipVerboseTrace“ die ausführliche Ablaufverfolgung überspringen.
 3. Beendet die ausführliche Ablaufverfolgung mit Stop-AADCloudSyncToolsVerboseLogs
 4. Sammelt Ereignisanzeigeprotokolle für die letzten 24 Stunden
 5. Komprimiert alle Agent-Protokolle, ausführlichen Protokolle und Ereignisanzeigeprotokolle in einer ZIP-Datei im Ordner „Dokumente“ des Benutzers. 
 </br>Sie können mit „-OutputPath \<folder path\>“ einen anderen Ausgabeordner angeben.

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Zeigt Details zum Azure AD-Mandanten und den Status interner Variablen an

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt die Informationen zum Synchronisierungsauftrag zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt den Zeitplan zum Synchronisierungsauftrag zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt das Schema des Synchronisierungsauftrags zurück.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Verwendet Graph, um das Schema des Synchronisierungsauftrags für die angegebene Synchronisierungsauftrags-ID abzurufen, und gibt alle Bereiche der Filtergruppe aus.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt die Einstellungen des Synchronisierungsauftrags zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Verwendet Graph, um AD2AAD-Dienstprinzipale abzurufen, und gibt den Status des Synchronisierungsauftrags zurück.
Kann auch mit der jeweiligen Synchronisierungsauftrags-ID als Parameter aufgerufen werden.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Verwendet Graph, um die Dienstprinzipale für AD2AAD und/oder SyncFabric abzurufen.
Ohne Parameter werden nur AD2AAD-Dienstprinzipale zurückgegeben.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Prüft, ob PowerShellGet v2.2.4.1 oder höher und die Module AzureAD und MSAL.PS vorhanden sind, und installiert diese andernfalls.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Ruft eine Webanforderung auf, für die als Parameter der URI, die Methode und der Text angegeben sind

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Verwendet Azure AD PowerShell, um das aktuelle Konto (sofern vorhanden) zu löschen, und setzt die Authentifizierung mit dem Synchronisierungskonto auf ein neues Synchronisierungskonto in Azure AD zurück.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Startet eine vollständige Synchronisation.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Setzt die Synchronisierung am letzten Wasserzeichen fort.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Ändert „AADConnectProvisioningAgent.exe.config“, um die ausführliche Ablaufverfolgung zu aktivieren, und startet den Dienst AADConnectProvisioningAgent neu. Sie können den Neustart des Diensts zwar mit „-SkipServiceRestart“ verhindern, dann werden jedoch die Konfigurationsänderungen nicht übernommen.  Diese Überwachungsprotokolle finden Sie im Ordner C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Ändert „AADConnectProvisioningAgent.exe.config“, um die ausführliche Ablaufverfolgung zu deaktivieren, und startet den Dienst AADConnectProvisioningAgent neu. Sie können den Neustart des Diensts zwar mit „-SkipServiceRestart“ verhindern, dann werden jedoch die Konfigurationsänderungen nicht übernommen.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Unterbricht die Synchronisierung.

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)

