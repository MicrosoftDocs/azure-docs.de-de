---
title: Falsch konfigurierte Gastbetriebssystem-Firewall der Azure-VM | Microsoft-Dokumentation
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
ms.openlocfilehash: fcea5e4e6bb108f1a8d8036e51a5dae8a9e6431b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711015"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Falsch konfigurierte Gastbetriebssystem-Firewall der Azure-VM

In diesem Artikel wird beschrieben, wie eine falsch konfigurierte Gastbetriebssystem-Firewall der Azure-VM repariert wird.

## <a name="symptoms"></a>Symptome

1.  Die Willkommensseite des virtuellen Computers (VM) zeigt, dass der virtuelle Computer vollständig geladen wurde.

2.  Je nachdem, wie das Gastbetriebssystem konfiguriert ist, könnte der Netzwerkdatenverkehr teilweise oder gar nicht den virtuellen Computer erreichen.

## <a name="cause"></a>Ursache

Eine fehlerhafte Konfiguration der Gastbetriebssystem-Firewall kann den Netzwerkdatenverkehr mit dem virtuellen Computer teilweise oder völlig blockieren.

## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Verwenden Sie zum Beheben dieses Problems die serielle Konsole, oder [reparieren Sie den virtuellen Computer offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline), indem Sie den Systemdatenträger des virtuellen Computers an einen virtuellen Wiederherstellungscomputer anfügen.

## <a name="online-mitigations"></a>Vorbeugende Maßnahmen online

Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine PowerShell-Instanz](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Ist die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert, gehen Sie zum Abschnitt „Reparieren des virtuellen Computers im Offlinestatus“ des folgenden Azure-Artikels:

 [Interner Fehler beim Herstellen einer Verbindung mit einem virtuellen Azure-Computer über Remotedesktop](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Die folgenden Regeln können bearbeitet werden, um entweder den Zugriff auf den virtuellen Computer (über RDP) zu aktivieren oder eine einfachere Problembehandlung zu ermöglichen:

*   Remotedesktop (TCP eingehend): Dies ist die Standardregel, die primären Zugriff auf den virtuellen Computer bietet, indem RDP in Azure zugelassen wird.

*   Windows-Remoteverwaltung (HTTP eingehend): Mit dieser Regel können Sie die Verbindung mit dem virtuellen Computer mithilfe von PowerShell herstellen. In Azure können Sie mit dieser Art von Zugriff den Skripterstellungsaspekt von Remoteskripterstellung und Problembehandlung verwenden.

*   Datei- und Druckerfreigabe (SMB eingehend): Diese Regel ermöglicht den Zugriff auf die Netzwerkfreigabe als eine Option zur Problembehandlung.

*   Datei- und Druckerfreigabe (Echoanforderung – ICMPv4 eingehend): Mit dieser Regel können Sie den virtuellen Computer pingen.

In der Instanz „Zugriff auf die serielle Konsole“ können Sie den aktuellen Status der Firewallregel abfragen.

*   Verwenden Sie bei der Abfrage den Anzeigenamen als Parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Verwenden Sie bei der Abfrage den von der Anwendung genutzten lokalen Port:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Verwenden Sie bei der Abfrage die von der Anwendung genutzte lokale IP-Adresse:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Wenn Sie sehen, dass die Regel deaktiviert ist, können Sie sie aktivieren, indem Sie den folgenden Befehl ausführen:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   Zur Problembehandlung können Sie die Firewallprofile auf OFF setzen:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Aktivieren Sie in diesem Fall die Firewall nach Abschluss der Problembehandlung erneut, um sie ordnungsgemäß einzurichten.

    > [!Note]
    > Sie müssen den virtuellen Computer nicht neu starten, um diese Änderungen zu übernehmen.

*   Versuchen Sie erneut, eine Verbindung mit dem virtuellen Computer über RDP herzustellen.

### <a name="offline-mitigations"></a>Vorbeugende Maßnahmen offline

1.  Informationen zum Aktivieren und Deaktivieren der Firewallregeln finden Sie unter [Aktivieren oder Deaktivieren einer Firewallregel unter einem Gastbetriebssystem in Azure VM](enable-disable-firewall-rule-guest-os.md).

2.  Überprüfen Sie, ob folgendes Szenario vorliegt: [Gastbetriebssystem-Firewall blockiert eingehenden Datenverkehr](guest-os-firewall-blocking-inbound-traffic.md).

3.  Wenn Sie sich noch nicht sicher sind, ob die Firewall den Zugriff blockiert, handeln Sie gemäß [Deaktivieren der Gastbetriebssystem-Firewall auf der Azure-VM](disable-guest-os-firewall-windows.md), und aktivieren Sie dann die Gastbetriebssystem-Firewall erneut mithilfe der richtigen Regeln.

