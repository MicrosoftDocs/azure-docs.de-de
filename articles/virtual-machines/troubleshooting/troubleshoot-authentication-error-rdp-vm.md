---
title: Problembehandlung von Authentifizierungsfehlern bei der Verwendung von RDP für das Herstellen von Verbindungen mit Azure-VMs | Microsoft-Dokumentation
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60594927"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Problembehandlung von Authentifizierungsfehlern bei der Verwendung von RDP für das Herstellen von Verbindungen mit Azure-VMs

Dieser Artikel kann Sie bei der Problembehandlung von Authentifizierungsfehlern unterstützen, die auftreten, wenn Sie eine RDP-Verbindung (Remotedesktopprotokoll) zum Herstellen einer Verbindung mit einem virtuellen Azure-Computer (VM) verwenden.

## <a name="symptoms"></a>Symptome

Sie erfassen einen Screenshot einer Azure-VM, auf dem der Begrüßungsbildschirm zu sehen ist und damit angezeigt wird, dass das Betriebssystem ausgeführt wird. Wenn Sie jedoch versuchen, eine Verbindung mit der VM mithilfe einer Remotedesktopverbindung herzustellen, erhalten Sie eine der folgenden Fehlermeldungen.

### <a name="error-message-1"></a>Fehlermeldung 1

**Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar**.

### <a name="error-message-2"></a>Fehlermeldung 2

**Der Remotecomputer, mit dem Sie eine Verbindung herstellen möchten, erfordert NLA (Network Level Authentication, Authentifizierung auf Netzwerkebene), es besteht jedoch kein Kontakt mit Ihrem Windows-Domänencontroller zum Durchführen der NLA. Wenn Sie Administrator des Remotecomputers sind, können Sie NLA mithilfe der Optionen auf der Registerkarte „Remote“ im Dialogfeld „Systemeigenschaften“ deaktivieren.**

### <a name="error-message-3-generic-connection-error"></a>Fehlermeldung 3 (allgemeiner Verbindungsfehler)

**Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen. Versuchen Sie noch einmal, die Verbindung herzustellen, und wenden Sie sich an den Besitzer des Remotecomputers oder an Ihren Netzwerkadministrator, wenn das Problem weiterhin besteht.**

## <a name="cause"></a>Ursache

Es gibt mehrere Gründe, warum der RDP-Zugriff auf eine VM von NLA blockiert werden kann.

### <a name="cause-1"></a>Ursache 1

Die VM kann nicht mit dem Domänencontroller (DC) kommunizieren. Dieses Problem kann eine RDP-Sitzung am Zugriff auf eine VM mithilfe von Domänenanmeldeinformationen hindern. Allerdings wäre die Anmeldung unter Verwendung der lokalen Administratoranmeldeinformationen trotzdem noch möglich. Dieses Problem kann in den folgenden Situationen auftreten:

1. Der Active Directory-Sicherheitskanal zwischen der VM und dem DC ist beschädigt.

2. Der VM verfügt über ein altes Exemplar des Kontokennworts, und der DC hat ein neueres Exemplar.

3. Der Status des DCs, mit dem die VM eine Verbindung herzustellen versucht, ist fehlerhaft.

### <a name="cause-2"></a>Ursache 2

Die Verschlüsselungsstufe der VM ist höher als die vom Clientcomputer verwendete.

### <a name="cause-3"></a>Ursache 3

Die Protokolle TLS 1.0, 1.1 oder 1.2 (Server) sind auf der VM deaktiviert.

### <a name="cause-4"></a>Ursache 4

Die VM wurde so eingerichtet, dass die Anmeldung mithilfe von Domänenanmeldeinformationen deaktiviert ist, und die lokale Sicherheitsautorität (LSA) ist fehlerhaft eingerichtet.

### <a name="cause-5"></a>Ursache 5

Die VM wurde dafür eingerichtet, nur Verbindungen mit FIPS-konformen (Federal Information Processing Standard) Algorithmen anzunehmen. Dies erfolgt in der Regel mithilfe einer Active Directory-Richtlinie. Diese Konfiguration ist selten anzutreffen, aber FIPS kann nur für Remotedesktopverbindungen erzwungen werden.

## <a name="before-you-troubleshoot"></a>Vor Beginn der Problembehandlung

