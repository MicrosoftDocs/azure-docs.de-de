---
title: CRITICAL SERVICE FAILED (FEHLER BEIM KRITISCHEN DIENST) beim Starten eines virtuellen Azure-Computers | Microsoft-Dokumentation
description: 'Informationen zum Beheben des Fehlers „0x0000005A-CRITICAL SERVICE FAILED“ (0x0000005A: FEHLER BEIM KRITISCHEN DIENST), der beim Start auftritt | Microsoft-Dokumentation'
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
ms.date: 10/08/2018
ms.author: genli
ms.openlocfilehash: e828a8fc4211a0f0c4b53a9e18fa1c2fb6f6916b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60593222"
---
# <a name="windows-shows-critical-service-failed-on-blue-screen-when-booting-an-azure-vm"></a>Unter Windows wird beim Starten eines virtuellen Azure-Computers der Bluescreenfehler „CRITICAL SERVICE FAILED“ (FEHLER BEIM KRITISCHEN DIENST) angezeigt.
Dieser Artikel beschreibt den Fehler „CRITICAL SERVICE FAILED“ (FEHLER BEIM KRITISCHEN DIENST), der unter Umständen beim Starten eines virtuellen Windows-Computers in Microsoft Azure angezeigt wird. Er enthält Schritte zum Beheben des Problems. 

> [!NOTE] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel beschreibt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptom"></a>Symptom 

Eine Windows-VM wird nicht gestartet. Beim Überprüfen der Startscreenshots unter [Startdiagnose](./boot-diagnostics.md) wird eine der folgenden Fehlermeldungen auf einem Bluescreen angezeigt:

- „Your PC ran into a problem and needs to restart. You can restart. For more information about this issue and possible fixes, visit http://windows.com/stopcode. Wenn Sie sich an den Support wenden, stellen Sie die folgenden Informationen bereit: Stillstandcode: CRITICAL SERVICE FAILED (FEHLER BEIM KRITISCHEN DIENST) 
- „Your PC ran into a problem and needs to restart. We're just collecting some error info, and then we'll restart for you. If you'd like to know more, you can search online later for this error: CRITICAL_SERVICE_FAILED“ (Auf dem PC ist ein Fehler aufgetreten. Er muss neu gestartet werden. Wir sammeln Fehlerinformationen und starten dann den PC für Sie neu. Wenn Sie weitere Informationen benötigen, können Sie später online nach dem folgenden Fehler suchen: CRITICAL_SERVICE_FAILED)

## <a name="cause"></a>Ursache

Es gibt verschiedene Gründe für Abbruchfehler. Folgende Ursachen sind am häufigsten anzutreffen:
- Problem mit einem Treiber
- Beschädigte Systemdatei oder beschädigter Arbeitsspeicher
- Zugriff auf einen unzulässigen Sektor des Arbeitsspeichers durch eine Anwendung

## <a name="solution"></a>Lösung 

Um das Problem zu beheben, [wenden Sie sich an den Support, und senden Sie ihm eine Dumpdatei](./troubleshoot-common-blue-screen-error.md#collect-memory-dump-file), mit der das Problem schneller diagnostiziert werden kann. Probieren Sie alternativ die folgende Lösung zur Selbsthilfe aus:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).
2. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](./troubleshoot-recovery-disks-portal-windows.md). 
3. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

### <a name="enable-dump-logs-and-serial-console"></a>Aktivieren der Abbildprotokolle und der seriellen Konsole

Das Abbildprotokoll und die [serielle Konsole](./serial-console-windows.md) helfen uns bei der weiteren Problembehandlung.

Um die Abbildprotokolle und die serielle Konsole zu aktivieren, führen Sie das folgende Skript aus.

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (Als Administrator ausführen).
2. Führen Sie das folgende Skript aus:

    In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie ihn durch den entsprechenden Wert Ihres virtuellen Computers.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 10
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 2 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

### <a name="replace-the-unsigned-drivers"></a>Ersetzen der nicht signierten Treiber

1. Führen Sie auf dem virtuellen Wiederherstellungscomputer den folgenden Befehl in einer Eingabeaufforderung mit erhöhten Rechten aus. Mit diesem Befehl wird festgelegt, dass der betroffene Betriebssystemdatenträger beim nächsten Start im abgesicherten Modus gestartet wird:

        bcdedit /store <OS DISK you attached>:\boot\bcd /set {default} safeboot minimal

    Ist der angefügte Betriebssystemdatenträger beispielsweise „F“ ist, führen Sie den folgenden Befehl aus:

        bcdedit /store F: boot\bcd /set {default} safeboot minimal

