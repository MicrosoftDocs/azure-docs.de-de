---
title: 'Windows-kritischer Fehler: Hardwarefehler'
description: Dieser Artikel enthält Schritte zur Behebung von Problemen, bei denen virtuelle Computer unter Windows Server 2008 mit einer Fehlermeldung abstürzen, in der auf einen Hardwarefehler hingewiesen wird.
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/13/2020
ms.author: v-mibufo
ms.openlocfilehash: 18622d60f3a33658fadfd28c53c93a07b4b438a9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488645"
---
# <a name="windows-stop-error---hardware-malfunction"></a>Windows-kritischer Fehler: Hardwarefehler

Dieser Artikel enthält Schritte zur Behebung von Problemen, bei denen virtuelle Computer unter Windows Server 2008 mit einer Fehlermeldung abstürzen, in der auf einen Hardwarefehler hingewiesen wird.

## <a name="symptoms"></a>Symptome

Wenn Sie mithilfe der [Startdiagnose](./boot-diagnostics.md) den Screenshot des virtuellen Computers anzeigen, sehen Sie einen Bluescreen mit folgender Meldung:

**\*\*\* Hardware Malfunction**

**Call your vendor for support**

**\*\*\* The system has halted \*\*\*** (Hardwarefehler
Wenden Sie sich an den Hersteller.
Das System wurde angehalten.)

#### <a name="blue-screen"></a>Bluescreen

![Der Screenshot zeigt einen Bluescreen für einen durch einen Hardwarefehler bedingten Absturz.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-1.png)

#### <a name="serial-console"></a>Serielle Konsole

![Der Screenshot zeigt die Meldung „Hardware Malfunction“ (Hardwarefehler) in der seriellen Konsole, sofern dieses Feature aktiviert wurde.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-2.png)

## <a name="cause"></a>Ursache

Dieser Bildschirm wird angezeigt, wenn das Gastbetriebssystem nicht ordnungsgemäß eingerichtet ist und ein nicht maskierbarer Interrupt (NMI) gesendet wurde. In der Fehlermeldung wird darauf hingewiesen, dass durch ein Kernelmodusprogramm eine Ausnahme generiert wurde, die der Handler nicht abgefangen hat. Die generierte Ausnahme kann durch Erfassen eines Speicherabbilds ermittelt werden.

## <a name="solution"></a>Lösung

### <a name="process-overview"></a>Prozessübersicht 

1. Einrichten des Registrierungsschlüssels für den nicht maskierbaren Interrupt (NMI) 
2. Erstellen einer Reparatur-VM und Zugreifen darauf 
3. Aktivieren Sie die serielle Konsole und die Speicherabbilderfassung. 
4. Erstellen Sie die VM neu. 

### <a name="set-up-the-non-maskable-interrupt-nmi-registry-key"></a>Einrichten des Registrierungsschlüssels für den nicht maskierbaren Interrupt (NMI)

