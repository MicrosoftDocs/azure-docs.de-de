---
title: Beheben von allgemeinen RDP-Fehlern auf einer Windows-VM in Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen allgemeinen RDP-Fehler auf einer Windows-VM in Azure beheben.
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f290a7e16938c66d45fab9b78086f77bfdfe4839
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319512"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Beheben eines allgemeinen RDP-Fehlers auf einer Azure-VM

Dieser Artikel beschreibt einen allgemeinen Fehler, der möglicherweise auftritt, wenn Sie über das Remotedesktopprotokoll (RDP) eine Verbindung mit einer Windows-VM in Azure herstellen.

## <a name="symptom"></a>Symptom

Wenn Sie eine RDP-Verbindung mit einer Windows-VM in Azure herstellen, erhalten Sie möglicherweise folgende allgemeine Fehlermeldung:

**Remotedesktop kann aus einem der folgenden Gründe keine Verbindung mit dem Remotecomputer herstellen:**

1. **Der Remotezugriff auf den Server ist nicht aktiviert.**

2. **Der Remotecomputer ist ausgeschaltet.**

3. **Der Remotecomputer ist im Netzwerk nicht verfügbar.**

**Stellen Sie sicher, dass der Remotecomputer eingeschaltet und mit dem Netzwerk verbunden ist und dass der Remotezugriff aktiviert ist.**

## <a name="cause"></a>Ursache

Dieses Problem kann aus folgenden Gründen auftreten:

### <a name="cause-1"></a>Ursache 1

Die RDP-Komponente ist deaktiviert:

- Auf Komponentenebene
- Auf Listenerebene
- Auf dem Terminalserver
- In der Rolle „Remotedesktop-Sitzungshost“

### <a name="cause-2"></a>Ursache 2

Die Remotedesktopdienste (TermService) werden nicht ausgeführt.

### <a name="cause-3"></a>Ursache 3

Der RDP-Listener ist falsch konfiguriert.

## <a name="solution"></a>Lösung

Um dieses Problem zu lösen [sichern Sie den Betriebssystemdatenträger](../windows/snapshot-copy-managed-disk.md), [fügen Sie den Betriebssystemdatenträger an eine Wiederherstellungs-VM an](troubleshoot-recovery-disks-portal-windows.md), und führen Sie dann die folgenden Schritte aus.

### <a name="serial-console"></a>Serielle Konsole

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Schritt 1: Öffnen einer CMD-Instanz in einer seriellen Konsole

1. Greifen Sie auf die [serielle Konsole](serial-console-windows.md) zu, indem Sie **Support und Problembehandlung** > **Serielle Konsole (Vorschau)** auswählen. Wenn dieses Feature auf der VM aktiviert ist, können Sie erfolgreich eine Verbindung mit der VM herstellen.

2. Erstellen Sie einen neuen Kanal für eine CMD-Instanz. Geben Sie **CMD** ein, um den Kanal zu starten und den Kanalnamen abzurufen.

3. Wechseln Sie zum Kanal, der die CMD-Instanz ausführt. Im vorliegenden Fall sollte dies Kanal 1 sein.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Schritt 2: Überprüfen der Werte des RDP-Registrierungsschlüssels:

