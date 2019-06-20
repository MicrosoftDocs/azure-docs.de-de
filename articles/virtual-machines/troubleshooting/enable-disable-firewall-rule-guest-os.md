---
title: Aktivieren oder Deaktivieren einer Firewallregel unter einem Gastbetriebssystem in Azure VM | Microsoft-Dokumentation
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: ed3d89bc15f960947a48ac4364bd14f3fdf50cc2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505562"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Aktivieren oder Deaktivieren einer Firewallregel unter einem Gastbetriebssystem in Azure VM

Dieser Artikel enthält eine Referenz zur Problembehandlung in Situationen, in denen Sie vermuten, dass die Firewall des Gastbetriebssystems teilweise Datenverkehr zu einem virtuellen Computer (VM) filtert. Dies kann aus folgenden Gründen hilfreich sein:

*   Wenn an der Firewall absichtlich eine Änderung vorgenommen wurde, die zum Ausfall der RDP-Verbindungen führte, können Sie mithilfe der „Benutzerdefinierten Skripterweiterung“ das Problem lösen.

*   Deaktivieren aller Firewallprofile ist eine wesentlich sicherere Methode zur Problembehandlung als das Festlegen der RDP-spezifischen Firewallregel.

## <a name="solution"></a>Lösung

Wie Sie die Firewallregeln konfigurieren, hängt von der erforderlichen Zugriffsebene des virtuellen Computers ab. Die folgenden Beispiele verwenden RDP-Regeln. Allerdings können die gleichen Methoden durch Zeigen auf den richtigen Registrierungsschlüssel für jede andere Art von Datenverkehr angewendet werden.

### <a name="online-troubleshooting"></a>Onlineproblembehandlung 

#### <a name="mitigation-1-custom-script-extension"></a>Lösung 1: Benutzerdefinierte Skripterweiterung

1.  Erstellen Sie Ihr Skript mit der folgenden Vorlage.

    *   Um eine Regel zu aktivieren:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Um eine Regel zu deaktivieren:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Laden Sie dieses Skript mithilfe des Features [Benutzerdefinierte Skripterweiterung](../extensions/custom-script-windows.md) in das Azure-Portal hoch. 

#### <a name="mitigation-2-remote-powershell"></a>Lösung 2: Remote-PowerShell

Wenn der virtuelle Computer online ist und ein anderer virtueller Computer im gleichen virtuellen Netzwerk darauf zugreifen kann, können Sie die folgenden vorbeugenden Maßnahmen mithilfe des anderen virtuellen Computers durchführen.

1.  Öffnen Sie auf dem virtuellen Computer, der zur Problembehandlung dient, ein PowerShell-Konsolenfenster.

2.  Führen Sie die folgenden Befehle wie erforderlich aus.

    *   Um eine Regel zu aktivieren:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Um eine Regel zu deaktivieren:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Lösung 3: PSTools-Befehle

Wenn der virtuelle Computer online ist und ein anderer virtueller Computer im gleichen virtuellen Netzwerk darauf zugreifen kann, können Sie die folgenden vorbeugenden Maßnahmen mithilfe des anderen virtuellen Computers durchführen.

1.  Laden Sie auf den virtuellen Computer, der zur Problembehandlung dient, [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools) herunter.

2.  Öffnen Sie eine CMD-Instanz, und greifen Sie dann über deren interne IP-Adresse (DIP) auf den virtuellen Computer zu. 

    * Um eine Regel zu aktivieren:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Um eine Regel zu deaktivieren:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Lösung 4: Remoteregistrierung

Wenn der virtuelle Computer online ist und ein anderer virtueller Computer im gleichen virtuellen Netzwerk darauf zugreifen kann, können Sie die [Remoteregistrierung](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) auf dem anderen virtuellen Computer verwenden.

1.  Starten Sie auf dem virtuellen Computer, der zur Problembehandlung dient, den Registrierungs-Editor (regedit.exe), und wählen Sie **Datei** > **Mit Netzwerkregistrierung verbinden** aus.

2.  Öffnen Sie die Verzweigung  *TARGET MACHINE*\SYSTEM, und geben Sie die folgenden Werte ein:

    * Um eine Regel zu aktivieren, öffnen Sie den folgenden Registrierungswert:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Ändern Sie dann **Active=FALSE** in **Active=TRUE** in der Zeichenfolge:

        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Um eine Regel zu deaktivieren, öffnen Sie den folgenden Registrierungswert:
    
        *TARGET MACHINE*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ändern Sie dann **Active=TRUE** in **Active=FALSE** in der Zeichenfolge:
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  Starten Sie den virtuellen Computer neu, um diese Änderungen zu übernehmen.

### <a name="offline-troubleshooting"></a>Offlineproblembehandlung 

Wenn Sie mit keiner Methode auf den virtuellen Computer zugreifen können, ist die „Benutzerdefinierte Skripterweiterung“ nicht einsetzbar, und Sie müssen im OFFLINEMODUS direkt über den Betriebssystemdatenträger arbeiten.

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter  [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

1.  [Fügen Sie den Systemdatenträger an einen virtuellen Wiederherstellungscomputer an.](troubleshoot-recovery-disks-portal-windows.md)

2.  Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

3.  Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als  **Online** gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.

4.  Bevor Sie Änderungen vornehmen, erstellen Sie eine Kopie des Ordners „\Windows\system32\config“ für den Fall, dass ein Rollback der Änderungen erforderlich ist.

5.  Starten Sie auf dem virtuellen Computer, der zur Problembehandlung dient, den Registrierungs-Editor (regedit.exe).

6.  Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE** und dann im Menü die Optionen  **Datei** > **Struktur laden** aus.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Suchen und öffnen Sie die Datei „\windows\system32\config\SYSTEM“. 

    > [!Note]
    > Sie werden aufgefordert, einen Namen einzugeben. Geben Sie **BROKENSYSTEM** ein, und erweitern Sie **HKEY_LOCAL_MACHINE**. Daraufhin wird ein zusätzlicher Schlüssel mit dem Namen **BROKENSYSTEM** angezeigt. Für diese Problembehandlung binden wir diese Problemstrukturen als **BROKENSYSTEM** ein.

8.  Nehmen Sie die folgenden Änderungen an der Verzweigung BROKENSYSTEM vor:

    1.  Überprüfen Sie, mit welchem Registrierungsschlüssel **ControlSet** der virtuelle Computer startet. Sie sehen seine Schlüsselnummer in HKLM\BROKENSYSTEM\Select\Current.

    2.  Um eine Regel zu aktivieren, öffnen Sie den folgenden Registrierungswert:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Ändern Sie dann **Active=FALSE** in **Active=TRUE**.
        
        **v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Um eine Regel zu deaktivieren, öffnen Sie den folgenden Registrierungsschlüssel:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ändern Sie dann **Active=TRUE** in **Active=FALSE**.
        
        **v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  Markieren Sie **BROKENSYSTEM**, und wählen Sie dann **Datei** > **Struktur entladen** im Menü aus.

10. [Trennen des Betriebssystemdatenträgers und erneutes Erstellen des virtuellen Computers](troubleshoot-recovery-disks-portal-windows.md).

11. Überprüfen Sie, ob das Problem behoben ist.
