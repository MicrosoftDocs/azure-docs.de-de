---
title: Deaktivieren der Firewall des Gastbetriebssystems eines virtuellen Azure-Computers | Microsoft-Dokumentation
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
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60505460"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Deaktivieren der Firewall des Gastbetriebssystems eines virtuellen Azure-Computers

Dieser Artikel enthält eine Referenz für Situationen, in denen Sie vermuten, dass die Firewall des Gastbetriebssystems teilweise oder vollständig Datenverkehr zu einem virtuellen Computer (VM) filtert. Dies kann auftreten, wenn absichtlich Änderungen an der Firewall vorgenommen wurden, die den Ausfall von RDP-Verbindungen verursacht hat.

## <a name="solution"></a>Lösung

Der in diesem Artikel beschriebene Prozess soll als Problemumgehung verwendet werden, damit Sie sich auf Ihre eigentliche Aufgabe konzentrieren können – die richtige Einrichtung der Firewallregeln. Es ist eine Microsoft Best Practice, die Windows Firewall-Komponente zu aktivieren. Wie Sie die Firewallregeln konfigurieren, hängt von der erforderlichen Zugriffsebene des virtuellen Computers ab.

### <a name="online-solutions"></a>Onlinelösungen 

Wenn der virtuelle Computer online ist und ein anderer virtueller Computer im gleichen virtuellen Netzwerk darauf zugreifen kann, können Sie diese vorbeugenden Maßnahmen mithilfe des anderen virtuellen Computers durchführen.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Lösung 1: Feature „Benutzerdefinierte Skripterweiterung“ oder „Befehl ‚Ausführen‘“

Mit einem Azure-Agent können Sie das Feature [Benutzerdefinierte Skripterweiterung](../extensions/custom-script-windows.md) oder [Befehl „Ausführen“](../windows/run-command.md) (nur Resource Manager-VMs) verwenden, um die folgenden Skripts remote auszuführen.

> [!Note]
> * Wenn die Firewall lokal eingerichtet ist, führen Sie das folgende Skript aus:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Wenn die Firewall über eine Active Directory-Richtlinie eingerichtet ist, können Sie das folgende Skript für temporären Zugriff ausführen. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Sobald die Richtlinie erneut angewendet wird, werden Sie allerdings aus der Remotesitzung entfernt. Die endgültige Lösung für dieses Problem ist die Änderung der Richtlinie, die auf diesem Computer angewendet wird.

#### <a name="mitigation-2-remote-powershell"></a>Lösung 2: Remote-PowerShell

1.  Stellen Sie eine Verbindung mit einem virtuellen Computer her, der sich in demselben virtuellen Netzwerk wie der virtuelle Computer befindet, den Sie mithilfe der RDP-Verbindung nicht erreichen können.

2.  Öffnen Sie ein PowerShell-Konsolenfenster.

3.  Führen Sie die folgenden Befehle aus:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Wenn die Firewall durch ein Gruppenrichtlinienobjekt festgelegt ist, funktioniert diese Methode möglicherweise nicht, da dieser Befehl nur die Einträge in der lokalen Registrierung ändert. Wenn eine Richtlinie vorhanden ist, wird diese Änderung überschrieben. 

#### <a name="mitigation-3-pstools-commands"></a>Lösung 3: PSTools-Befehle

1.  Laden Sie auf den virtuellen Computer, der zur Problembehandlung dient, [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools) herunter.

2.  Öffnen Sie eine CMD-Instanz, und greifen Sie dann über deren DIP-Adresse auf den virtuellen Computer zu.

3.  Führen Sie die folgenden Befehle aus:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Lösung 4: Remoteregistrierung 

Gehen Sie zur Verwendung der [Remoteregistrierung](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) wie folgt vor.

1.  Starten Sie auf dem virtuellen Computer, der zur Problembehandlung dient, den Registrierungs-Editor, und wählen Sie **Datei** > **Mit Netzwerkregistrierung verbinden** aus.

2.  Öffnen Sie die Verzweigung  *TARGET MACHINE*\SYSTEM, und geben Sie die folgenden Werte ein:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Starten Sie den Dienst neu. Da dies mit der Remoteregistrierung nicht möglich ist, müssen Sie „Dienstkonsole entfernen“ verwenden.

4.  Öffnen Sie eine Instanz von  **Services.msc**.

5.  Klicken Sie auf **Dienste (lokal)** .

6.  Wählen Sie **Verbindung mit einem anderen Computer herstellen** aus.

7.  Geben Sie die **Private IP-Adresse (DIP)**  des virtuellen Computers ein, auf dem das Problem auftritt.

8.  Starten Sie die lokale Firewallrichtlinie neu.

9.  Versuchen Sie erneut, von Ihrem lokalen Computer aus über RDP eine Verbindung mit dem virtuellen Computer herzustellen.

### <a name="offline-solutions"></a>Offlinelösungen 

Wenn Sie den virtuellen Computer mit keiner Methode erreichen können, ist die „Benutzerdefinierte Skripterweiterung“ nicht einsetzbar, und Sie müssen im OFFLINEMODUS direkt über den Betriebssystemdatenträger arbeiten. Gehen Sie dazu folgendermaßen vor:

1.  [Fügen Sie den Systemdatenträger an einen virtuellen Wiederherstellungscomputer an.](troubleshoot-recovery-disks-portal-windows.md)

2.  Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

3.  Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als „Online“ gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.

4.  Bevor Sie Änderungen vornehmen, erstellen Sie eine Kopie des Ordners „\Windows\system32\config“ für den Fall, dass ein Rollback der Änderungen erforderlich ist.

5.  Starten Sie auf dem virtuellen Computer, der zur Problembehandlung dient, den Registrierungs-Editor (regedit.exe). 

6.  Für dieses Verfahren zur Problembehandlung werden die Strukturen als BROKENSYSTEM und BROKENSOFTWARE eingebunden.

7.  Wählen Sie den Schlüssel HKEY_LOCAL_MACHINE und dann im Menü die Optionen „Datei > Struktur laden“ aus.

8.  Suchen Sie nach der Datei „\windows\system32\config\SYSTEM“ auf dem angefügten Datenträger.

9.  Öffnen Sie eine PowerShell-Instanz mit erhöhten Rechten, und führen Sie dann die folgenden Befehle aus:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Trennen des Betriebssystemdatenträgers und erneutes Erstellen des virtuellen Computers](troubleshoot-recovery-disks-portal-windows.md).

11. Überprüfen Sie, ob das Problem behoben ist.
