---
title: Blockierung von eingehendem Datenverkehr durch die Firewall des Azure-VM-Gastbetriebssystems | Microsoft-Dokumentation
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
ms.openlocfilehash: 0a0da446385c592bfeda2e01e209ef1fb75b7de3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711570"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Blockierung von eingehendem Datenverkehr durch die Firewall des Azure-VM-Gastbetriebssystem

In diesem Artikel wird erläutert, wie Sie das im Remote Desktop Portal (RDP) auftretende Problem beheben, dass die Firewall des Gastbetriebssystems eingehenden Datenverkehr blockiert.

## <a name="symptoms"></a>Symptome

Sie können keine RDP-Verbindung mit einem virtuellen Azure-Computer (VM) herstellen. „Startdiagnose -> Screenshot“ zeigt, dass das Betriebssystem auf der Willkommensseite vollständig geladen ist (STRG+ALT+ENTF).

## <a name="cause"></a>Ursache

### <a name="cause-1"></a>Ursache 1

Die RDP-Regel ist nicht zum Zulassen des RDP-Datenverkehrs eingerichtet.

### <a name="cause-2"></a>Ursache 2

Die Firewallprofile des Gastbetriebssystems wurden zum Blockieren aller eingehenden Verbindungen eingerichtet. Dazu zählt auch RDP-Datenverkehr.

![Firewalleinstellungen](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter  [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Um das Problem zu beheben, verwenden Sie eine der Methoden in [Verwenden von Remotetools zur Behandlung von Azure-VM-Problemen](remote-tools-troubleshoot-azure-vm-issues.md) zum Herstellen einer Remoteverbindung mit dem virtuellen Computer, und bearbeiten Sie die Firewallregeln des Gastbetriebssystems so, dass sie RDP-Datenverkehr **zulassen**.

### <a name="online-troubleshooting"></a>Onlineproblembehandlung

Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine PowerShell-Instanz](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Ist die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert, gehen Sie zu [Reparieren des virtuellen Computers im Offlinestatus](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Vorbeugende Maßnahme 1

1.  Wenn der Azure-Agent installiert ist und auf dem virtuellen Computer ordnungsgemäß funktioniert, können Sie die Option „Nur Konfiguration zurücksetzen“ unter **Support + Problembehandlung** > **Kennwort zurücksetzen** im VM-Menü verwenden.

2.  Diese Wiederherstellungsoption bewirkt Folgendes:

    *   Aktiviert eine RDP-Komponente, wenn sie deaktiviert ist.

    *   Aktiviert alle Windows-Firewallprofile.

    *   Stellen Sie sicher, dass die RDP-Regel in der Windows-Firewall aktiviert ist.

    *   Wenn die vorherigen Schritte nicht funktionieren, sollten Sie die Firewallregel manuell zurücksetzen. Fragen Sie zu diesem Zweck alle Regeln ab, die den Namen „Remotedesktop“ enthalten, indem Sie den folgenden Befehl ausführen:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Wenn der RDP-Port auf einen anderen Port als 3389 festgelegt wurde, müssen Sie eine möglicherweise erstellte benutzerdefinierte Regel finden, die auf diesen Port festgelegt wurde. Um alle eingehenden Regeln mit benutzerdefiniertem Port abzufragen, führen Sie den folgenden Befehl aus:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Wenn Sie sehen, dass die Regel deaktiviert ist, aktivieren Sie sie. Führen Sie den folgenden Befehl aus, um eine gesamte Gruppe zu öffnen, z.B. die integrierte Gruppe „Remotedesktop“:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Führen Sie andernfalls den folgenden Befehl aus, um die spezielle Remotedesktopregel (TCP eingehend) zu öffnen:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Zur Problembehandlung können Sie die Firewallprofile auf OFF setzen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Nach Abschluss der Problembehandlung und zum ordnungsgemäßen Einrichten der Firewall sollten Sie die Firewall erneut aktivieren.

    > [!Note]
    > Sie müssen den virtuellen Computer nicht neu starten, um diese Änderungen zu übernehmen.

5.  Versuchen Sie, eine RDP-Verbindung herzustellen, um auf den virtuellen Computer zuzugreifen.

#### <a name="mitigation-2"></a>Vorbeugende Maßnahme 2

1.  Fragen Sie die Firewallprofile ab, um zu bestimmen, ob die Firewallrichtlinie für eingehende Verbindungen auf  *BlockInboundAlways* festgelegt ist:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Die folgenden Richtlinien gelten für die Firewallrichtlinie in Abhängigkeit von der Art der Einrichtung:
    >    * *BlockInbound*: Der gesamte eingehende Datenverkehr wird blockiert, sofern keine Regel in Kraft ist, die diesen Datenverkehr ermöglicht.
    >    * *BlockInboundAlways*: Alle Firewallregeln werden ignoriert und sämtlicher Datenverkehr blockiert.

2.  Bearbeiten Sie *DefaultInboundAction* so, dass diese Profile Datenverkehr  **zulassen**. Führen Sie zu diesem Zweck den folgenden Befehl aus:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Fragen Sie die Profile erneut ab, um sicherzustellen, dass die Änderung erfolgreich vorgenommen wurde. Führen Sie zu diesem Zweck den folgenden Befehl aus:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Sie müssen den virtuellen Computer nicht neu starten, um diese Änderungen zu übernehmen.

4.  Versuchen Sie erneut, über RDP auf Ihren virtuellen Computer zuzugreifen.

### <a name="offline-mitigations"></a>Vorbeugende Maßnahmen offline

1.  [Fügen Sie den Systemdatenträger an einen virtuellen Wiederherstellungscomputer an.](troubleshoot-recovery-disks-portal-windows.md)

2.  Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

3.  Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als  **Online** gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.

#### <a name="mitigation-1"></a>Vorbeugende Maßnahme 1

Siehe  [Aktivieren/Deaktivieren einer Firewallregel unter einem Gastbetriebssystem](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Vorbeugende Maßnahme 2

1.  [Fügen Sie den Systemdatenträger an einen virtuellen Wiederherstellungscomputer an.](troubleshoot-recovery-disks-portal-windows.md)

2.  Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

3.  Stellen Sie nach dem Anfügen des Betriebssystemdatenträgers an den virtuellen Wiederherstellungscomputer sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als  **Online** gekennzeichnet ist. Beachten Sie den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.

4.  Öffnen Sie dann eine CMD-Instanz mit erhöhten Rechten, und führen Sie die folgenden Skripts aus:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Trennen des Betriebssystemdatenträgers und erneutes Erstellen des virtuellen Computers](troubleshoot-recovery-disks-portal-windows.md).

6.  Überprüfen Sie, ob das Problem behoben ist.
