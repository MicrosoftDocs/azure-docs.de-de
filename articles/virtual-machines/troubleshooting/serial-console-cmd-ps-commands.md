---
title: CMD und PowerShell in Azure-VMs unter Windows | Microsoft-Dokumentation
description: Verwenden von CMD- und PowerShell-Befehlen in SAC in Azure-VMs unter Windows
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 55b7e45bb9e600267e1dad0e36e9a97eca9a7d40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60306882"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-Befehle – CMD und PowerShell

Dieser Abschnitt enthält Beispielbefehle für allgemeine Aufgaben in Szenarios, in denen Sie ggf. über die spezielle Verwaltungskonsole (Special Administration Console, SAC) auf den virtuellen Windows-Computer zugreifen müssen, um beispielsweise Probleme mit RDP-Verbindungsfehlern zu behandeln.

SAC ist seit Windows Server 2003 in allen Windows-Versionen enthalten, standardmäßig aber deaktiviert. SAC basiert auf dem Kernel-Treiber `sacdrv.sys`, dem Dienst `Special Administration Console Helper` (`sacsvr`) und dem Prozess `sacsess.exe`. Weitere Informationen finden Sie unter [Emergency Management Services Tools and Settings](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)) (Tools und Einstellungen für die Notverwaltungsdienste).

Mit SAC können Sie über den seriellen Port eine Verbindung mit dem aktiven Betriebssystem herstellen. Wenn Sie CMD über SAC starten, wird `cmd.exe` von `sacsess.exe` innerhalb Ihres aktiven Betriebssystems gestartet. Dies ist im Task-Manager zu sehen, wenn Sie eine RDP-Verbindung mit Ihrem virtuellen Computer herstellen, während über die serielle Konsole eine Verbindung mit SAC besteht. Bei der CMD-Instanz, auf die Sie über SAC zugreifen, handelt es sich um die Instanz von `cmd.exe`, die Sie auch verwenden, wenn Sie über RDP eine Verbindung herstellen. Ihnen stehen die gleichen Befehle und Tools zur Verfügung – einschließlich der Möglichkeit, PowerShell über diese CMD-Instanz zu starten. Dies ist ein entscheidender Unterschied zwischen SAC und der Windows-Wiederherstellungsumgebung (Windows Recovery Environment, WinRE), da Sie mit SAC Ihr aktives Betriebssystem verwalten können. Im Gegensatz dazu startet WinRE mit einem anderen, minimalen Betriebssystem. Virtuelle Azure-Computer unterstützen zwar den Zugriff auf WinRE nicht, können mit der seriellen Konsole aber über SAC verwaltet werden.

Da SAC auf einen 80x24-Bildschirmpuffer ohne Scrollback beschränkt ist, sollten Sie Befehle mit `| more` versehen, damit die Ausgabe seitenweise erfolgt. Verwenden Sie `<spacebar>`, um die nächste Seite anzuzeigen, oder `<enter>`, um die nächste Zeile anzuzeigen.  

`SHIFT+INSERT` ist im Fenster der seriellen Konsole die Tastenkombination für „Einfügen“.

Aufgrund des eingeschränkten Bildschirmpuffers von SAC empfiehlt es sich bei der Eingabe längerer Befehle, diese in einen lokalen Text-Editor einzugeben und sie anschließend in SAC einzufügen.

## <a name="view-and-edit-windows-registry-settings"></a>Anzeigen und Bearbeiten von Windows-Registrierungseinstellungen
### <a name="verify-rdp-is-enabled"></a>Überprüfen, ob RDP aktiviert ist
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Der zweite Schlüssel (unter „\Policies“) ist nur vorhanden, wenn die relevante Gruppenrichtlinieneinstellung konfiguriert ist.

### <a name="enable-rdp"></a>Aktivieren von RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

Der zweite Schlüssel (unter „\Policies“) ist nur erforderlich, wenn die relevante Gruppenrichtlinieneinstellung konfiguriert wurde. Der Wert wird bei der nächsten Gruppenrichtlinienaktualisierung umgeschrieben, sofern dies in der Gruppenrichtlinie konfiguriert ist.

