---
title: Remotedesktopdienste für eine Azure-VM werden nicht gestartet | Microsoft-Dokumentation
description: Informationen zur Problembehandlung für Remotedesktopdienste beim Verbinden mit einer VM |Microsoft-Dokumentation
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5458a02c09a3600875c7300b27c5a87a735b2f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318880"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Remotedesktopdienste für eine Azure-VM werden nicht gestartet

Dieser Artikel beschreibt, wie Sie Probleme beim Verbinden mit einem virtuellen Azure-Computer (Azure-VM) beheben, wenn die Remotedesktopdienste bzw. TermService nicht starten/startet oder ein Startfehler auftritt.

> [!NOTE]  
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Azure Resource Manager und klassische Bereitstellung](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Resource Manager-Bereitstellungsmodells. Es wird empfohlen, dieses Modell anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen zu verwenden.

## <a name="symptoms"></a>Symptome

Wenn Sie versuchen, eine Verbindung mit einer VM herzustellen, beobachten Sie dieses Verhalten:

- Der VM-Screenshot zeigt, dass das Betriebssystem vollständig geladen ist und auf Anmeldeinformationen wartet.

    ![Screenshot des VM-Status](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Sie können die Ereignisprotokolle in der VM mit der Ereignisanzeige remote anzeigen. Sie sehen, dass die Remotedesktopdienste bzw. TermService nicht starten/startet oder ein Startfehler auftritt. Dieses Protokoll dienst als Beispiel:

    **Protokollname:**      System </br>
    **Quelle:**        Dienststeuerungs-Manager </br>
    **Datum:**          12/16/2017 11:19:36 AM</br>
    **Ereignis-ID:**      7022</br>
    **Aufgabenkategorie:** Keine</br>
    **Ebene:**         Error</br>
    **Schlüsselwörter:**      Klassisch</br>
    **Benutzer:**          –</br>
    **Computer:**      vm.contoso.com</br>
    **Beschreibung:** Die Remotedesktopdienste wurden nicht ordnungsgemäß gestartet. 

    Sie können diese Fehler auch über die serielle Zugriffskonsole suchen. Führen Sie dazu die folgende Abfrage aus: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Ursache
 
Dieses Problem tritt auf, weil die Remotedesktopdienste auf dem virtuellen Computer nicht ausgeführt werden. Dies kann folgende Ursachen haben: 

- Der TermService-Dienst ist auf **Deaktiviert** festgelegt. 
- Der TermService-Dienst ist abgestürzt oder reagiert nicht. 
- TermService kann aufgrund einer falschen Konfiguration nicht gestartet werden.

## <a name="solution"></a>Lösung

Verwenden Sie die serielle Konsole, um dieses Problem zu beheben. Alternativ können Sie die [VM offline reparieren](#repair-the-vm-offline), indem Sie den Betriebssystemdatenträger der VM an eine Wiederherstellungs-VM anfügen.

### <a name="use-serial-console"></a>Verwenden der seriellen Konsole

1. Greifen Sie auf die [serielle Konsole](serial-console-windows.md) zu, indem Sie **Support und Problembehandlung** > **Serielle Konsole** auswählen. Wenn dieses Feature auf der VM aktiviert ist, können Sie erfolgreich eine Verbindung mit der VM herstellen.

2. Erstellen Sie einen neuen Kanal für eine CMD-Instanz. Geben Sie **CMD** ein, um den Kanal zu starten und den Kanalnamen abzurufen.

3. Wechseln Sie zu dem Kanal, in dem die CMD-Instanz ausgeführt wird. In diesem Fall ist das Kanal 1:

   ```
   ch -si 1
   ```

4. Drücken Sie erneut die **EINGABETASTE**, und geben Sie einen gültigen Benutzernamen, ein Kennwort sowie eine lokale oder Domänen-ID für die VM ein.

5. Fragen Sie den Status des TermService-Diensts ab:

   ```
   sc query TermService
   ```

6. Wenn als Dienststatus **Beendet** angezeigt wird, versuchen Sie, den Dienst zu starten:

    ```
    sc start TermService
     ``` 

7. Fragen Sie den Dienst erneut ab, um sicherzustellen, dass der Dienst erfolgreich gestartet wurde:

   ```
   sc query TermService
   ```
8. Wenn der Dienst nicht gestartet wird, befolgen Sie je nach der erhaltenen Fehlermeldung die entsprechende Anweisung zur Problembehandlung:

    |  Error |  Vorschlag |
    |---|---|
    |5 – ACCESS DENIED |Weitere Informationen finden Sie unter [TermService-Dienst wird aufgrund eines „Zugriff verweigert“-Fehlers beendet](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT  |Weitere Informationen finden Sie unter [TermService-Dienst ist deaktiviert](#termservice-service-is-disabled).  |  
    |1058 – ERROR_SERVICE_DISABLED  |Weitere Informationen finden Sie unter [TermService-Dienst stürzt ab oder reagiert nicht](#termservice-service-crashes-or-hangs).  |
    |1059 – ERROR_CIRCULAR_DEPENDENCY |[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.|
    |1067 – ERROR_PROCESS_ABORTED  |Weitere Informationen finden Sie unter [TermService-Dienst stürzt ab oder reagiert nicht](#termservice-service-crashes-or-hangs).  |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL|[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.|
    |1069 – ERROR_SERVICE_LOGON_FAILED  |Weitere Informationen finden Sie unter [Fehler beim TermService-Dienst aufgrund eines Anmeldefehlers](#termservice-service-fails-because-of-logon-failure). |
    |1070 – ERROR_SERVICE_START_HANG   | Weitere Informationen finden Sie unter [TermService-Dienst stürzt ab oder reagiert nicht](#termservice-service-crashes-or-hangs). |
    |1077 – ERROR_SERVICE_NEVER_STARTED   | Weitere Informationen finden Sie unter [TermService-Dienst ist deaktiviert](#termservice-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben. |
    |1753   |[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell zu beheben.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService-Dienst wird aufgrund eines „Zugriff verweigert“-Fehlers beendet

1. Stellen Sie eine Verbindung mit der [seriellen Konsole](serial-console-windows.md) her, und öffnen Sie eine PowerShell-Instanz.
2. Laden Sie das Tool Process Monitor herunter, indem Sie das folgende Skript ausführen:

   ```
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

4. Reproduzieren Sie das Problem, indem Sie den Dienst starten, der den Fehler **Zugriff verweigert** ausgibt: 

   ```
   sc start TermService 
   ```

   Wenn der Fehler auftritt, beenden Sie die Prozessüberwachungs-Ablaufverfolgung:

   ```   
   procmon /Terminate 
   ```

5. Erfassen Sie die Datei  **c:\temp\ProcMonTrace.PML**:

    1. [Fügen Sie einen Datenträger an die VM an.](../windows/attach-managed-disk-portal.md
)
    2. Verwenden Sie die serielle Konsole, um die Datei in das neue Laufwerk zu kopieren. Beispiel: `copy C:\temp\ProcMonTrace.PML F:\`. In diesem Befehl ist „F“ der Laufwerkbuchstabe des angefügten Datenträgers.
    3. Trennen Sie das Datenlaufwerk, und fügen Sie es an eine ausgeführte VM an, auf der die Prozessüberwachung „ubstakke“ installiert ist.

6. Öffnen Sie **ProcMonTrace.PML** mit der Prozessüberwachung der ausgeführten VM. Filtern Sie wie im folgenden Screenshot nach  **Ergebnis ist Zugriff verweigert**:

    ![Filtern nach Ergebnissen in der Prozessüberwachung](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Beheben Sie Probleme mit den Registrierungsschlüsseln, Ordnern oder Dateien, die in der Ausgabe aufgeführt sind. Normalerweise tritt dieses Problem auf, wenn das im Dienst verwendete Anmeldekonto keine ACL-Berechtigung für den Zugriff auf diese Objekte hat. Nach der entsprechenden ACL-Berechtigung für das Anmeldekonto können Sie auf einer fehlerfreien VM suchen. 

#### <a name="termservice-service-is-disabled"></a>TermService-Dienst ist deaktiviert

1. Stellen Sie den Standardstartwert des Diensts wieder her:

   ```
   sc config TermService start= demand 
   ```

2. Starten Sie den Dienst:

   ```
   sc start TermService
   ```

3. Fragen Sie den Status erneut ab, um sicherzustellen, dass der Dienst ausgeführt wird:

   ```
   sc query TermService 
   ```

4. Versuchen Sie, über Remotedesktop eine Verbindung mit der VM herzustellen.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Fehler beim TermService-Dienst aufgrund eines Anmeldefehlers

1. Dieses Problem tritt auf, wenn das Startkonto des Diensts geändert wurde. Stellen Sie dafür den Standardwert wieder her: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Starten Sie den Dienst:

        sc start TermService
3. Versuchen Sie, über Remotedesktop eine Verbindung mit der VM herzustellen.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService-Dienst stürzt ab oder reagiert nicht
1. Wenn der Dienst im Status **Wird gestartet** oder **Wird beendet** verbleibt, beenden Sie den Dienst: 

        sc stop TermService
2. Isolieren Sie den Dienst auf seinem eigenen „svchost“-Container:

        sc config TermService type= own
3. Starten Sie den Dienst:

        sc start TermService
4. Wenn der Dienst noch immer nicht startet, [wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her. Stellen Sie sicher, dass der angefügte Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.
3. Öffnen Sie eine Eingabeaufforderungsinstanz mit erhöhten Rechten (**Als Administrator ausführen**). Führen Sie dann das folgende Skript aus. Es wird angenommen, dass der Laufwerkbuchstabe, der dem angefügten Betriebssystemdatenträger zugeordnet ist, **F** ist. Ersetzen Sie ihn durch den entsprechenden Wert in Ihrer VM. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Trennen Sie den Betriebssystemdatenträger, und erstellen die VM neu.](../windows/troubleshoot-recovery-disks-portal.md) Überprüfen Sie dann, ob das Problem behoben ist.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem zu beheben.