1. Überprüfen Sie, ob das RDP durch Richtlinien deaktiviert wurde.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Wenn eine Domänenrichtlinie existiert, ist das Setup in der lokalen Richtlinie außer Kraft gesetzt.
      - Wenn die Domänenrichtlinie angibt, dass RDP deaktiviert (1) ist, aktualisieren Sie die AD-Richtlinie im Domänencontroller.
      - Wenn die Domänenrichtlinie angibt, dass RDP aktiviert (0) ist, ist kein Update erforderlich.
      - Wenn keine Domänenrichtlinie vorhanden ist und die lokale Richtlinie angibt, dass RDP deaktiviert (1) ist, aktivieren Sie RDP mithilfe des folgenden Befehls: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Überprüfen Sie die aktuelle Konfiguration des Terminalservers.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Wenn der Befehl „0“ zurückgibt, ist der Terminalserver deaktiviert. Aktivieren Sie in diesem Fall den Terminalserver wie folgt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Das Terminalservermodul ist auf den Ausgleichsmodus festgelegt, wenn sich der Server in einer Terminalserverfarm (RDS oder Citrix) befindet. Überprüfen Sie den aktuellen Modus des Terminalservermoduls.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Wenn der Befehl „1“ zurückgibt, ist das Terminalservermodul auf den Ausgleichsmodus festgelegt. Legen Sie das Modul anschließend wie folgt auf den Funktionsmodus fest:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Überprüfen Sie, ob Sie eine Verbindung mit dem Terminalserver herstellen können.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Wenn der Befehl „1“ zurückgibt, können Sie keine Verbindung mit dem Terminalserver herstellen. Aktivieren Sie in diesem Fall die Verbindung wie folgt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Überprüfen Sie die aktuelle Konfiguration des RDP-Listeners.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Wenn der Befehl „0“ zurückgibt, ist der RDP-Listener deaktiviert. Aktivieren Sie in diesem Fall den Listener wie folgt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Überprüfen Sie, ob Sie eine Verbindung mit dem RDP-Listener herstellen können.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Wenn der Befehl „1“ zurückgibt, können Sie keine Verbindung mit dem RDP-Listener herstellen. Aktivieren Sie in diesem Fall die Verbindung wie folgt:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Starten Sie den virtuellen Computer neu.

8. Beenden Sie die CMD-Instanz durch Eingabe von `exit`, und drücken Sie dann zweimal die **EINGABETASTE**.

9. Starten Sie die VM neu, indem Sie `restart` eingeben, und stellen Sie dann eine Verbindung mit der VM her.

Wenn das Problem weiterhin auftritt, fahren Sie mit Schritt 2 fort.

#### <a name="step-2-enable-remote-desktop-services"></a>Schritt 2: Remotedesktopdienste aktivieren

Weitere Informationen finden Sie unter [Remotedesktopdienste für eine Azure-VM werden nicht gestartet](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Schritt 3: RDP-Listener zurücksetzen

Weitere Informationen finden Sie unter [Häufiges Trennen der Remotedesktopverbindung auf einem virtuellen Azure-Computer](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offlinereparatur

#### <a name="step-1-turn-on-remote-desktop"></a>Schritt 1: Aktivieren des Remotedesktops

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.
3. Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Notieren Sie sich den Laufwerkbuchstaben des angefügten Betriebssystemdatenträgers.
4. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.
5. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**). Führen Sie die folgenden Skripts aus. In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert für Ihren virtuellen Computer.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Wenn die VM einer Domäne beigetreten ist, überprüfen Sie den folgenden Registrierungsschlüssel, um zu ermitteln, ob eine Gruppenrichtlinie vorhanden ist, die RDP deaktiviert. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Wenn der Wert dieses Schlüssel „1“ lautet, bedeutet dies, dass RDP von der Richtlinie deaktiviert wurde. Um Remotedesktop über die GPO-Richtlinie zu aktivieren, ändern Sie die folgende Richtlinie auf dem Domänencontroller:

   
      **Computerkonfiguration\Richtlinien\Administrative Vorlagen:**

      Richtliniendefinitionen\Windows-Komponenten\Remotedesktopdienste\Remotedesktop-Sitzungshost\Verbindungen\Remoteverbindungen für Benutzer mithilfe der Remotedesktopdienste zulassen
  
1. Trennen Sie den Datenträger von der Wiederherstellungs-VM.
1. [Erstellen Sie eine neue VM auf Grundlage des Datenträgers](../windows/create-vm-specialized.md).

Wenn das Problem weiterhin auftritt, fahren Sie mit Schritt 2 fort.

#### <a name="step-2-enable-remote-desktop-services"></a>Schritt 2: Remotedesktopdienste aktivieren

Weitere Informationen finden Sie unter [Remotedesktopdienste für eine Azure-VM werden nicht gestartet](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Schritt 3: RDP-Listener zurücksetzen

Weitere Informationen finden Sie unter [Häufiges Trennen der Remotedesktopverbindung auf einem virtuellen Azure-Computer](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.