## <a name="manage-windows-services"></a>Verwalten von Windows-Diensten

### <a name="view-service-state"></a>Anzeigen des Dienststatus
`sc query termservice`
###  <a name="view-service-logon-account"></a>Anzeigen des Anmeldekontos für den Dienst
`sc qc termservice`
### <a name="set-service-logon-account"></a>Festlegen des Anmeldekontos für den Dienst 
`sc config termservice obj= "NT Authority\NetworkService"`

Nach dem Gleichheitszeichen muss sich ein Leerzeichen befinden.
### <a name="set-service-start-type"></a>Festlegen des Starttyps für den Dienst
`sc config termservice start= demand` 

Nach dem Gleichheitszeichen muss sich ein Leerzeichen befinden. Mögliche Startwerte: `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`
### <a name="set-service-dependencies"></a>Festlegen von Dienstabhängigkeiten
`sc config termservice depend= RPCSS`

Nach dem Gleichheitszeichen muss sich ein Leerzeichen befinden.
### <a name="start-service"></a>Starten des Diensts
`net start termservice`

oder

`sc start termservice`
### <a name="stop-service"></a>Beenden des Diensts
`net stop termservice`

oder

`sc stop termservice`
## <a name="manage-networking-features"></a>Verwalten von Netzwerkfeatures
### <a name="show-nic-properties"></a>Anzeigen der NIC-Eigenschaften
`netsh interface show interface` 
### <a name="show-ip-properties"></a>Anzeigen der IP-Eigenschaften
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Anzeigen der IPsec-Konfiguration
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Aktivieren der NIC
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Konfigurieren der NIC für die DHCP-Verwendung
`netsh interface ip set address name="<interface name>" source=dhcp`