2. [Trennen Sie den Betriebssystemdatenträger, und fügen Sie ihn dann erneut an den betroffenen virtuellen Computer an.](troubleshoot-recovery-disks-portal-windows.md) Der virtuelle Computer wird im abgesicherten Modus gestartet. Tritt der Fehler weiterhin auf, fahren Sie mit dem optionalen Schritt fort.
3. Öffnen Sie das Feld **Ausführen**,und führen Sie die **Überprüfung** aus, um den Treiberüberprüfungs-Manager zu starten.
4. Aktivieren Sie **Nicht signierte Treiber automatisch wählen**, und klicken Sie dann auf **Weiter**.
5. Daraufhin wird die Liste der unsignierten Treiberdateien ausgegeben. Notieren Sie die Dateinamen.
6. Kopieren Sie die gleichen Versionen dieser Dateien von einem funktionierenden virtuellen Computer, und ersetzen Sie die unsignierten Dateien. 

7. Entfernen Sie die Einstellungen für den sicheren Start:

        bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} safeboot
8.  Starten Sie den virtuellen Computer neu. 

### <a name="optional-analyze-the-dump-logs-in-dump-crash-mode"></a>Optional: Analysieren der Sicherungsprotokolle im Modus für Absturzabbilder

Führen Sie die folgenden Schritte aus, um die Absturzabbilder selbst zu analysieren:

1. Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an.
2. Navigieren Sie auf dem angefügten Betriebssystemdatenträger zu **\windows\system32\config**. Kopieren Sie alle Dateien als Sicherung für den Fall, dass ein Rollback erforderlich ist.
3. Starten Sie den **Registrierungs-Editor** (regedit.exe).
4. Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE**. Wählen Sie im Menü **Datei** > **Struktur laden**.
5. Navigieren Sie auf dem angefügten Betriebssystemdatenträger zum Ordner **\windows\system32\config\SYSTEM**. Geben Sie beim Namen der Struktur **BROKENSYSTEM** ein. Die neue Registrierungsstruktur wird unter dem Schlüssel **HKEY_LOCAL_MACHINE** angezeigt.
6. Navigieren Sie zu **HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Control\CrashControl**, und nehmen Sie die folgenden Änderungen vor:

    Autoreboot = 0

    CrashDumpEnabled = 2
7.  Wählen Sie **BROKENSYSTEM**. Wählen Sie im Menü **Datei** > **Struktur laden**.
8.  Ändern Sie das BCD-Setup zum Starten im Debugmodus. Führen Sie die folgenden Befehle in einer Eingabeaufforderung mit erhöhten Rechten aus:

    ```cmd
    REM Setup some debugging flags on the boot manager
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} default {<IDENTIFIER OF THE WINDOWS BOOT LOADER>}
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices disable

    REM Setup some debugging flags on the boot loader
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootlog yes
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy displayallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} nointegritychecks on
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} testsigning off
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservices disable
    ```
