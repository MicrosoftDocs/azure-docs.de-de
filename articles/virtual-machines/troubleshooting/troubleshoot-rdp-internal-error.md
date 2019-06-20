---
title: Interner Fehler beim Herstellen einer RDP-Verbindung mit Azure Virtual Machines | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie interne RDP-Fehler in Microsoft Azure beheben.| Microsoft-Dokumentation
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 4476e4732dfcf8d79c9678a7ff4719eba10e48f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319427"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Interner Fehler beim Herstellen einer Verbindung mit einem virtuellen Azure-Computer über Remotedesktop

Dieser Artikel beschreibt einen Fehler, der beim Herstellen einer Verbindung mit einem virtuellen Computer in Microsoft Azure auftreten kann.
> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptoms"></a>Symptome

Sie können per Remotedesktopprotokoll (RDP) keine Verbindung mit einem virtuellen Azure-Computer herstellen. Die Verbindungsherstellung bleibt im Abschnitt zum Konfigurieren der Remotesitzung hängen, oder Sie erhalten die folgende Fehlermeldung:

- RDP internal error (Interner RDP-Fehler)
- Es ist ein interner Fehler aufgetreten.
- This computer can't be connected to the remote computer. Try connecting again. If the problem continues, contact the owner of the remote computer or your network administrator. (Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen. Versuchen Sie noch einmal, die Verbindung herzustellen. Wenden Sie sich an den Besitzer des Remotecomputers oder an Ihren Netzwerkadministrator, wenn das Problem weiterhin besteht.)


## <a name="cause"></a>Ursache

Dieses Problem kann folgende Ursachen haben:

- Auf die lokalen RSA-Verschlüsselungsschlüssel kann nicht zugegriffen werden.
- Das TLS-Protokoll ist deaktiviert.
- Das Zertifikat ist beschädigt oder abgelaufen.

## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Verwenden Sie zum Beheben dieses Problems die serielle Konsole, oder [reparieren Sie den virtuellen Computer offline](#repair-the-vm-offline), indem Sie den Betriebssystemdatenträger des virtuellen Computers an eine Wiederherstellungs-VM anfügen.