### <a name="create-a-backup-snapshot"></a>Erstellen einer Momentaufnahme für die Sicherung

Wenn Sie eine Momentaufnahme für die Sicherung erstellen möchten, führen Sie die unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md) beschriebenen Schritte aus.

### <a name="connect-to-the-vm-remotely"></a>Herstellen einer Remoteverbindung mit dem virtuellen Computer

Um eine Remoteverbindung mit der VM herzustellen, verwenden Sie eine der in [How to use remote tools to troubleshoot Azure VM issues](remote-tools-troubleshoot-azure-vm-issues.md) (Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen) beschriebenen Methoden.

### <a name="group-policy-client-service"></a>Gruppenrichtlinien-Clientdienst

Wenn es sich um eine in eine Domäne eingebundene VM handelt, beenden Sie zuerst den Gruppenrichtlinien-Clientdienst, um das Überschreiben der Änderungen durch eine Active Directory-Richtlinie zu verhindern. Führen Sie zu diesem Zweck den folgenden Befehl aus:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Stellen Sie nach dem Beheben des Problems die Fähigkeit der VM wieder her, eine Verbindung mit der Domäne herzustellen, um das neueste GPO bei der Domäne abzurufen. Führen Sie zu diesem Zweck die folgenden Befehle aus:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Wenn die Änderung zurückgesetzt wird, bedeutet dies, dass das Problem durch eine Active Directory-Richtlinie verursacht wird. 

### <a name="workaround"></a>Problemumgehung

Um dieses Problem zu umgehen, führen Sie die folgenden Befehle im Befehlsfenster aus, um NLA zu deaktivieren:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Starten Sie die VM anschließend neu.

Um NLA wieder zu aktivieren, führen Sie den folgenden Befehl aus, und starten Sie die VM dann neu:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="for-domain-joined-vms"></a>Für in die Domäne eingebundene virtuelle Computer

Um dieses Problem zu beheben, überprüfen Sie zunächst, ob die VM eine Verbindung mit einem DC herstellen kann, ob der DC den Status „fehlerfrei“ aufweist und Anforderungen von der VM verarbeiten kann.

>[!Note] 
>Um die Integrität des DCs zu testen, können Sie eine andere VM im gleichen VNET und Subnetz verwenden. Beide müssen den gleichen Anmeldeserver gemein haben.

Stellen Sie eine Verbindung mit der VM her, bei der das Problem auftritt, indem Sie die serielle Konsole, Remote-CMD oder Remote-PowerShell verwenden. Führen Sie dazu die unter „Herstellen einer Remoteverbindung mit dem virtuellen Computer“ beschriebenen Schritte aus.

Um zu bestimmen, mit welchem DC die VM eine Verbindung herstellt, führen Sie in der Konsole den folgenden Befehl aus: 

```cmd
set | find /i "LOGONSERVER"
```

Überprüfen Sie anschließend die Integrität des sicheren Kanals zwischen der VM und dem DC. Führen Sie dazu den folgenden Befehl in einer PowerShell-Instanz mit erhöhten Rechten aus. Dieser Befehl gibt ein Boolesches Flag zurück, das anzeigt, ob der sichere Kanal aktiv ist:

```powershell
Test-ComputerSecureChannel -verbose
```

Wenn der Kanal gestört ist, führen Sie den folgenden Befehl aus, um ihn zu reparieren:

```powershell
Test-ComputerSecureChannel -repair
```

Achten Sie darauf, dass das Kennwort für das Computerkonto in der VM und auf dem DC aktualisiert wird:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Wenn die Kommunikation zwischen dem DC und der VM gut ist, die Integrität des DCs aber nicht ausreicht, um eine RDP-Sitzung zu öffnen, können Sie versuchen, den DC neu zu starten.

Wenn das Kommunikationsproblem mit der Domäne durch die vorstehenden Befehle nicht behoben wurde, können Sie die VM erneut in die Domäne einbinden. Gehen Sie dazu folgendermaßen vor:

1. Erstellen Sie mithilfe des folgenden Inhalts ein Skript mit dem Namen „Unjoin.ps1“, und stellen Sie es dann als benutzerdefinierte Skripterweiterung auf dem Azure-Portal bereit:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Dieses Skript erzwingt die Herauslösung der VM aus der Domäne und startet sie 10 Sekunden später erneut. Anschließend müssen Sie das domänenseitige Computerobjekt bereinigen.

