---
title: Windows-Neustartschleife auf einem virtuellen Azure-Computer | Microsoft-Dokumentation
description: Informationen zur Problembehandlung bei einer Windows-Neustartschleife | Microsoft-Dokumentation
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
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 1c97b1da094b759ccf85f310ceec4c7abfd91b9b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472293"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows-Neustartschleife auf einem virtuellen Azure-Computer
In diesem Artikel wird die Neustartschleife beschrieben, die auf einem virtuellen Windows-Computer (Virtual Machine, VM) in Microsoft Azure auftreten kann.

## <a name="symptom"></a>Symptom

Bei Verwendung der [Startdiagnose](./boot-diagnostics.md) zum Abrufen der Momentaufnahmen eines virtuellen Computers stellen Sie fest, dass der virtuelle Computer gestartet wird, aber der Startvorgang unterbrochen wird und der Vorgang wieder von vorn beginnt.

![Startbildschirm 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Ursache

Die Neustartschleife tritt aus folgenden Gründen auf:

### <a name="cause-1"></a>Ursache 1

Ein Drittanbieterdienst wurde als kritisch gekennzeichnet und kann nicht gestartet werden. Dies führt zu einem Neustart des Betriebssystems.

### <a name="cause-2"></a>Ursache 2

Am Betriebssystem wurden einige Änderungen vorgenommen. Diese beziehen sich in der Regel auf die Installation eines Updates oder einer Anwendung oder auf eine neue Richtlinie. Möglicherweise müssen Sie die folgenden Protokolle auf weitere Details überprüfen:

- Ereignisprotokolle
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Ursache 3

Eine Beschädigung des Dateisystems kann die Ursache dafür sein. Die Änderung, die zur Beschädigung des Betriebssystems geführt hat, kann jedoch nur schwer diagnostiziert und identifiziert werden.

## <a name="solution"></a>Lösung

Um dieses Problem zu lösen, können Sie [den Betriebssystemdatenträger sichern](../windows/snapshot-copy-managed-disk.md), [den Betriebssystemdatenträger an einen virtuellen Wiederherstellungscomputer anfügen](../windows/troubleshoot-recovery-disks-portal.md) und dann den Lösungsoptionen entsprechend folgen oder die einzelnen Lösungen nacheinander ausprobieren.

### <a name="solution-for-cause-1"></a>Lösung für Ursache 1

1. Nachdem der Betriebssystemdatenträger an einen funktionierenden virtuellen Computer angefügt wurde, müssen Sie sicherstellen, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Notieren Sie sich zudem den Laufwerkbuchstaben der Partition, die den Ordner **\Windows** enthält.

2. Wenn der Datenträger auf **Offline** festgelegt ist, legen Sie ihn auf **Online** fest.

3. Erstellen Sie eine Kopie des Ordners **\Windows\System32\config** für den Fall, dass ein Rollback der Änderungen erforderlich ist.

4. Öffnen Sie auf dem virtuellen Wiederherstellungscomputer den Windows-Registrierungs-Editor (regedit).

5. Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE** aus, und wählen Sie dann im Menü die Optionen **Datei** > **Struktur laden** aus.

6. Navigieren Sie im Ordner **\Windows\System32\config** zur Datei „SYSTEM“.

7. Wählen Sie **Öffnen** aus, geben Sie den Namen **BROKENSYSTEM** ein, und erweitern Sie den Schlüssel **HKEY_LOCAL_MACHINE**. Dann wird ein zusätzlicher Schlüssel namens **BROKENSYSTEM**  angezeigt.

8. Überprüfen Sie, über welchen ControlSet-Schlüssel der Computer gestartet wird. Die zugehörige Schlüsselnummer wird im folgenden Registrierungsschlüssel angezeigt.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Überprüfen Sie die Wichtigkeit des VM-Agent-Diensts über den folgenden Registrierungsschlüssel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Wenn der Wert des Registrierungsschlüssels nicht auf **2** festgelegt ist, können Sie mit der nächsten Lösung fortfahren.

11. Wenn der Wert des Registrierungsschlüssels auf **2** festgelegt ist, ändern Sie den Wert von **2** in **1**.

12. Wenn einer der folgenden Schlüssel vorhanden ist und den Wert **2** oder **3** aufweist, ändern Sie diese Werte entsprechend in **1**:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Wählen Sie den Schlüssel **BROKENSYSTEM** aus, und wählen Sie dann im Menü die Optionen **Datei** > **Struktur laden** aus.

14. Trennen Sie den Betriebssystemdatenträger von dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen.

15. Entfernen Sie den Datenträger von dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen, und warten Sie ca. 2 Minuten, bis Azure diesen Datenträger freigibt.

16. [Erstellen Sie einen neuen virtuellen Computer auf Grundlage des Betriebssystemdatenträgers](../windows/create-vm-specialized.md).

17. Wenn das Problem behoben wurde, müssen Sie möglicherweise den [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) („WaAppAgent.exe“) neu installieren.

### <a name="solution-for-cause-2"></a>Lösung für Ursache 2

Stellen Sie die letzte als funktionierend bekannte Konfiguration des virtuellen Computers her. Führen Sie die unter [Starten des virtuellen Azure Windows-Computers in der letzten als funktionierend bekannten Konfiguration](https://support.microsoft.com/help/4016731/) aufgeführten Schritte aus.

### <a name="solution-for-cause-3"></a>Lösung für Ursache 3
>[!NOTE]
>Das folgende Verfahren sollte nur als letzte Maßnahme verwendet werden. Beim Wiederherstellen über „RegBack“ kann der Zugriff auf den Computer wiederhergestellt werden, aber das Betriebssystem wird nicht als stabil betrachtet, weil in der Registrierung zwischen dem Zeitstempel der Struktur und dem aktuellen Tag Daten verloren gehen. Sie müssen einen neuen virtuellen Computer erstellen und Pläne zum Migrieren von Daten erarbeiten.

1. Nachdem der Datenträger an einen Problembehandlungs-VM angefügt wurde, müssen Sie sicherstellen, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist.

2. Erstellen Sie eine Kopie des Ordners **\Windows\System32\config** für den Fall, dass ein Rollback der Änderungen erforderlich ist.

3. Kopieren Sie die Dateien im Ordner **\Windows\System32\config\regback**, und ersetzen Sie die Dateien im Ordner **\Windows\System32\config**.

4. Entfernen Sie den Datenträger von dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen, und warten Sie ca. 2 Minuten, bis Azure diesen Datenträger freigibt.

5. [Erstellen Sie einen neuen virtuellen Computer auf Grundlage des Betriebssystemdatenträgers](../windows/create-vm-specialized.md).


