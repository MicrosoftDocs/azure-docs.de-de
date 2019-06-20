---
title: Vorbereiten einer Windows-VHD für das Hochladen in Azure | Microsoft-Dokumentation
description: Vorbereiten einer Windows-VHD oder -VHDX vor dem Hochladen in Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 5ae0e7855db6bec9f48d2b9511f0d0626d883111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65561342"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure

Bevor Sie einen virtuellen Windows-Computer aus einem lokalen Speicherort in Microsoft Azure hochladen können, müssen Sie die virtuelle Festplatte (Virtual Hard Disk, VHD oder VHDX) vorbereiten. Azure unterstützt VMs der Generation 1 und 2, die das VHD-Dateiformat und einen Datenträger mit fester Größe aufweisen. Die maximal zulässige Größe für die virtuelle Festplatte beträgt 1.023 GB. Sie können virtuelle Computer der 1. Generation vom VHDX- in das VHD-Dateisystemformat und von einem dynamisch erweiterbaren Datenträger in einen Datenträger mit fester Größe konvertieren. Aber die Generation eines virtuellen Computers kann nicht geändert werden. Weitere Informationen finden Sie unter [Sollte ich eine VM der Generation 1 oder 2 in Hyper-V erstellen?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) und [VMs der Generation 2 in Azure](generation-2.md).