2.  Binden Sie nach erledigter Bereinigung die VM wieder in die Domäne ein. Erstellen Sie dazu mithilfe des folgenden Inhalts ein Skript mit dem Namen „JoinDomain.ps1“, und stellen Sie das Skript anschließend als benutzerdefinierte Skripterweiterung auf dem Azure-Portal bereit: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Dadurch wird die VM mithilfe der angegebenen Anmeldeinformationen in die Domäne eingebunden.

Wenn der Active Directory-Kanal fehlerfrei ist, das Computerkennwort aktualisiert wurde und der Domänencontroller erwartungsgemäß arbeitet, führen Sie die folgenden Schritte aus.

Wenn das Problem weiterhin besteht, überprüfen Sie, ob die Domänenanmeldeinformationen deaktiviert sind. Öffnen Sie zu diesem Zweck eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie dann den folgenden Befehl aus, um zu bestimmen, ob die VM so eingerichtet ist, dass Domänenkonten für die Anmeldung bei der VM deaktiviert werden:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Wenn der Schlüssel auf **1** festgelegt ist, bedeutet dies, dass der Server so eingerichtet wurde, dass er keine Domänenanmeldeinformationen zulässt. Ändern Sie diesen Schlüssel in **0**.

### <a name="for-standalone-vms"></a>Für eigenständige VMs

#### <a name="check-minencryptionlevel"></a>Überprüfen von MinEncryptionLevel

Führen Sie in einer CMD-Instanz den folgenden Befehl aus, um den Registrierungswert **MinEncryptionLevel** abzufragen:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Führen Sie ausgehend vom Registrierungswert diese Schritte aus:

* 4 (FIPS): Navigieren Sie zu [FIPS-konforme Algorithmusverbindungen überprüfen](#fips-compliant).

* 3 (128-Bit-Verschlüsselung): Legen Sie den Schweregrad auf **2** fest, indem Sie den folgenden Befehl ausführen:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (Höchstmögliche Verschlüsselung nach Maßgabe des Clients): Sie können versuchen, die Verschlüsselung auf den Mindestwert **1** festzulegen, indem Sie den folgenden Befehl ausführen:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Führen Sie einen Neustart der VM aus, so dass die Änderungen an der Registrierung wirksam werden.

#### <a name="tls-version"></a>TLS-Version

Je nach System verwendet RDP das TLS 1.0-, 1.1- oder 1.2-Protokoll (Server). Um abzufragen, wie diese Protokolle in der VM eingerichtet sind, öffnen Sie eine CMD-Instanz, und führen Sie dann die folgenden Befehle aus:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Wenn die zurückgegebenen Werte nicht alle **1** sind, bedeutet dies, dass das Protokoll deaktiviert ist. Führen Sie die folgenden Befehle aus, um diese Protokolle zu aktivieren:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Für andere Protokollversionen können Sie die folgenden Befehle ausführen:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Rufen Sie die SSH/TLS-Version x.x aus den Protokollen des Gastbetriebssystems für die SCHANNEL-Fehler ab.

#### <a name="fips-compliant"></a>FIPS-konforme Algorithmusverbindungen überprüfen

Für Remotedesktop lässt sich die ausschließliche Verwendung von FIPS-konformen Algorithmusverbindungen erzwingen. Dies kann mithilfe eines Registrierungsschlüssels festgelegt werden. Öffnen Sie zu diesem Zweck ein Eingabeaufforderungsfenster mit erhöhten Rechten, und fragen Sie dann die folgenden Schlüssel ab:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Wenn der Befehl **1** zurückgibt, ändern Sie den Wert des Registrierungsschlüssels in **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Überprüfen des aktuellen MinEncryptionLevel für die VM:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Wenn der Befehl **4** zurückgibt, ändern Sie den Registrierungswert in **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Führen Sie einen Neustart der VM aus, so dass die Änderungen an der Registrierung wirksam werden.

## <a name="next-steps"></a>Nächste Schritte

[SetEncryptionLevel-Methode der Win32_TSGeneralSetting-Klasse](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Konfigurieren von Serverauthentifizierung und Verschlüsselungsstufen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting-Klasse](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
