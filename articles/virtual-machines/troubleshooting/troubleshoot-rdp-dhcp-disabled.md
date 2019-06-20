---
title: Remoteverbindung mit Azure Virtual Machines ist nicht möglich, weil DHCP deaktiviert ist | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Problembehandlung für ein RDP-Problem durchführen, das in Microsoft Azure von einem DHCP-Clientdienst verursacht wird. | Microsoft-Dokumentation
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
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: daddb859c6bfc6309ef833c6c6c3ea43c70f1889
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362287"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Eine RDP-Verbindung mit Azure Virtual Machines ist nicht möglich, da der DHCP-Clientdienst deaktiviert ist

In diesem Artikel wird ein Problem beschrieben, bei dem Sie keine Remotedesktopverbindung mit virtuellen Windows-Computern in Azure (VMs) herstellen können, nachdem der DHCP-Clientdienst in der VM deaktiviert wurde.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Symptome
Sie können keine RDP-Verbindung eines virtuellen Computers in Azure herstellen, weil der DHCP-Clientdienst auf dem virtuellen Computer deaktiviert ist. Im Screenshot im Azure-Portal unter [Startdiagnose](../troubleshooting/boot-diagnostics.md) ist zu erkennen, dass die VM normal gestartet und auf dem Anmeldebildschirm auf die Anmeldeinformationen gewartet wird. Sie können die Ereignisprotokolle in der VM mit der Ereignisanzeige remote anzeigen. Sie sehen, dass der DHCP-Clientdienst nicht gestartet wurde oder nicht gestartet werden kann. Es folgt ein Beispielprotokoll:

**Protokollname:** System </br>
**Quelle:** Dienststeuerungs-Manager </br>
**Datum:** 12/16/2015 11:19:36 AM </br>
**Ereignis-ID:** 7022 </br>
**Aufgabenkategorie:** Keine </br>
**Ebene:** Error </br>
**Schlüsselwörter:** Klassisch</br>
**Benutzer:** – </br>
**Computer**: myvm.cosotos.com</br>
**Beschreibung:** Der DHCP-Clientdienst wurde nicht ordnungsgemäß gestartet.</br>

Bei Resource Manager-VMs können Sie die serielle Zugriffskonsole verwenden, um die Ereignisprotokolle 7022 mithilfe des folgenden Befehls abzufragen:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Bei klassischen virtuellen Computern müssen Sie im OFFLINE-Modus arbeiten und die Protokolle manuell sammeln.

## <a name="cause"></a>Ursache

Der DHCP-Clientdienst wird nicht auf der VM ausgeführt.

> [!NOTE]
> Dieser Artikel gilt nur für den DHCP-Clientdienst und nicht den DHCP-Server.

## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Verwenden Sie zum Beheben dieses Problems die serielle Steuerung, um DHCP zu aktivieren oder die [Netzwerkschnittstelle für die VM zurückzusetzen](reset-network-interface.md).

### <a name="use-serial-control"></a>Verwenden der seriellen Konsole

1. Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine CMD-Instanz](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
(Andernfalls wird ein Fehler aufgrund eines Firmwarekonflikts angezeigt: Die Zurücksetzung auf die werkseitigen Einstellungen kann aufgrund eines Konflikts bei den Firmwareversionen nicht fortgesetzt werden.) Wenn die serielle Konsole auf Ihrer VM nicht aktiviert ist, helfen Ihnen die Informationen unter [Zurücksetzen der Netzwerkschnittstelle](reset-network-interface.md) weiter.
2. Überprüfen Sie, ob DHCP für die Netzwerkschnittstelle deaktiviert ist:

        sc query DHCP
3. Wenn DHCP beendet wurde, versuchen Sie, den Dienst zu starten.

        sc start DHCP

4. Fragen Sie den Dienst erneut ab, um sicherzustellen, dass der Dienst erfolgreich gestartet wurde.

        sc query DHCP

    Versuchen Sie, eine Verbindung mit der VM herzustellen, und prüfen Sie, ob das Problem behoben wurde.
5. Wenn der Dienst nicht gestartet wird, verwenden Sie die folgende geeignete Lösung basierend auf der Fehlermeldung, die Sie erhalten haben:

    | Error  |  Lösung |
    |---|---|
    | 5 – ACCESS DENIED  | Weitere Informationen finden Sie unter [DHCP-Clientdienst wird aufgrund eines „Zugriff verweigert“-Fehlers beendet](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT   | Weitere Informationen finden Sie unter [DHCP-Clientdienst stürzt ab oder reagiert nicht](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 – ERROR_SERVICE_DISABLED  | Siehe [DHCP-Clientdienst ist deaktiviert](#dhcp-client-service-is-disabled).  |
    | 1059 – ERROR_CIRCULAR_DEPENDENCY  |[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.   |
    | 1067 – ERROR_PROCESS_ABORTED |Weitere Informationen finden Sie unter [DHCP-Clientdienst stürzt ab oder reagiert nicht](#dhcp-client-service-crashes-or-hangs).   |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL   | [Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.  |
    |1069 – ERROR_SERVICE_LOGON_FAILED   |  Siehe [Fehler beim DHCP-Clientdienst aufgrund eines Anmeldefehlers](#dhcp-client-service-fails-because-of-logon-failure). |
    | 1070 – ERROR_SERVICE_START_HANG  | Weitere Informationen finden Sie unter [DHCP-Clientdienst stürzt ab oder reagiert nicht](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 – ERROR_SERVICE_NEVER_STARTED  | Siehe [DHCP-Clientdienst ist deaktiviert](#dhcp-client-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.  |
    |1\.053 | [Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-Clientdienst wird aufgrund eines „Zugriff verweigert“-Fehlers beendet

1. Stellen Sie eine Verbindung mit der [seriellen Konsole](serial-console-windows.md) her, und öffnen Sie eine PowerShell-Instanz.
2. Laden Sie das Tool Process Monitor herunter, indem Sie das folgende Skript ausführen:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Starten Sie dann eine **procmon**-Ablaufverfolgung:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reproduzieren Sie das Problem, indem Sie den Dienst starten, der die Meldung **Zugriff verweigert** verursacht hat:

   ```
   sc start DHCP
   ```

   Wenn der Fehler auftritt, beenden Sie die Prozessüberwachungs-Ablaufverfolgung:

   ```
   procmon /Terminate
   ```
5. Erfassen Sie die Datei **c:\temp\ProcMonTrace.PML**:

    1. [Fügen Sie einen Datenträger an die VM an.](../windows/attach-managed-disk-portal.md
)
    2. Verwenden Sie die serielle Konsole, um die Datei in das neue Laufwerk zu kopieren. Beispiel: `copy C:\temp\ProcMonTrace.PML F:\`. In diesem Befehl ist „F“ der Laufwerkbuchstabe des angefügten Datenträgers. Ersetzen Sie den Buchstaben durch den entsprechenden Wert.
    3. Trennen Sie das Datenlaufwerk, und fügen Sie es an eine ausgeführte VM an, auf der die Prozessüberwachung „ubstakke“ installiert ist.

6. Öffnen Sie **ProcMonTrace.PML** mit der Prozessüberwachung auf der ausgeführten VM. Filtern Sie wie im folgenden Screenshot nach  **Ergebnis ist Zugriff verweigert**:

    ![Filtern nach Ergebnissen in der Prozessüberwachung](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Beheben Sie Probleme mit den Registrierungsschlüsseln, Ordnern oder Dateien, die in der Ausgabe aufgeführt sind. Normalerweise tritt dieses Problem auf, wenn das im Dienst verwendete Anmeldekonto keine ACL-Berechtigung für den Zugriff auf diese Objekte hat. Um die entsprechende ACL-Berechtigung für das Anmeldekonto zu bestimmen, können Sie auf einer fehlerfreien VM suchen.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-Clientdienst ist deaktiviert

1. Stellen Sie den Standardstartwert des Diensts wieder her:

   ```
   sc config DHCP start= auto
   ```

2. Starten Sie den Dienst:

   ```
   sc start DHCP
   ```

3. Fragen Sie den Dienststatus erneut ab, um sicherzustellen, dass der Dienst ausgeführt wird:

   ```
   sc query DHCP
   ```

4. Versuchen Sie, über Remotedesktop eine Verbindung mit der VM herzustellen.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>Fehler beim DHCP-Clientdienst aufgrund eines Anmeldefehlers

1. Da dieses Problem auftritt, wenn das Startkonto dieses Diensts geändert wurde, stellen Sie den Standardstatus dieses Kontos wieder her:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Starten Sie den Dienst:

        sc start DHCP
3. Versuchen Sie, über Remotedesktop eine Verbindung mit der VM herzustellen.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-Clientdienst stürzt ab oder reagiert nicht

1. Wenn der Dienst im Status **Wird gestartet** oder **Wird beendet** verbleibt, versuchen Sie, den Dienst zu beenden:

        sc stop DHCP
2. Isolieren Sie den Dienst auf seinem eigenen „svchost“-Container:

        sc config DHCP type= own
3. Starten Sie den Dienst:

        sc start DHCP
4. Wenn der Dienst noch immer nicht startet, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her. Stellen Sie sicher, dass der angefügte Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.
3.  Öffnen Sie eine Eingabeaufforderungsinstanz mit erhöhten Rechten (**Als Administrator ausführen**). Führen Sie dann das folgende Skript aus. Dieses Skript setzt voraus, dass der Laufwerkbuchstabe, der dem angefügten Betriebssystemdatenträger zugeordnet ist, **F** ist. Ersetzen Sie den Buchstaben durch den entsprechenden Wert auf dem virtuellen Computer.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Trennen Sie den Betriebssystemdatenträger, und erstellen die VM neu.](../windows/troubleshoot-recovery-disks-portal.md) Überprüfen Sie dann, ob das Problem behoben ist.

## <a name="next-steps"></a>Nächste Schritte

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem zu beheben.