Weitere Informationen zur Supportrichtlinie für Azure-VMs finden Sie unter [Microsoft-Server-Software-Support für virtuelle Maschinen von Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> Die Anweisungen in diesem Artikel gelten für die 64-Bit-Version und Windows Server 2008 R2 und höhere Versionen des Windows-Serverbetriebssystems. Informationen zum Ausführen einer 32-Bit-Version des Betriebssystems in Azure finden Sie unter [Unterstützung für 32-Bit-Betriebssysteme in Azure-VMs](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>Konvertieren des virtuellen Datenträgers in VHD und einen Datenträger mit fester Größe 
Wenn Sie Ihren virtuellen Datenträger in das für Azure erforderliche Format konvertieren müssen, verwenden Sie eine der in diesem Abschnitt beschriebenen Methoden. Sichern Sie den virtuellen Computer, bevor Sie die Konvertierung des virtuellen Datenträgers ausführen, und stellen Sie sicher, dass die Windows-VHD auf dem lokalen Server ordnungsgemäß funktioniert. Beheben Sie alle Probleme auf dem virtuellen Computer selbst, bevor Sie versuchen, ihn zu konvertieren oder in Azure hochzuladen.

Nachdem Sie den Datenträger konvertiert haben, erstellen Sie einen virtuellen Computer, der den konvertierten Datenträger verwendet. Starten Sie, und melden Sie sich bei dem virtuellen Computer an, um die Vorbereitung des virtuellen Computers für den Upload abzuschließen.

### <a name="convert-disk-using-hyper-v-manager"></a>Konvertieren eines Datenträgers mithilfe des Hyper-V-Managers
1. Öffnen Sie den Hyper-V-Manager, und wählen Sie auf der linken Seite Ihren lokalen Computer aus. Klicken Sie im Menü über der Computerliste auf **Aktion** > **Datenträger bearbeiten**.
2. Navigieren Sie auf dem Bildschirm **Virtuelle Festplatte suchen** zu Ihrem virtuellen Datenträger, und wählen Sie ihn aus.
3. Wählen Sie auf dem Bildschirm **Aktion auswählen** die Option **Konvertieren** und **Weiter**.
4. Wenn Sie eine VHDX konvertieren müssen, wählen Sie **VHD** aus. Klicken Sie anschließend auf **Weiter**.
5. Wenn Sie einen dynamisch erweiterbaren Datenträger konvertieren müssen, wählen Sie **Feste Größe** aus, und klicken Sie dann auf **Weiter**.
6. Navigieren Sie zu einem Pfad zum Speichern der neuen VHD-Datei, und wählen Sie ihn aus.
7. Klicken Sie auf **Fertig stellen**.

>[!NOTE]
>Die Befehle in diesem Artikel müssen in einer PowerShell-Sitzung mit erhöhten Rechten ausgeführt werden.

### <a name="convert-disk-by-using-powershell"></a>Konvertieren eines Datenträgers mithilfe von PowerShell
Sie können einen virtuellen Datenträger mithilfe des Befehls [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) in Windows PowerShell konvertieren. Wählen Sie beim Starten von PowerShell **Als Administrator ausführen**. 

Im folgenden Beispiel wird der Vorgang zum Konvertieren von VHDX zu VHD und von einem dynamisch erweiterbaren Datenträger zu einem Datenträger fester Größe veranschaulicht:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Ersetzen Sie in diesem Befehl die Werte für „-Path“ durch den Pfad zu der virtuellen Festplatte, die Sie konvertieren möchten, und „-DestinationPath“ durch den neuen Pfad und den Namen für den konvertierten Datenträger.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertieren des VMware-VMDK-Datenträgerformats
Wenn Sie über ein Windows-VM-Image im [VMDK-Dateiformat](https://en.wikipedia.org/wiki/VMDK) verfügen, konvertieren Sie es mit dem [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497) in eine virtuelle Festplatte. Weitere Informationen finden Sie im Blog [How to Convert a VMware VMDK to Hyper-V VHD](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Konvertieren von VMware-VMDK in Hyper-V-VHD).

## <a name="set-windows-configurations-for-azure"></a>Festlegen der Windows-Konfigurationen für Azure

Führen Sie alle in den folgenden Schritten genannten Befehle über eine [Eingabeaufforderung mit erhöhten Rechten](https://technet.microsoft.com/library/cc947813.aspx) auf dem virtuellen Computer aus, den Sie in Azure hochladen möchten:

1. Entfernen Sie alle statischen persistenten Routen aus der Routingtabelle:
   
   * Führen Sie zum Anzeigen der Routingtabelle `route print` über die Eingabeaufforderung aus.
   * Überprüfen Sie die Abschnitte mit **Persistenzrouten** . Wenn eine persistente Route vorhanden ist, verwenden Sie den Befehl **route delete**, um die Route zu entfernen.
2. Entfernen Sie den WinHTTP-Proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Wenn der virtuelle Computer mit einem bestimmten Proxy arbeiten muss, müssen Sie der Azure-IP-Adresse eine Proxyausnahme hinzufügen ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), sodass der virtuelle Computer mit Azure verbunden ist:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Legen Sie für die Datenträger-SAN-Richtlinie [Onlineall](https://technet.microsoft.com/library/gg252636.aspx) fest:
   
    ```PowerShell
    diskpart 
    ```
    Führen Sie im geöffneten Eingabeaufforderungsfenster die folgenden Befehle aus:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Legen Sie die koordinierte Weltzeit (UTC, Coordinated Universal Time) für Windows fest und als Starttyp des Windows-Zeitdiensts (w32time) **Automatisch**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Legen Sie das Energieprofil auf **Hohe Leistung** fest:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Stellen Sie sicher, dass die Umgebungsvariablen **TEMP** und **TMP** auf ihre Standardwerte festgelegt sind:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Überprüfen der Windows-Dienste
Stellen Sie sicher, dass jeder der folgenden Windows-Dienste auf die **Windows-Standardwerte** festgelegt ist. Dies ist die Mindestanzahl an Diensten, die festgelegt werden muss, um Konnektivität für die VM sicherzustellen. Um die Starteinstellungen zurückzusetzen, führen Sie die folgenden Befehle aus:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualisieren der Remotedesktop-Registrierungseinstellungen
Stellen Sie sicher, dass die folgenden Einstellungen für die Remotedesktopverbindung ordnungsgemäß konfiguriert sind:

>[!Note] 
>Sie erhalten möglicherweise eine Fehlermeldung, wenn Sie in diesen Schritten den **Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;Objektname&gt; -value &lt;Wert&gt;** ausführen. Die Fehlermeldung kann problemlos ignoriert werden. Sie gibt lediglich an, dass diese Konfiguration von der Domäne nicht über ein Gruppenrichtlinienobjekt per Push übertragen wird.
>
>

1. Das Remotedesktopprotokoll (RDP) ist aktiviert:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Der RDP-Port ist ordnungsgemäß festgelegt (Standardport: 3389):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Wenn Sie eine VM bereitstellen, werden die Standardregeln für Port 3389 erstellt. Wenn Sie die Portnummer ändern möchten, können Sie dies nach der Bereitstellung der VM in Azure tun.

3. Der Listener lauscht auf allen Netzwerkschnittstellen:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Konfigurieren Sie den Modus zur Authentifizierung auf Netzwerkebene für die RDP-Verbindungen:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Legen Sie den Keep-Alive-Wert fest:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Erneute Verbindungsherstellung:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Beschränken Sie die Anzahl gleichzeitiger Verbindungen:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Wenn selbstsignierte Zertifikate vorhanden sind, die an den RDP-Listener gebunden sind, entfernen Sie diese:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    So wird sichergestellt, dass Sie zu Beginn der Bereitstellung der VM eine Verbindung herstellen können. Sie können dies ggf. auch später überprüfen, nachdem die VM in Azure bereitgestellt wurde.

9. Wenn die VM Teil einer Domäne sein wird, überprüfen Sie sämtliche der folgenden Einstellungen, um sicherzustellen, dass die vorherigen Einstellungen nicht zurückgesetzt werden. Folgende Richtlinien müssen überprüft werden:
    
    | Zielsetzung                                     | Richtlinie                                                                                                                                                       | Wert                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP ist aktiviert                           | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Verbindungen         | Remoteverbindungen für Benutzer mithilfe der Remotedesktopdienste zulassen                                  |
    | NLA-Gruppenrichtlinie                         | Einstellungen\Administrative Vorlagen\Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Sicherheit                                                    | Benutzerauthentifizierung mit Authentifizierung auf Netzwerkebene ist für Remoteverbindungen erforderlich |
    | Keep-Alive-Einstellungen                      | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Windows-Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Verbindungen | Keep-Alive-Verbindungsintervall konfigurieren                                                 |
    | Einstellungen zur erneuten Verbindungsherstellung                       | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Windows-Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Verbindungen | Automatisch erneut verbinden                                                                   |
    | Einstellung zum Beschränken der Anzahl von Verbindungen | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Windows-Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Verbindungen | Anzahl der Verbindungen einschränken                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurieren von Windows-Firewallregeln
1. Aktivieren Sie die Windows-Firewall für die drei Profile („Domäne“, „Standard“ und „Öffentlich“):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Führen Sie den folgenden Befehl in PowerShell aus, um WinRM in den drei Firewallprofilen („Domäne“, „Privat“ und „Öffentlich“) zuzulassen, und aktivieren Sie den PowerShell-Remotedienst:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Aktivieren Sie die folgenden Firewallregeln, um RDP-Datenverkehr zuzulassen:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Aktivieren Sie die Regel für die Datei- und Druckerfreigabe, damit die VM auf einen Ping-Befehl innerhalb des virtuellen Netzwerks antworten kann:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Wenn die VM Teil einer Domäne sein wird, überprüfen Sie sämtliche der folgenden Einstellungen, um sicherzustellen, dass die vorherigen Einstellungen nicht zurückgesetzt werden. Folgende AD-Richtlinien müssen überprüft werden:

    | Zielsetzung                                 | Richtlinie                                                                                                                                                  | Wert                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Aktivieren der Windows-Firewallprofile | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Netzwerk\Netzwerkverbindung\Windows-Firewall\Domänenprofil\Windows-Firewall   | Schützen aller Netzwerkverbindungen         |
    | Aktivieren von RDP                           | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Netzwerk\Netzwerkverbindung\Windows-Firewall\Domänenprofil\Windows-Firewall   | Zulassen eingehender Remotedesktopausnahmen |
    |                                      | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Netzwerk\Netzwerkverbindung\Windows-Firewall\Standardprofil\Windows-Firewall | Zulassen eingehender Remotedesktopausnahmen |
    | Aktivieren von ICMP-V4                       | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Netzwerk\Netzwerkverbindung\Windows-Firewall\Domänenprofil\Windows-Firewall   | Zulassen von ICMP-Ausnahmen                   |
    |                                      | Computerkonfiguration\Richtlinien\Windows-Einstellungen\Administrative Vorlagen\Netzwerk\Netzwerkverbindung\Windows-Firewall\Standardprofil\Windows-Firewall | Zulassen von ICMP-Ausnahmen                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Sicherstellen, dass der virtuelle Computer fehlerfrei, sicher und der Zugriff mit RDP darauf möglich ist 
1. Um sicherzustellen, dass der Datenträger fehlerfrei und konsistent ist, führen Sie beim nächsten VM-Neustart eine Datenträgerüberprüfung durch:

    ```PowerShell
    Chkdsk /f
    ```
    Stellen Sie sicher, dass der Bericht einen sauberen und fehlerfreien Datenträger anzeigt.

2. Legen Sie die Einstellungen für die Startkonfigurationsdaten (Boot Configuration Data, BCD) fest. 

    > [!Note]
    > Stellen Sie sicher, dass Sie diese Befehle in einem PowerShell-Fenster mit erhöhten Rechten ausführen.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. Das Abbildprotokoll kann bei der Problembehandlung nach Windows-Abstürzen hilfreich sein. Aktivieren Sie die Sammlung von Abbildprotokollen:

    ```powershell
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Überprüfen Sie, ob das Repository für die Windows-Verwaltungsinstrumentation (Windows Management Instrumentation, WMI) konsistent ist. Führen Sie zu diesem Zweck den folgenden Befehl aus:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Wenn das Repository beschädigt ist, informieren Sie sich in [WMI: Repository beschädigt oder nicht](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Stellen Sie sicher, dass keine andere Anwendung Port 3389 verwendet. Dieser Port wird für den RDP-Dienst in Azure verwendet. Sie können **netstat -anob** ausführen, um anzuzeigen, welche Ports auf der VM verwendet werden:

    ```PowerShell
    netstat -anob
    ```

6. Wenn es sich bei der Windows-VHD, die Sie hochladen möchten, um einen Domänencontroller handelt, führen Sie diese Schritte aus:

    1. Führen Sie [diese zusätzlichen Schritte aus](https://support.microsoft.com/kb/2904015), um den Datenträger vorzubereiten.

    1. Stellen Sie sicher, dass Sie das DSRM-Kennwort kennen, falls Sie zu einem bestimmten Zeitpunk die VM in DSRM starten müssen. Dieser Link enthält Informationen dazu, wie Sie das [DSRM-Kennwort festlegen](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Stellen Sie sicher, dass Sie die Anmeldeinformationen für das integrierte Administratorkonto kennen. Setzen Sie ggf. das aktuelle Kennwort des lokalen Administrators zurück, und stellen Sie sicher, dass Sie sich mit diesem Konto über die RDP-Verbindung bei Windows anmelden können. Diese Zugriffsberechtigung wird über das Gruppenrichtlinienobjekt „Anmelden über Remotedesktopdienste zulassen“ gesteuert. Sie können dieses Objekt im folgenden Pfad im Editor für lokale Gruppenrichtlinien anzeigen:

    Computerkonfiguration\Windows-Einstellungen\Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten

8. Überprüfen Sie die folgenden AD-Richtlinien, um sich zu vergewissern, dass Sie den RDP-Zugriff weder über RDP noch aus dem Netzwerk blockieren:

    - Computerkonfiguration\Windows-Einstellungen\Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten\Zugriff vom Netzwerk auf diesen Computer verweigern

    - Computerkonfiguration\Windows-Einstellungen\Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten\Anmelden über Remotedesktopdienste verweigern


9. Überprüfen Sie die folgende AD-Richtlinie, um sicherzustellen, dass Sie keins der folgenden erforderlichen Zugriffskonten entfernen:

   - Computerkonfiguration\Windows-Einstellungen\Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten\Zugriff vom Netzwerk auf diesen Computer

     Für diese Richtlinie sollten die folgenden Gruppen aufgeführt sein:

   - Administratoren
   - Sicherungsoperatoren
   - Jeder
   - Benutzer

10. Starten Sie den virtuellen Computer neu, um sicherzustellen, dass Windows weiterhin fehlerfrei ausgeführt wird und über die RDP-Verbindung erreichbar ist. An diesem Punkt können Sie eine VM in Ihrer lokalen Hyper-V-Umgebung einrichten, um sicherzustellen, dass die VM vollständig startet. Anschließend können Sie testen, ob die VM über RDP erreichbar ist.

11. Entfernen Sie jegliche zusätzlichen Transport Driver Interface-Filter, beispielsweise Software zur Analyse von TCP-Paketen oder zusätzliche Firewalls. Sie können dies ggf. auch später überprüfen, nachdem die VM in Azure bereitgestellt wurde.

12. Deinstallieren Sie jegliche Drittanbietersoftware und -treiber im Zusammenhang mit physischen Komponenten oder einer anderen Virtualisierungstechnologie.

### <a name="install-windows-updates"></a>Installieren von Windows-Updates
Die ideale Konfiguration ist die, dass **mindestens die Patchebene des Computers vorliegt**. Wenn dies nicht möglich ist, stellen Sie sicher, dass die folgenden Updates installiert sind:

| Komponente               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 Version 1607 Windows Server 2016 Version 1607 | Windows 10, Version 1703    | Windows 10 1709, Windows Server 2016 Version 1709 | Windows 10 1803, Windows Server 2016 Version 1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 – KB3137061 | 6.3.9600.18203 – KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 – KB3018489 | 6.3.9600.18573 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | ntfs.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 – KB3121255 | 6.3.9600.18654 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 – KB3125574                | 6.2.9200.16384 – KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 – KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 – KB2995387 | 6.3.9600.18334 – KB3172614         | 10.0.14393.953 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 – KB2975331 | 6.3.9600.18265 – KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.16681 – KB2877114                  | 6.3.9600.17401 – KB3000850         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 – KB3125574                | 6.2.9200.21006 – KB2955163                  | 6.3.9600.18624 – KB4022726         | 10.0.14393.1066 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 – KB3125574                | 6.2.9200.21474 – KB3046101                  | 6.3.9600.18592 – KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.21190 – KB3046101                  | 6.3.9600.18616 – KB4022726         | 10.0.14393.1198 – KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 – KB4072650                | 6.3.9600.18080 – KB3063109                  | 6.3.9600.18907 – KB4072650         | 10.0.14393.2007 – KB4345418                             | 10.0.15063.850 – KB4345419 | 10.0.16299.371 – KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.18294 – KB3172614         | 10.0.14393.576 – KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 – KB3125574                | 6.2.9200.20930 – KB2930244                  | 6.3.9600.17415 – KB3172614         | 10.0.14393.206 – KB4022715                              | -                          | -                                               | -                                               |
| Netzwerk                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 – KB4022715                             | 10.0.15063.250 – KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.22108 – KB4022724                  | 6.3.9600.18603 – KB4022726         | 10.0.14393.479 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 – KB4022722                | 6.2.9200.21548 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 – KB4022722                | 6.2.9200.22074 – KB4022724                  | 6.3.9600.18586 – KB4022726         | 10.0.14393.953 – KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip.sys      | 6.1.7601.23761 – KB4022722                | 6.2.9200.22070 – KB4022724                  | 6.3.9600.18478 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 – KB3125574                | 6.2.9200.17285 – KB3042553                  | 6.3.9600.18574 – KB4022726         | 10.0.14393.251 – KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 – KB4022719                | 6.2.9200.22117 – KB4022724                  | 6.3.9600.18654 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 – KB4022719                | 6.2.9200.22170 – KB4022718                  | 6.3.9600.18696 – KB4022726         | 10.0.14393.1358 – KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Remotedesktopdienste | rdpcorets.dll  | 6.2.9200.21506 – KB4022719                | 6.2.9200.22104 – KB4022724                  | 6.3.9600.18619 – KB4022726         | 10.0.14393.1198 – KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 – KB3125574                | 6.2.9200.17048 – KB2973501                  | 6.3.9600.17415 – KB3000850         | 10.0.14393.0 – KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 – KB4022719                | 6.2.9200.22168 – KB4022718                  | 6.3.9600.18698 – KB4022726         | 10.0.14393.594 – KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 – KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Sicherheit                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Einsatz von Sysprep <a id="step23"></a>    

Sysprep ist ein Prozess, den Sie in einer Windows-Installation ausführen können, um die Systeminstallation zurückzusetzen. Dabei werden alle personenbezogenen Daten entfernt und verschiedene Komponenten zurückgesetzt. Sie verwenden Sysprep üblicherweise, wenn Sie eine Vorlage erstellen, aus der Sie verschiedene weitere VMs mit einer spezifischen Konfiguration bereitstellen können. Dies wird als **generalisiertes Image** bezeichnet.

Wenn Sie stattdessen nur eine VM aus einem Datenträger erstellen möchten, ist die Verwendung von Sysprep nicht erforderlich. In diesem Fall können Sie die VM einfach aus einem sogenannten **spezialisierten Image** erstellen.

Weitere Informationen zum Erstellen einer VM aus einem spezialisierten Datenträger finden Sie hier:

- [Erstellen eines virtuellen Computers von einem speziellen Datenträger](create-vm-specialized.md)
- [Create a VM from a specialized VHD disk](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master) (Erstellen einer VM aus einem spezialisierten VHD-Datenträger)

Wenn Sie ein generalisiertes Image erstellen möchten, müssen Sie Sysprep ausführen. Weitere Informationen zur Verwendung von Sysprep finden Sie unter [How to Use Sysprep: An Introduction](https://technet.microsoft.com/library/bb457073.aspx) (Verwenden von Sysprep: Eine Einführung). 

Nicht jede Rolle oder Anwendung, die auf einem Windows-basierten Computer installiert ist, unterstützt diese Generalisierung. Bevor Sie also dieses Verfahren ausführen, sollten Sie den folgenden Artikel lesen, um sicherzustellen, dass die Rolle des betreffenden Computers von Sysprep unterstützt wird. Weitere Informationen finden Sie unter [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles) (Sysprep-Unterstützung für Serverrollen).

### <a name="steps-to-generalize-a-vhd"></a>Schritte zum Generalisieren einer VHD

>[!NOTE]
> Nachdem Sie „sysprep.exe“ wie in den folgenden Schritten beschrieben ausgeführt haben, schalten Sie die VM aus und schalten Sie nicht wieder ein, bis Sie ein Image davon in Azure erstellen.

1. Melden Sie sich bei der Windows-VM an.
2. Öffnen Sie als Administrator eine **Eingabeaufforderung**. 
3. Wechseln Sie zum Verzeichnis **%windir%\system32\sysprep**, und führen Sie anschließend **sysprep.exe** aus.
3. Wählen Sie unter **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und vergewissern Sie sich, dass das Kontrollkästchen **Generalisieren** aktiviert ist.

    ![Tool für die Systemvorbereitung](media/prepare-for-upload-vhd-image/syspre.png)
4. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
5. Klicken Sie auf **OK**.
6. Fahren Sie die VM herunter, nachdem Sysprep abgeschlossen wurde. Verwenden Sie nicht **Neu starten**, um die VM herunterzufahren.
7. Jetzt kann die VHD hochgeladen werden. Weitere Informationen zum Erstellen einer VM aus einem generalisierten Datenträger finden Sie unter [Hochladen einer generalisierten VHD in Azure und Erstellen einer neuen VM](sa-upload-generalized.md).


>[!NOTE]
> Eine benutzerdefinierte Datei „unattend.xml“ wird nicht unterstützt. Wir unterstützen aber die „AdditionalUnattendContent“-Eigenschaft, die nur eingeschränkte Unterstützung für das Hinzufügen von [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup)-Optionen in die Datei „unattend.xml“ bietet, die der Azure-Bereitstellungs-Agent verwendet. Beispiel:  können sie [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) verwenden, um „FirstLogonCommands“ und „LogonCommands“ hinzuzufügen. Siehe auch [Beispiel für additionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-recommended-configurations"></a>Abschließen empfohlener Konfigurationen
Die folgenden Einstellungen wirken sich nicht auf das Hochladen von VHDs aus. Es wird jedoch dringend empfohlen, diese Einstellungen zu konfigurieren.

* Installieren Sie den [Azure-VM-Agent](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Anschließend können Sie VM-Erweiterungen aktivieren. Die VM-Erweiterungen implementieren die meisten der wichtigen Funktionen, die Sie für Ihre virtuellen Computer möglicherweise verwenden möchten, darunter das Zurücksetzen von Kennwörtern, das Konfigurieren von RDP und viele andere. Weitere Informationen finden Sie unter [Übersicht über den Agent für virtuelle Azure-Computer](../extensions/agent-windows.md).
* Nachdem die VM in Azure erstellt wurde, wird empfohlen, die Auslagerungsdatei auf dem Volume für das temporäre Laufwerk zu platzieren, um die Leistung zu verbessern. Die Einrichtung kann wie folgt durchgeführt werden:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Wenn der VM ein Datenträger angefügt ist, lautet der Laufwerkbuchstabe des Volumes für das temporäre Laufwerk typischerweise „D“. Die Bezeichnung kann abhängig von der Anzahl verfügbarer Laufwerke und den vorgenommenen Einstellungen jedoch abweichen.

## <a name="next-steps"></a>Nächste Schritte
* [Hochladen eines Windows-VM-Images an Azure für Resource Manager-Bereitstellungen](upload-generalized-managed.md)
* [Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer](troubleshoot-activation-problems.md)

