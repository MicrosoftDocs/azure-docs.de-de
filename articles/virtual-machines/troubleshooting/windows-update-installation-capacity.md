---
title: 'Beheben von Problemen beim Betriebssystemstart: Windows Update-Installationskapazität'
description: Schritte zur Behebung von Problemen, bei denen ein Fehler bei Windows Update (KB) auftritt und es in einer Azure-VM nicht mehr reagiert.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: f83a1820eb931fa075681da7a9661b304059cd2a
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635704"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Beheben von Problemen beim Betriebssystemstart: Windows Update-Installationskapazität

In diesem Artikel werden Schritte zur Behebung von Problemen in einem virtuellen Azure-Computer (VM) erläutert, bei denen ein Fehler bei Windows Update (KB) auftritt und es nicht mehr reagiert.

## <a name="symptom"></a>Symptom

Wenn Sie die Startdiagnose verwenden, um den Screenshot des virtuellen Computers anzuzeigen, sehen Sie, dass der Screenshot ein aktuell stattfindendes Windows Update (KB) zeigt, das jedoch mit einem Fehlercode fehlschlägt: **C01A001D**. Das folgende Bild zeigt das nicht reagierende Windows Update (KB) mit der Nachricht „Fehler C01A001D beim Anwenden des Updatevorgangs ##### von ##### (######)”:

![Windows Update (KB) reagiert nicht und zeigt die Nachricht „Fehler C01A001D beim Anwenden des Updatevorgangs X von Y (Z)“ an.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Ursache

In dieser Situation ist das Betriebssystem (OS) nicht in der Lage, eine Windows Update (KB)-Installation abzuschließen, da eine Kerndatei nicht im Dateisystem erstellt werden kann. Ausgehend von diesem Fehlercode kann das Betriebssystem überhaupt keine Dateien auf den Datenträger schreiben.

## <a name="solution"></a>Lösung

### <a name="process-overview"></a>Prozessübersicht:

1. Erstellen und Aufrufen einer Reparatur-VM.
1. Freigeben von Speicherplatz auf dem Datenträger.
1. Aktivieren der seriellen Konsole und der Speicherabbilderfassung.
1. Erstellen Sie den virtuellen Computer neu.

> [!NOTE]
> Wenn dieser Fehler auftritt, ist das Gastbetriebssystem nicht funktionsfähig. Behandeln Sie dieses Problem im Offlinemodus, um dieses Problem zu beheben.

### <a name="create-and-access-a-repair-vm"></a>Erstellen einer Reparatur-VM und Zugreifen darauf

1. Führen Sie die Schritte 1-3 der [Reparaturbefehle für virtuelle Computer](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) aus, um eine Reparatur-VM vorzubereiten.
1. Stellen Sie über eine Remotedesktopverbindung eine Verbindung mit der Reparatur-VM her.

### <a name="free-up-space-on-the-disk"></a>Freigeben von Speicherplatz auf der Festplatte

So beheben Sie das Problem:

- Ändern Sie die Größe des Datenträgers auf bis zu 1 TB, wenn er die maximale Größe von 1 TB noch nicht erreicht hat.
- Führen Sie eine Datenträgerbereinigung durch.
- Defragmentieren Sie das Laufwerk.

1. Überprüfen Sie, ob der Datenträger voll ist. Wenn die Größe des Datenträgers unter 1 TB liegt, [erweitern Sie ihn mit PowerShell](../windows/expand-os-disk.md) auf eine maximale Größe von 1 TB.
1. Wenn der Datenträger bereits 1 TB groß ist, müssen Sie eine Datenträgerbereinigung durchführen.
   1. Geben Sie mit dem [Datenträgerbereinigungstool](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) Speicherplatz frei.
1. Wenn Änderung der Größe und Bereinigung abgeschlossen sind, defragmentieren Sie das Laufwerk mit dem folgenden Befehl:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
Je nach dem Grad der Fragmentierung kann die Defragmentierung mehrere Stunden dauern.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>Aktivieren der seriellen Konsole und der Speicherabbilderfassung

**Empfohlen:** Aktivieren Sie die serielle Konsole und die Speicherabbilderfassung, bevor Sie die VM neu erstellen, indem Sie das folgende Skript ausführen:

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten als Administrator.
1. Führen Sie die folgenden Befehle aus:

   **Aktivieren der seriellen Konsole**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Überprüfen Sie, ob der freie Speicherplatz auf dem Betriebssystemdatenträger größer als die Größe des Arbeitsspeichers (RAM) auf der VM ist.

   Wenn nicht genügend Speicherplatz auf dem Betriebssystemdatenträger vorhanden ist, ändern Sie den Speicherort für das Erstellen der Speicherabbilddatei, und verweisen Sie auf einen an die VM angefügten Datenträger, der über genügend freien Speicherplatz verfügt. Um den Speicherort zu ändern, ersetzen Sie in den folgenden Befehlen **%SystemRoot%** durch den Laufwerksbuchstaben des Datenträgers, beispielsweise **F:** .

   Empfohlene Konfiguration für das Aktivieren des Betriebssystemabbilds:

    **Laden des beschädigten Betriebssystemdatenträgers:**

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
   
### <a name="rebuild-the-vm"></a>Neuerstellen der VM

Verwenden Sie [Schritt 5 der VM-Reparaturbefehle](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example), um die VM neu zu erstellen.
