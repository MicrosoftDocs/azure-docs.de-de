---
title: Bluescreenfehler beim Starten einer Azure-VM | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Problem, dass beim Starten Bluescreenfehler angezeigt werden, beheben.
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
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: 26306489b11e24ab50f0ae893f11137d279c6127
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719803"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows zeigt Bluescreenfehler beim Starten einer Azure-VM an
Dieser Artikel beschreibt Bluescreenfehler, die möglicherweise beim Starten eines virtuellen Windows-Computers (VM) in Microsoft Azure angezeigt werden. Er bietet Schritte, mit denen Sie Daten für ein Supportticket sammeln können. 

> [!NOTE] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel beschreibt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptom"></a>Symptom 

Eine Windows-VM wird nicht gestartet. Beim Überprüfen der Startscreenshots unter [Startdiagnose](./boot-diagnostics.md) wird eine der folgenden Fehlermeldungen in einem Bluescreen angezeigt:

- Auf dem PC ist ein Problem aufgetreten. Er muss neu gestartet werden. Es werden einige Fehlerinformationen gesammelt, und dann können Sie einen Neustart ausführen.
- Auf dem PC ist ein Problem aufgetreten. Er muss neu gestartet werden.

In diesem Abschnitt sind die allgemeinen Fehlermeldungen aufgeführt, die bei der Verwaltung Ihrer virtuellen Computer unter Umständen angezeigt werden:

## <a name="cause"></a>Ursache

Es kann mehrere Gründe geben, warum ein Abbruchfehler angezeigt wird. Folgende Ursachen sind am häufigsten anzutreffen:

- Problem mit einem Treiber
- Beschädigte Systemdatei oder beschädigter Arbeitsspeicher
- Eine Anwendung greift auf einen unzulässigen Sektor des Arbeitsspeichers zu

## <a name="collect-memory-dump-file"></a>Sammeln der Speichersicherungsdatei

Um dieses Problem zu beheben, müssten Sie die erste Sicherungsdatei für den Absturz erfassen und an den Support senden. Um die Sicherungsdatei zu sammeln, gehen Sie folgendermaßen vor:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).
2. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md). 
3. Remotedesktopverbindung mit der Wiederherstellungs-VM.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Suche nach der Sicherungsdatei und Senden eines Supporttickets

1. Navigieren Sie auf der Wiederherstellungs-VM zum Windows-Ordner im angefügten Betriebssystemdatenträger. Wenn der Laufwerkbuchstabe, der dem angefügten Betriebssystemdatenträger zugewiesen ist, „F“ lautet, müssen Sie zu „F:\Windows“ navigieren.
2. Suchen Sie nach der Datei „memory.dmp“, und [senden Sie dann ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) mit der Sicherungsdatei. 

Wenn Sie die Sicherungsdatei nicht finden können, fahren Sie mit dem nächsten Schritt zum Aktivieren des Sicherungsprotokolls und der seriellen Konsole fort.

### <a name="enable-dump-log-and-serial-console"></a>Aktivieren des Sicherungsprotokolls und der seriellen Konsole

Um das Sicherungsprotokoll und die serielle Konsole zu aktivieren, führen Sie das folgende Skript aus.

1. Öffnen Sie die Eingabeaufforderungssitzung mit erhöhten Rechten (Als Administrator ausführen).
2. Führen Sie das folgende Skript aus:

    In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie ihn durch den entsprechenden Wert Ihrer VM.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Stellen Sie sicher, dass genügend Speicherplatz auf dem Datenträger vorhanden ist, um genauso viel Arbeitsspeicher wie der RAM zuzuweisen. Dieser hängt von der Größe ab, die Sie für diese VM auswählen.
    2. Wenn nicht genügend Speicherplatz vorhanden ist, oder es sich um eine VM beträchtlicher Größe (der Serie G, GS oder E) handelt, können Sie den Speicherort, in dem diese Datei erstellt wird, ändern und auf einen anderen Datenträger verweisen, der der VM angefügt ist. Hierzu müssen Sie den folgenden Schlüssel ändern:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Trennen Sie den Betriebssystemdatenträger, und fügen Sie ihn wieder an die betroffene VM an](../windows/troubleshoot-recovery-disks-portal.md).
4. Starten Sie die VM, um das Problem zu reproduzieren. Daraufhin wird eine Sicherungsdatei generiert.
5. Fügen Sie den Betriebssystemdatenträger an eine Wiederherstellungs-VM an, sammeln Sie die Sicherungsdatei, und [senden Sie dann ein Supportticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) mit der Sicherungsdatei.