1. Starten Sie den virtuellen Computer über das Azure-Portal neu, damit das Gastbetriebssystem normal startet. 
2. Wenn Sie wieder auf den virtuellen Computer zugreifen können, öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten („Als Administrator ausführen“). 
3. Richten Sie mithilfe des folgenden Befehls den NMI-Registrierungsschlüssel ein:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```
    Weitere Informationen zum Befehl „REG ADD“ finden Sie [hier](/windows-server/administration/windows-commands/reg-add).
4. *(Optional)* Richten Sie die Speicherabbilderfassung ein:

    ```
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f  
    REG ADD "HKLM\SYSTEM\CurrentControlSet\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 

    ```
5. *(Optional)* Richten Sie den Zugriff auf die serielle Konsole ein:

    ```
    BCDEDIT /ems {current} on, or bcdedit /ems '{current}' on if you are using PowerShell
    BCDEDIT /emssettings EMSPORT:1 EMSBAUDRATE:115200 
    ```
    Weitere Informationen zum Befehl „BCDEDIT“ finden Sie [hier](/windows-server/administration/windows-commands/bcdedit).
6. Starten Sie den virtuellen Computer mithilfe des folgenden Befehls neu:

    ```
    SHUTDOWN /r /t 0 /f 
    ```
    Weitere Informationen zum Befehl „SHUTDOWN“ finden Sie [hier](/windows-server/administration/windows-commands/shutdown).

> [!IMPORTANT]
> Damit sollte das Problem behoben sein.

> [!NOTE]
> Testen Sie Ihren virtuellen Computer nach dem Neustart, um sich zu vergewissern, dass er normal funktioniert. Sollten weiterhin Probleme auftreten, erhalten Sie im nächsten Abschnitt weitere Informationen.

> [!TIP]
> Es empfiehlt sich, im obigen Abschnitt den Registrierungsschlüssel für den nicht maskierbaren Interrupt (NMI) einzurichten. Falls Ihr virtueller Computer im Anschluss jedoch nicht normal gestartet wurde, wurde die Registrierung des Gastbetriebssystems unter Umständen nicht wie beabsichtigt geändert. In diesem Fall können Sie wie im Anschluss beschrieben vorgehen, um die Registrierungseinstellungen manuell hinzuzufügen.

### <a name="create-and-access-a-repair-vm"></a>Erstellen und Aufrufen einer Reparatur-VM

1. Führen Sie die Schritte 1-3 der [Reparaturbefehle für virtuelle Computer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) aus, um eine Reparatur-VM vorzubereiten.
2. Stellen Sie über eine Remotedesktopverbindung eine Verbindung mit der Reparatur-VM her.

### <a name="enable-serial-console-and-memory-dump-collection"></a>Aktivieren der seriellen Konsole und der Speicherabbilderfassung

Vor der Neuerstellung des virtuellen Computers empfiehlt es sich, die Speicherabbilderfassung und die serielle Konsole zu aktivieren. Führen Sie dazu das folgende Skript aus: 

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (Als Administrator ausführen). 
2. Listen Sie die Daten im BCD-Speicher auf, und bestimmen Sie den Bezeichner des Startladeprogramms, das Sie im nächsten Schritt verwenden. 
    1. Geben Sie für eine VM der Generation 1 den folgenden Befehl ein, und notieren Sie sich den angezeigten Bezeichner: 
 
        ```
        bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
        ```
        Ersetzen Sie im Befehl `<BOOT PARTITON>` durch den Buchstaben der Partition auf dem angefügten Datenträger, der den Startordner enthält. 

        ![Der Screenshot zeigt die Ausgabeliste des BCD-Speichers eines virtuellen Computers der ersten Generation. Die Bezeichnernummer ist hier unter dem Windows-Startladeprogramm aufgeführt.](media/windows-stop-error-hardware-malfunction/windows-stop-error-hardware-malfunction-3.png)
    2. Geben Sie für eine VM der Generation 2 den folgenden Befehl ein, und notieren Sie sich den aufgelisteten Bezeichner:
    
        ```
        BCDEDIT /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum 
        ```
        * Ersetzen Sie im Befehl `<LETTER OF THE EFI SYSTEM PARTITION>` durch den Buchstaben der EFI-Systempartition.
        * Es kann hilfreich sein, die Datenträgerverwaltungskonsole zu starten, um die entsprechende als *EFI-Systempartition* bezeichnete Systempartition zu ermitteln.
        * Beim Bezeichner kann es sich um eine eindeutige GUID oder die Standardpartition *bootmgr* handeln.
3. Führen Sie die folgenden Befehle aus, um die serielle Konsole zu aktivieren:

    ```
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON  
    BCDEDIT /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200 

    ```
    * Ersetzen Sie im Befehl `<VOLUME LETTER WHERE THE BCD FOLDER IS>` durch den Buchstaben des BCD-Ordners.
    * Ersetzen Sie im Befehl `<BOOT LOADER IDENTIFIER>` durch den Bezeichner, den Sie im vorherigen Schritt ermittelt haben.
4. Überprüfen Sie, ob der freie Speicherplatz auf dem Betriebssystemdatenträger größer ist als die Größe des Arbeitsspeichers (RAM) auf der VM. 
    1. Wenn auf dem Betriebssystemdatenträger nicht genügend Speicherplatz zur Verfügung steht, ändern Sie den Ort, an dem die Speicherabbilddatei erstellt wird. Anstatt die Datei auf dem Betriebssystemdatenträger zu erstellen, können Sie sie an jeden anderen Datenträger verweisen, der mit der VM verbunden ist und über ausreichend freien Speicherplatz verfügt. Ersetzen Sie zum Ändern des Speicherorts in den unten aufgeführten Befehlen **%SystemRoot%** durch den Laufwerkbuchstaben des Datenträgers (z. B. **F:** ). 
    2. Geben Sie die folgenden Befehle ein (empfohlene Speicherabbildkonfiguration):

    **Laden der Registrierungsstruktur vom beschädigten Betriebssystem-Datenträger:**

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    **Aktivieren für „ControlSet001“** :

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Aktivieren für „ControlSet002“** :

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
    ```

    **Entladen des beschädigten Betriebssystemdatenträgers**:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```
### <a name="rebuild-the-virtual-machine"></a>Neuerstellung des virtuellen Computers

* Verwenden Sie [Schritt 5 der VM-Reparaturbefehle](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example), um die VM neu zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Problembehandlung bei Startfehlern von Azure-VMs](./boot-error-troubleshoot.md)