### <a name="use-serial-control"></a>Verwenden der seriellen Konsole

Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine PowerShell-Instanz](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Ist die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert, gehen Sie zum Abschnitt [Reparieren des virtuellen Computers im Offlinestatus](#repair-the-vm-offline).

#### <a name="step-1-check-the-rdp-port"></a>Schritt 1: Überprüfen des RDP-Ports

1. Überprüfen Sie in einer PowerShell-Instanz mithilfe von [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
), ob Port 8080 von anderen Anwendungen verwendet wird:

        Netstat -anob |more
2. Verwendet „Termservice.exe“ Port 8080, fahren Sie mit Schritt 2 fort. Wenn ein anderer Dienst oder eine andere Anwendung als „Termservice.exe“ Port 8080 nutzt, führen Sie die folgenden Schritte aus:

    1. Beenden Sie den Dienst oder die Anwendung, der bzw. die den Dienst 3389 verwendet:

            Stop-Service -Name <ServiceName> -Force

    2. Starten Sie den Terminaldienst:

            Start-Service -Name Termservice

2. Wenn die Anwendung nicht beendet werden kann oder diese Methode nicht für Sie zutrifft, ändern Sie den Port für RDP:

    1. Ändern Sie den Port:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Legen Sie die Firewall für den neuen Port fest:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Aktualisieren Sie die Netzwerksicherheitsgruppe für den neuen Port](../../virtual-network/security-overview.md) im Azure-Portal (RDP-Port).

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Schritt 2: Festlegen der richtigen Berechtigungen für das selbstsignierte RDP-Zertifikat

1.  Führen Sie in einer PowerShell-Instanz nacheinander die folgenden Befehle aus, um das selbstsignierte RDP-Zertifikat zu erneuern:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Wenn Sie das Zertifikat mit dieser Methode nicht erneuern können, versuchen Sie, das selbstsignierte RDP-Zertifikat remote zu erneuern:

    1. Geben Sie auf einem funktionierenden virtuellen Computer, der mit dem fehlerhaften virtuellen Computer verbunden ist, **mmc** in das Feld **Ausführen** ein, um die Microsoft Management Console zu öffnen.
    2. Wählen Sie im Menü **Datei** die Option **Snap-In hinzufügen/entfernen** und dann **Zertifikate** und **Hinzufügen** aus.
    3. Wählen Sie **Computerkonten** und **Anderer Computer** aus, und fügen Sie anschließend die IP-Adresse des virtuellen Computers hinzu, bei dem Probleme auftreten.
    4. Wechseln Sie zum Ordner **Remotedesktop\Zertifikate**, klicken Sie mit der rechten Maustaste auf das Zertifikat, und wählen Sie dann **Löschen** aus.
    5. Starten Sie in einer PowerShell-Instanz auf der seriellen Konsole den Remotedesktop-Konfigurationsdienst neu:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. Setzen Sie die Berechtigungen für den Ordner „MachineKeys“ zurück.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. Starten Sie den virtuellen Computer neu, und versuchen Sie dann, eine Remotedesktopverbindung mit dem virtuellen Computer herzustellen. Falls der Fehler weiterhin auftritt, fahren Sie mit dem nächsten Schritt fort.

Schritt 3: Aktivieren aller unterstützten TLS-Versionen

Der RDP-Client verwendet TLS 1.0 als Standardprotokoll. Dies kann jedoch in den neuen Standard TLS 1.1 geändert werden. Wenn TLS 1.1 auf dem virtuellen Computer deaktiviert ist, tritt bei der Verbindung ein Fehler auf.
1.  Aktivieren Sie in einer CMD-Instanz das TLS-Protokoll:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  Beenden Sie vorübergehend die Gruppenrichtlinienaktualisierung, damit die AD-Richtlinie die Änderungen nicht überschreibt:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Führen Sie einen Neustart des virtuellen Computers aus, damit die Änderungen wirksam werden. Ist das Problem behoben, führen Sie den folgenden Befehl aus, um die Gruppenrichtlinie wieder zu aktivieren:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Wenn die Änderung zurückgesetzt wird, bedeutet dies, dass eine Active Directory-Richtlinie in Ihrer Unternehmensdomäne vorhanden ist. Sie müssen diese Richtlinie ändern, um zu verhindern, dass das Problem erneut auftritt.

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie nach dem Anfügen des Betriebssystemdatenträgers an den virtuellen Wiederherstellungscomputer sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Notieren Sie sich den Laufwerkbuchstaben des angefügten Betriebssystemdatenträgers.
3. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

#### <a name="enable-dump-log-and-serial-console"></a>Aktivieren des Sicherungsprotokolls und der seriellen Konsole

Um das Sicherungsprotokoll und die serielle Konsole zu aktivieren, führen Sie das folgende Skript aus.

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**).
2. Führen Sie das folgende Skript aus:

    In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert für Ihren virtuellen Computer.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Zurücksetzen der Berechtigungen für den Ordner „MachineKeys“

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**).
2. Führen Sie das folgende Skript aus. In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert für Ihren virtuellen Computer.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Aktivieren aller unterstützten TLS-Versionen

1.  Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**), und führen Sie dann die folgenden Befehle aus. Im folgenden Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert Ihres virtuellen Computers.
2.  Überprüfen Sie, welche TLS aktiviert ist:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Ist der Schlüssel nicht vorhanden oder lautet sein Wert **0**, aktivieren Sie das Protokoll durch Ausführung der folgenden Skripts:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  Aktivieren von NLA:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [Trennen Sie den Betriebssystemdatenträger, und erstellen Sie den virtuellen Computer erneut](../windows/troubleshoot-recovery-disks-portal.md). Überprüfen Sie dann, ob das Problem behoben wurde.





