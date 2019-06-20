---
title: Häufiges Trennen der Remotedesktopverbindung auf einem virtuellen Azure-Computer | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie bei häufigem Trennen der Remotedesktopverbindung auf dem virtuellen Azure-Computer eine Problembehandlung durchführen.
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
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: 7fecf8c5fdafb64f7922054dd2bb9755b0dec031
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60386175"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Häufiges Trennen der Remotedesktopverbindung auf einem virtuellen Azure-Computer

In diesem Artikel wird erläutert, wie Sie bei häufigem Trennen der RDP-Verbindung (Remotedesktopprotokoll) mit einem virtuellen Azure-Computer eine Problembehandlung durchführen.

> [!NOTE] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [das Resource Manager-Modell und das klassische Bereitstellungsmodell](../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells. Es wird empfohlen, dieses Modell anstelle des klassischen Bereitstellungsmodells für neue Bereitstellungen zu verwenden.

## <a name="symptom"></a>Symptom

Bei ihren Sitzungen treten zeitweilige RDP-Verbindungsprobleme auf. Sie können anfangs eine Verbindung mit dem virtuellen Computer herstellen, dann wird die Verbindung jedoch getrennt.

## <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn der RDP-Listener falsch konfiguriert ist. Dieses Problem tritt in der Regel auf einem virtuellen Computer auf, der ein benutzerdefiniertes Image verwendet.

## <a name="solution"></a>Lösung

[Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers](../windows/snapshot-copy-managed-disk.md) des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. 

Verwenden Sie zum Beheben des Problems die serielle Konsole, oder [reparieren Sie den virtuellen Computer offline](#repair-the-vm-offline), indem Sie den Betriebssystemdatenträger des virtuellen Computers an einen virtuellen Wiederherstellungscomputer anfügen.

### <a name="serial-control"></a>Serielle Konsole

1. Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine CMD-Instanz](./serial-console-windows.md). Führen Sie dann die folgenden Befehle zum Zurücksetzen der RDP-Konfigurationen aus. Ist die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert, fahren Sie mit dem nächsten Schritt fort.
2. Setzen Sie die RDP-Sicherheitsstufe auf 0 herunter. Bei dieser Einstellung wird für die Kommunikation zwischen Server und Client die native RDP-Verschlüsselung genutzt.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Legen Sie für die Verschlüsselungsstufe die niedrigste Einstellung fest, um die Verbindungsherstellung für ältere RDP-Clients zu ermöglichen.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. Legen Sie RDP zum Laden der Benutzerkonfiguration des Clientcomputers fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. Aktivieren Sie die RDP-Keep-Alive-Einstellung:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. Legen Sie die Einstellung für das erneute Herstellen einer RDP-Verbindung fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. Legen Sie die Einstellung für die RDP-Sitzungsdauer fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. Legen Sie die Einstellung für die RDP-Trennungsdauer fest: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. Legen Sie die Einstellung für die RDP-Verbindungsdauer fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. Legen Sie die Einstellung für die Leerlaufzeit der RDP-Sitzung fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. Legen Sie die Einstellung zum Beschränken der maximalen Anzahl gleichzeitiger Verbindungen fest:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. Starten Sie den virtuellen Computer neu, und versuchen Sie dann noch einmal, eine RDP-Verbindung mit ihm herzustellen.

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

1. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](../windows/troubleshoot-recovery-disks-portal.md).
2. Stellen Sie nach dem Anfügen des Betriebssystemdatenträgers an den virtuellen Wiederherstellungscomputer sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Notieren Sie sich den Laufwerkbuchstaben des angefügten Betriebssystemdatenträgers.
3. Navigieren Sie auf dem angefügten Betriebssystemdatenträger zum Ordner **\windows\system32\config**. Kopieren Sie alle Dateien in diesem Ordner als Sicherung für den Fall, dass ein Zurücksetzen erforderlich ist.
4. Starten Sie den Registrierungs-Editor (regedit.exe).
5. Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE**. Wählen Sie im Menü **Datei** > **Struktur laden**:
6. Navigieren Sie auf dem angefügten Betriebssystemdatenträger zum Ordner **\windows\system32\config\SYSTEM**. Geben Sie beim Namen der Struktur **BROKENSYSTEM** ein. Die neue Registrierungsstruktur wird unter dem Schlüssel **HKEY_LOCAL_MACHINE** angezeigt. Laden Sie dann die Softwarestruktur **\windows\system32\config\SOFTWARE** unter dem Schlüssel **HKEY_LOCAL_MACHINE**. Geben Sie beim Namen der Struktursoftware **BROKENSYSTEM** ein. 
7. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten (**Als Administrator ausführen**), und führen Sie die Befehle in den übrigen Schritten aus, um die RDP-Konfigurationen zurückzusetzen. 
8. Setzen Sie die RDP-Sicherheitsstufe auf 0 herunter, sodass für die Kommunikation zwischen dem Server und dem Client die native RDP-Verschlüsselung genutzt wird:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Legen Sie für die Verschlüsselungsstufe die niedrigste Einstellung fest, um die Verbindungsherstellung für ältere RDP-Clients zu ermöglichen:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. Legen Sie RDP zum Laden der Benutzerkonfiguration des Clientcomputers fest.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. Aktivieren Sie die RDP-Keep-Alive-Einstellung:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. Legen Sie die Einstellung für das erneute Herstellen einer RDP-Verbindung fest:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. Legen Sie die Einstellung für die RDP-Sitzungsdauer fest:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. Legen Sie die Einstellung für die RDP-Trennungsdauer fest:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. Legen Sie die Einstellung für die RDP-Verbindungsdauer fest:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. Legen Sie die Einstellung für die Leerlaufzeit der RDP-Sitzung fest:     REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. Legen Sie die Einstellung zum Beschränken der maximalen Anzahl gleichzeitiger Verbindungen fest:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. Starten Sie den virtuellen Computer neu, und versuchen Sie dann noch einmal, eine RDP-Verbindung mit ihm herzustellen.

## <a name="need-help"></a>Sie brauchen Hilfe? 
Wenden Sie sich an den Support. [Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.