Für weitere Informationen `netsh`[klicken Sie hier](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Virtuelle Azure-Computer müssen im Gastbetriebssystem immer so konfiguriert werden, dass sie eine IP-Adresse über DHCP beziehen. DHCP wird auch bei Verwendung der Azure-Einstellung für statische IP-Adressen verwendet, um die statische IP-Adresse für den virtuellen Computer zu vergeben.
### <a name="ping"></a>Pingen
`ping 8.8.8.8` 
### <a name="port-ping"></a>Pingen eines Ports  
Installieren des Telnet-Clients

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testen der Konnektivität

`telnet bing.com 80`

Entfernen des Telnet-Clients

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Wenn Sie auf die standardmäßig in Windows verfügbaren Methoden beschränkt sind, empfiehlt es sich ggf., die Portkonnektivität mithilfe von PowerShell zu testen. Entsprechende Beispiele finden Sie weiter unten im PowerShell-Abschnitt.
### <a name="test-dns-name-resolution"></a>Testen der DNS-Namensauflösung
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Anzeigen einer Windows-Firewallregel
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Deaktivieren der Windows-Firewall
`netsh advfirewall set allprofiles state off`

Mit diesem Befehl können Sie die Windows-Firewall bei der Problembehandlung vorübergehend deaktivieren. Sie wird beim nächsten Neustart wieder aktiviert (oder nach Verwendung des im Anschluss angegebenen Befehls). Beenden Sie nicht den Windows-Firewalldienst (MPSSVC) oder den BFE-Dienst (Base Filtering Engine, Basisfiltermodul), um die Windows-Firewall zu deaktivieren. Wenn Sie MPSSVC oder BFE beenden, werden sämtliche Verbindungen blockiert.
### <a name="enable-windows-firewall"></a>Aktivieren der Windows-Firewall
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Verwalten von Benutzern und Gruppen
### <a name="create-local-user-account"></a>Erstellen eines lokalen Benutzerkontos
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Hinzufügen eines lokalen Benutzers zu einer lokalen Gruppe
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Überprüfen, ob ein Benutzerkonto aktiviert ist
`net user <username> | find /i "active"`

Bei virtuellen Azure-Computern, die auf der Grundlage eines generalisierten Images erstellt wurden, wird der Name des lokalen Administratorkontos auf den Namen festgelegt, der im Rahmen der VM-Bereitstellung angegeben wurde. Er lautet also in der Regel nicht `Administrator`.
### <a name="enable-user-account"></a>Aktivieren eines Benutzerkontos
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Anzeigen der Eigenschaften des Benutzerkontos
`net user <username>`

Beispiele für interessante Zeilen aus einem lokalen Administratorkonto:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Anzeigen lokaler Gruppen
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Verwalten des Windows-Ereignisprotokolls
### <a name="query-event-log-errors"></a>Abfragen von Ereignisprotokollfehlern
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Geben Sie für `/c:10` die Anzahl zurückzugebender Ereignisse an, oder entfernen Sie die Angabe, um alle dem Filter entsprechenden Ereignisse zurückzugeben.
### <a name="query-event-log-by-event-id"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID und Anbieter
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID und Anbieter für die letzten 24 Stunden
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Verwenden Sie `604800000`, um den Zeitraum von 24 Stunden auf sieben Tage zu verlängern.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID, Anbieter und Ereignisdaten in den letzten sieben Tagen
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Anzeigen oder Entfernen installierter Anwendungen
### <a name="list-installed-applications"></a>Auflisten der installierten Anwendungen
`wmic product get Name,InstallDate | sort /r | more`

Durch `sort /r` werden die Elemente absteigend nach Installationsdatum sortiert. So können Sie ganz einfach erkennen, was zuletzt installiert wurde. Verwenden Sie `<spacebar>`, um die nächste Seite der Ausgabe anzuzeigen, oder `<enter>`, um die nächste Zeile anzuzeigen.
### <a name="uninstall-an-application"></a>Deinstallieren einer Anwendung
`wmic path win32_product where name="<name>" call uninstall`

Ersetzen Sie `<name>` durch den Namen, der im obigen Befehl für die Anwendung zurückgegeben wurde, die Sie entfernen möchten.

## <a name="file-system-management"></a>Dateisystemverwaltung
### <a name="get-file-version"></a>Abrufen der Dateiversion
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Dieses Beispiel gibt die Dateiversion des virtuellen NIC-Treibers zurück (je nach Windows-Version entweder „netvsc.sys“, „netvsc63.sys“ oder „netvsc60.sys“).
### <a name="scan-for-system-file-corruption"></a>Überprüfen des Dateisystems auf Beschädigungen
`sfc /scannow`

Siehe auch: [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Reparieren eines Windows-Images)
### <a name="scan-for-system-file-corruption"></a>Überprüfen des Dateisystems auf Beschädigungen
`dism /online /cleanup-image /scanhealth`

Siehe auch: [Repair a Windows Image](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image) (Reparieren eines Windows-Images)
### <a name="export-file-permissions-to-text-file"></a>Exportieren von Dateiberechtigungen in eine Textdatei
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Speichern von Dateiberechtigungen in einer ACL-Datei
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Wiederherstellen von Dateiberechtigungen aus einer ACL-Datei
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Bei Verwendung von `/restore` muss als Pfad der übergeordnete Ordner des Ordners angegeben werden, den Sie bei Verwendung von `/save` angegeben haben. In diesem Beispiel ist `\RSA` der übergeordnete Ordner des Ordners `\MachineKeys` aus dem obigen Beispiel für `/save`.
### <a name="take-ntfs-ownership-of-a-folder"></a>Übernehmen des NTFS-Besitzes eines Ordners
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Rekursives Gewähren von NTFS-Berechtigungen für einen Ordner
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Verwalten von Geräten
### <a name="remove-non-present-pnp-devices"></a>Entfernen nicht vorhandener Plug & Play-Geräte
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Verwalten von Gruppenrichtlinien
### <a name="force-group-policy-update"></a>Erzwingen einer Gruppenrichtlinienaktualisierung
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Sonstige Aufgaben
### <a name="show-os-version"></a>Anzeigen der Betriebssystemversion
`ver`

oder 

`wmic os get caption,version,buildnumber /format:list`

oder 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Anzeigen des Installationsdatums des Betriebssystems
`systeminfo | find /i "original"`

oder 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Anzeigen des letzten Systemstarts
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Anzeigen der Zeitzone
`systeminfo | find /i "time zone"`

oder

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Neustarten von Windows
`shutdown /r /t 0`

Durch Hinzufügen von `/f` werden aktive Anwendungen ohne Vorwarnung geschlossen.
### <a name="detect-safe-mode-boot"></a>Erkennen des Starts im abgesicherten Modus
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Windows-Befehle – PowerShell

Wenn Sie PowerShell in SAC ausführen möchten, geben Sie an einer CMD-Eingabeaufforderung Folgendes ein:

`powershell <enter>`

> [!CAUTION]
> Entfernen Sie zunächst das PSReadLine-Modul aus der PowerShell-Sitzung, bevor Sie andere PowerShell-Befehle ausführen. Es gibt ein bekanntes Problem, durch das beim Einfügen von Text aus der Zwischenablage unter Umständen zusätzliche Zeichen eingefügt werden, wenn „PSReadLine“ in einer PowerShell-Sitzung in SAC ausgeführt wird.

Überprüfen Sie zunächst, ob „PSReadLine“ geladen ist. Dies ist unter Windows Server 2016 sowie unter Windows 10 und höheren Versionen von Windows standardmäßig der Fall. Bei älteren Windows-Versionen ist das Modul nur vorhanden, wenn es manuell installiert wurde. 

Wenn dieser Befehl nichts zurückgibt, war das Modul nicht geladen, und Sie können die PowerShell-Sitzung in SAC wie gewohnt verwenden.

`get-module psreadline`

Falls der obige Befehl die Version des PSReadLine-Moduls zurückgibt, führen Sie den folgenden Befehl aus, um es zu entladen. Durch diesen Befehl wird das Modul nicht gelöscht oder deinstalliert, sondern lediglich aus der aktuellen PowerShell-Sitzung entladen.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Anzeigen und Bearbeiten von Windows-Registrierungseinstellungen
### <a name="verify-rdp-is-enabled"></a>Überprüfen, ob RDP aktiviert ist
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Der zweite Schlüssel (unter „\Policies“) ist nur vorhanden, wenn die relevante Gruppenrichtlinieneinstellung konfiguriert ist.
### <a name="enable-rdp"></a>Aktivieren von RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Der zweite Schlüssel (unter „\Policies“) ist nur erforderlich, wenn die relevante Gruppenrichtlinieneinstellung konfiguriert wurde. Der Wert wird bei der nächsten Gruppenrichtlinienaktualisierung umgeschrieben, sofern dies in der Gruppenrichtlinie konfiguriert ist.
## <a name="manage-windows-services"></a>Verwalten von Windows-Diensten
### <a name="view-service-details"></a>Anzeigen von Dienstdetails
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` kann verwendet werden, beinhaltet aber das Dienstanmeldekonto nicht. `Get-WmiObject win32-service` dagegen schon.
### <a name="set-service-logon-account"></a>Festlegen des Anmeldekontos für den Dienst
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Geben Sie bei Verwendung eines Dienstkontos, bei dem es sich nicht um `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService` oder `LocalSystem` handelt, das Kontokennwort als letztes (achtes) Argument nach dem Kontonamen ein.
### <a name="set-service-startup-type"></a>Festlegen des Starttyps des Diensts
`set-service termservice -startuptype Manual`

`Set-service` akzeptiert `Automatic`, `Manual` oder `Disabled` als Starttyp.
### <a name="set-service-dependencies"></a>Festlegen von Dienstabhängigkeiten
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Starten des Diensts
`start-service termservice`
### <a name="stop-service"></a>Beenden des Diensts
`stop-service termservice`
## <a name="manage-networking-features"></a>Verwalten von Netzwerkfeatures
### <a name="show-nic-properties"></a>Anzeigen der NIC-Eigenschaften
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

oder 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden.
### <a name="show-ip-properties"></a>Anzeigen der IP-Eigenschaften
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Aktivieren der NIC
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

oder

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden.
### <a name="set-nic-to-use-dhcp"></a>Konfigurieren der NIC für die DHCP-Verwendung
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden. Virtuelle Azure-Computer müssen im Gastbetriebssystem immer so konfiguriert werden, dass sie eine IP-Adresse über DHCP beziehen. DHCP wird auch bei Verwendung der Azure-Einstellung für statische IP-Adressen verwendet, um die IP-Adresse für den virtuellen Computer zu vergeben.
### <a name="ping"></a>Pingen
`test-netconnection`

oder

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

Bei Verwendung von `Test-Netconnection` ohne Parameter wird versucht, `internetbeacon.msedge.net` zu pingen. Diese Option ist ab 2012 verfügbar. Für 2008R2 muss wie im zweiten Beispiel `Get-WmiObject` verwendet werden.
### <a name="port-ping"></a>Pingen eines Ports
`test-netconnection -ComputerName bing.com -Port 80`

oder

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` ist ab 2012 verfügbar. Für 2008R2 muss `Net.Sockets.TcpClient` verwendet werden.
### <a name="test-dns-name-resolution"></a>Testen der DNS-Namensauflösung
`resolve-dnsname bing.com` 

oder 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` ist ab 2012 verfügbar. Für 2008R2 muss `System.Net.DNS` verwendet werden.
### <a name="show-windows-firewall-rule-by-name"></a>Anzeigen einer Windows-Firewallregel nach Name
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Anzeigen einer Windows-Firewallregel nach Port
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

oder

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` ist ab 2012 verfügbar. Für 2008R2 muss das COM-Objekt `hnetcfg.fwpolicy2` verwendet werden. 
### <a name="disable-windows-firewall"></a>Deaktivieren der Windows-Firewall
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` ist ab 2012 verfügbar. Für 2008R2 muss `netsh advfirewall` wie im obigen CMD-Abschnitt angegeben verwendet werden.
## <a name="manage-users-and-groups"></a>Verwalten von Benutzern und Gruppen
### <a name="create-local-user-account"></a>Erstellen eines lokalen Benutzerkontos
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Überprüfen, ob ein Benutzerkonto aktiviert ist
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

oder 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden. Dieses Beispiel zeigt das integrierte lokale Administratorkonto, welches immer die SID `S-1-5-21-*-500` besitzt. Bei virtuellen Azure-Computern, die auf der Grundlage eines generalisierten Images erstellt wurden, wird der Name des lokalen Administratorkontos auf den Namen festgelegt, der im Rahmen der VM-Bereitstellung angegeben wurde. Er lautet also in der Regel nicht `Administrator`.
### <a name="add-local-user-to-local-group"></a>Hinzufügen eines lokalen Benutzers zu einer lokalen Gruppe
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Aktivieren eines lokalen Benutzerkontos
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Dieses Beispiel aktiviert das integrierte lokale Administratorkonto, welches immer die SID `S-1-5-21-*-500` besitzt. Bei virtuellen Azure-Computern, die auf der Grundlage eines generalisierten Images erstellt wurden, wird der Name des lokalen Administratorkontos auf den Namen festgelegt, der im Rahmen der VM-Bereitstellung angegeben wurde. Er lautet also in der Regel nicht `Administrator`.
### <a name="view-user-account-properties"></a>Anzeigen der Eigenschaften des Benutzerkontos
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

oder 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden. Dieses Beispiel zeigt das integrierte lokale Administratorkonto, welches immer die SID `S-1-5-21-*-500` besitzt.
### <a name="view-local-groups"></a>Anzeigen lokaler Gruppen
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` ist ab 2012 verfügbar. Für 2008R2 muss `Get-WmiObject` verwendet werden.
## <a name="manage-the-windows-event-log"></a>Verwalten des Windows-Ereignisprotokolls
### <a name="query-event-log-errors"></a>Abfragen von Ereignisprotokollfehlern
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Geben Sie für `/c:10` die Anzahl zurückzugebender Ereignisse an, oder entfernen Sie die Angabe, um alle dem Filter entsprechenden Ereignisse zurückzugeben.
### <a name="query-event-log-by-event-id"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID und Anbieter
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID und Anbieter für die letzten 24 Stunden
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Verwenden Sie `604800000`, um den Zeitraum von 24 Stunden auf sieben Tage zu verlängern. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Abfragen des Ereignisprotokolls nach Ereignis-ID, Anbieter und Ereignisdaten in den letzten sieben Tagen
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Anzeigen oder Entfernen installierter Anwendungen
### <a name="list-installed-software"></a>Auflisten der installierten Software
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Deinstallieren von Software
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Dateisystemverwaltung
### <a name="get-file-version"></a>Abrufen der Dateiversion
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Dieses Beispiel gibt die Dateiversion des virtuellen NIC-Treibers zurück, der je nach Windows-Version entweder „netvsc.sys“, „netvsc63.sys“ oder „netvsc60.sys“ heißt.
### <a name="download-and-extract-file"></a>Herunterladen und Extrahieren einer Datei
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Dieses Beispiel erstellt den Ordner `c:\bin`, lädt die Sysinternals-Tools herunter, und extrahiert sie in `c:\bin`.
## <a name="miscellaneous-tasks"></a>Sonstige Aufgaben
### <a name="show-os-version"></a>Anzeigen der Betriebssystemversion
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Anzeigen des Installationsdatums des Betriebssystems
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Anzeigen des letzten Systemstarts
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Anzeigen der Betriebszeit von Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Gibt die Betriebszeit als `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` zurück (beispielsweise `49:16:48:00.00`). 
### <a name="restart-windows"></a>Neustarten von Windows
`restart-computer`

Durch Hinzufügen von `-force` werden aktive Anwendungen ohne Vorwarnung geschlossen.
## <a name="instance-metadata"></a>Instanzmetadaten

Sie können Azure-Instanzmetadaten aus Ihrem virtuellen Azure-Computer abfragen, um Details wie Betriebssystemtyp (osType), Standort (location), VM-Größe (vmSize), VM-ID (vmId), Name (name), Ressourcengruppenname (resourceGroupName), Abonnement-ID (subscriptionId), private IP-Adresse (privateIpAddress) und öffentliche IP-Adresse (publicIpAddress) anzuzeigen.

Voraussetzung für das Abfragen von Instanzmetadaten ist eine ordnungsgemäß funktionierende Gastnetzwerkverbindung, da hierzu ein REST-Aufruf über den Azure-Host an den Instanzmetadatendienst gerichtet werden muss. Wenn Sie also Instanzmetadaten abfragen können, kann der Gast über das Netzwerk mit einem von Azure gehosteten Dienst kommunizieren.

Weitere Informationen finden Sie unter [Azure-Instanzmetadatendienst](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Instanzmetadaten
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Betriebssystemtyp (Instanzmetadaten)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Standort (Instanzmetadaten)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Größe (Instanzmetadaten)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-ID (Instanzmetadaten)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM-Name (Instanzmetadaten)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Ressourcengruppenname (Instanzmetadaten)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Abonnement-ID (Instanzmetadaten)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tags (Instanzmetadaten)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Platzierungsgruppen-ID (Instanzmetadaten)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Fehlerdomäne der Plattform (Instanzmetadaten)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Updatedomäne der Plattform (Instanzmetadaten)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Private IPv4-IP-Adresse (Instanzmetadaten)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Öffentliche IPv4-IP-Adresse (Instanzmetadaten)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-Subnetzadresse/Prefix (Instanzmetadaten)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6-IP-Adresse (Instanzmetadaten)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-Adresse (Instanzmetadaten)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Nächste Schritte
* Sie finden die Windows-Dokumentationsseite für die serielle Hauptkonsole [hier](serial-console-windows.md).
* Die serielle Konsole ist auch für [virtuelle Linux-Computer](serial-console-linux.md) verfügbar.
* Erfahren Sie mehr zur [Startdiagnose](boot-diagnostics.md).