9. [Trennen Sie den Betriebssystemdatenträger, und fügen Sie ihn dann erneut an den betroffenen virtuellen Computer an.](troubleshoot-recovery-disks-portal-windows.md)
10. Starten Sie den virtuellen Computer, um festzustellen, ob eine Absturzabbildanalyse angezeigt wird. Suchen Sie die Datei, die nicht geladen wurde. Sie müssen diese Datei durch eine Datei vom funktionierenden virtuellen Computer ersetzen. 

    Nachfolgend sehen Sie ein Beispiel für die Absturzabbildanalyse. Sie sehen, dass der**FEHLER** in „filecrypt.sys“ vorliegt: "FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys".

    ```
    kd> !analyze -v 

    ******************************************************************************* 
    * * * Bugcheck Analysis * * * 

    ******************************************************************************* 
    CRITICAL_SERVICE_FAILED (5a) Arguments: Arg1: 0000000000000001 Arg2: ffffd80f4bfe7070 Arg3: ffffb00b0513d320 Arg4: ffffffffc0000428 Debugging 
    Details: ------------------ 
    DUMP_CLASS: 1 DUMP_QUALIFIER: 401 BUILD_VERSION_STRING: 10.0.14393.1770 (rs1_release.170917-1700) 
    DUMP_TYPE: 1 BUGCHECK_P1: 1 BUGCHECK_P2: ffffd80f4bfe7070 BUGCHECK_P3: ffffb00b0513d320 BUGCHECK_P4: ffffffffc0000428 BUGCHECK_STR: 0x5A_c0000428 
    CPU_COUNT: 1 CPU_MHZ: a22 CPU_VENDOR: GenuineIntel CPU_FAMILY: 6 CPU_MODEL: 3d CPU_STEPPING: 4 DEFAULT_BUCKET_ID: WIN8_DRIVER_FAULT 
    PROCESS_NAME: System CURRENT_IRQL: 0 ANALYSIS_SESSION_HOST: MININT-6RMM091 ANALYSIS_SESSION_TIME: 11-15-2017 19:32:42.0841 
    ANALYSIS_VERSION: 10.0.16361.1001 amd64fre STACK_TEXT: ffffc701`1dc74948 fffff803`b2ff4b4a : 00000000`0000005a 00000000`00000001 ffffd80f`4bfe7070 ffffb00b`0513d320 : nt!KeBugCheckEx [d:\rs1\minkernel\ntos\ke\amd64\procstat.asm @ 127] ffffc701`1dc74950 fffff803`b3205df3 : ffffd80f`4bba9f58 ffffd80f`4bba9f58 ffffc701`1dc74b80 ffffd80f`00000006 : nt!IopLoadDriver+0x19f8e6 [d:\rs1\minkernel\ntos\ke\amd64\threadbg.asm @ 81] 
    RETRACER_ANALYSIS_TAG_STATUS: DEBUG_FLR_FAULTING_IP is not found THREAD_SHA1_HASH_MOD_FUNC: eb79608c07faa1af62c0e61f25ff6bc1d6dfdb25 THREAD_SHA1_HASH_MOD_FUNC_OFFSET: 96a3a314834bb4e8443a8b7201525fc5dfc1878b THREAD_SHA1_HASH_MOD: 30a3e915496deaace47137d5b90c3ecc03746bf6 FOLLOWUP_NAME: wintriag
    MODULE_NAME: filecrypt IMAGE_NAME: filecrypt.sys DEBUG_FLR_IMAGE_TIMESTAMP: 0 IMAGE_VERSION: STACK_COMMAND: .thread ; .cxr ; kb FAILURE_BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys BUCKET_ID: 0x5A_c0000428_IMAGE_filecrypt.sys PRIMARY_PROBLEM_CLASS: 0x5A_c0000428_IMAGE_filecrypt.sys TARGET_TIME: 2017-11-13T20:51:04.000Z OSBUILD: 14393 OSSERVICEPACK: 1770 SERVICEPACK_NUMBER: 0 OS_REVISION: 0 SUITE_MASK: 144 PRODUCT_TYPE: 3 OSPLATFORM_TYPE: x64 OSNAME: Windows 10 OSEDITION: Windows 10 Server TerminalServer DataCenter OS_LOCALE: USER_LCID: 0 OSBUILD_TIMESTAMP: 2017-09-17 19:16:08 BUILDDATESTAMP_STR: 170917-1700 BUILDLAB_STR: rs1_release BUILDOSVER_STR: 10.0.14393.1770 ANALYSIS_SESSION_ELAPSED_TIME: bfc ANALYSIS_SOURCE: KM FAILURE_ID_HASH_STRING: km:0x5a_c0000428_image_filecrypt.sys FAILURE_ID_HASH: {35f25777-b01e-70a1-c502-f690dab6cb3a} FAILURE_ID_REPORT_LINK: https://go.microsoft.com/fwlink/?LinkID=397724&FailureHash=35f25777-b01e-70a1-c502-f690dab6cb3a
    ```

11. Entfernen Sie die Einstellungen für Absturzabbilder, wenn der virtuelle Computer normal funktioniert und startet:

    ```cmd
    REM Restore the boot manager to default values
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {bootmgr} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {bootmgr} integrityservices enable

    REM Restore the boot loader to default values for an azure VM
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} bootlog
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} bootstatuspolicy ignoreallfailures
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} nointegritychecks
    bcdedit /store <OS DISK LETTER>:\boot\bcd /deletevalue {default} testsigning
    bcddit /store <OS DISK LETTER>:\boot\bcd /set {default} recoveryenabled no
    bcdedit /store <OS DISK LETTER>:\boot\bcd /set {default} integrityservicesenable
    ```
