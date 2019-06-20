---
title: Remoteverbindung mit virtuellen Azure-Computern kann nicht hergestellt werden, weil die VM im abgesicherten Modus gestartet wird | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie ein Problem behandeln, bei dem eine RDP-Verbindung mit einer VM nicht möglich ist, weil sie im abgesicherten Modus gestartet wird.| Microsoft-Dokumentation
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
ms.openlocfilehash: 8e108d88282894a7b1bf014146083008bedd483d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60319486"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>RDP-Verbindung mit einem virtuellen Computer kann nicht hergestellt werden, weil die VM im abgesicherten Modus gestartet wird

In diesem Artikel wird veranschaulicht, wie Sie ein Problem lösen, bei dem Sie keine Verbindung mit virtuellen Azure-Computern (VMs) herstellen können, weil für die VM das Starten im abgesicherten Modus konfiguriert ist.

> [!NOTE]
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Resource Manager-Bereitstellungsmodells, das anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen empfohlen wird.

## <a name="symptoms"></a>Symptome

Sie können keine RDP-Verbindung oder andere Verbindungen (z.B. per HTTP) mit einer VM in Azure herstellen, weil für die VM das Starten im abgesicherten Modus konfiguriert ist. Wenn Sie sich im Azure-Portal in der [Startdiagnose](../troubleshooting/boot-diagnostics.md) den Screenshot ansehen, fällt Ihnen auf, dass die VM normal startet, aber die Netzwerkschnittstelle nicht verfügbar ist:

![Abbildung zur Netzwerkschnittstelle im abgesicherten Modus](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Ursache

Der RDP-Dienst ist im abgesicherten Modus nicht verfügbar. Wenn die VM im abgesicherten Modus gestartet wird, werden nur wichtige Systemprogramme und Dienste geladen. Dies gilt für die beiden unterschiedlichen Versionen des abgesicherten Modus: „Safe Boot minimal“ (Sicherer Start – minimal) und „Safe Boot with connectivity“ (Sicherer Start mit Konnektivität).


## <a name="solution"></a>Lösung

Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md).

Verwenden Sie zum Beheben dieses Problems die serielle Konsole, um die VM so zu konfigurieren, dass sie im normalen Modus gestartet wird. Sie können die [VM auch offline reparieren](#repair-the-vm-offline), indem Sie einen virtuellen Wiederherstellungscomputer verwenden.

### <a name="use-serial-control"></a>Verwenden der seriellen Konsole

1. Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine CMD-Instanz](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Wenn die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert ist, helfen Ihnen die Informationen unter [Reparieren des virtuellen Computers im Offlinestatus](#repair-the-vm-offline) weiter.
2. Überprüfen Sie die Startkonfigurationsdaten:

        bcdedit /enum

    Wenn für die VM das Starten im abgesicherten Modus konfiguriert ist, wird im Abschnitt **Windows Boot Loader** ein zusätzliches Flag mit dem Namen **safeboot** angezeigt. Wenn das Flag **safeboot** nicht zu sehen ist, befindet sich die VM nicht im abgesicherten Modus. Dieser Artikel gilt nicht für Ihr Szenario.

    Das Flag **safeboot** kann mit den folgenden Werten angezeigt werden:
   - Wenig
   - Netzwerk

     In diesen beiden Modi wird RDP nicht gestartet. Aus diesem Grund bleibt die Behebung unverändert.

     ![Abbildung zum Flag für den abgesicherten Modus](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Löschen Sie das Flag **safeboot**, damit die VM wieder im normalen Modus gestartet wird:

        bcdedit /deletevalue {current} safeboot

4. Überprüfen Sie die Daten der Startkonfiguration, um sicherzustellen, dass das Flag **safeboot** entfernt wurde:

        bcdedit /enum

5. Starten Sie die VM neu, und vergewissern Sie sich, dass das Problem behoben wurde.

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Anfügen des Betriebssystemdatenträgers an eine VM für die Wiederherstellung

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.
3. Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Beachten Sie den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Aktivieren des Sicherungsprotokolls und der seriellen Konsole (optional)

Mit dem Abbildprotokoll und der seriellen Konsole können wir weitere Problembehandlungsschritte ausführen, falls das Problem anhand der Lösung in diesem Artikel nicht behoben werden kann.

Um das Sicherungsprotokoll und die serielle Konsole zu aktivieren, führen Sie das folgende Skript aus.

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**).
2. Führen Sie das folgende Skript aus:

    In diesem Skript wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert für Ihren virtuellen Computer.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>Konfigurieren von Windows für das Starten im normalen Modus

1. Öffnen Sie eine Eingabeaufforderungssitzung mit erhöhten Rechten (**Als Administrator ausführen**).
2. Überprüfen Sie die Startkonfigurationsdaten. Bei den folgenden Befehlen wird davon ausgegangen, dass der dem angefügten Betriebssystemdatenträger zugewiesene Laufwerkbuchstabe „F“ ist. Ersetzen Sie diesen durch den entsprechenden Wert für Ihren virtuellen Computer.

        bcdedit /store F:\boot\bcd /enum
    Notieren Sie sich den Bezeichnernamen der Partition, auf der sich der Ordner **\windows** befindet. Standardmäßig ist der Bezeichnername „Default“ (Standard).

    Wenn für die VM das Starten im abgesicherten Modus konfiguriert ist, wird im Abschnitt **Windows Boot Loader** ein zusätzliches Flag mit dem Namen **safeboot** angezeigt. Wenn das Flag **safeboot** nicht angezeigt wird, gilt dieser Artikel nicht für Ihr Szenario.

    ![Abbildung zum Startbezeichner](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Entfernen Sie das Flag **safeboot**, damit die VM wieder im normalen Modus gestartet wird:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Überprüfen Sie die Daten der Startkonfiguration, um sicherzustellen, dass das Flag **safeboot** entfernt wurde:

        bcdedit /store F:\boot\bcd /enum
5. [Trennen Sie den Betriebssystemdatenträger, und erstellen die VM neu.](../windows/troubleshoot-recovery-disks-portal.md) Überprüfen Sie dann, ob das Problem behoben ist.
