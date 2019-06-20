---
title: Beheben eines Problems, das beim Herstellen einer Remoteverbindung mit einem virtuellen Windows 10- oder Windows Server 2016-Computer in Azure von „netvsc.sys“ verursacht wird | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie ein durch „netsvc.sys“ verursachtes RDP-Problem beheben, das beim Herstellen einer Verbindung mit einem virtuellen Windows 10- oder Windows Server 2016-Computer in Azure auftritt.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60362253"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Das Herstellen einer Remoteverbindung mit einem virtuellen Windows 10- oder Windows Server 2016-Computer in Azure ist aufgrund von „netvsc.sys“ nicht möglich.

In diesem Artikel wird die Behebung eines Problems erläutert, aufgrund dessen keine Netzwerkverbindung besteht, wenn Sie eine Verbindung mit einem virtuellen Windows 10- oder Windows Server 2016 Datacenter-Computer auf einem Hyper-V-Server 2016-Host herstellen.

## <a name="symptoms"></a>Symptome

Sie können per Remotedesktopprotokoll (RDP) keine Verbindung mit einem virtuellen Azure-Computer unter Windows 10 oder Windows Server 2016 herstellen. In der [Startdiagnose](boot-diagnostics.md) wird ein rotes Kreuz auf dem Netzwerkadapter angezeigt. Damit wird angegeben, dass für den virtuellen Computer nach dem vollständigen Laden des Betriebssystems keine Verbindung besteht.

Dieses Problem tritt in der Regel in [Build 14393](https://support.microsoft.com/help/4093120/) und [Build 15063](https://support.microsoft.com/help/4015583/) von Windows auf. Besitzen Sie eine höhere Betriebssystemversion, gilt dieser Artikel nicht für Ihr Szenario. Öffnen Sie zum Überprüfen der Version Ihres Systems eine CMD-Sitzung in der [seriellen Zugriffskonsole](serial-console-windows.md), und führen Sie **Ver** aus.

## <a name="cause"></a>Ursache

Das Problem kann auftreten, wenn die Version der installierten Systemdatei „netvsc.sys“ **10.0.14393.594** oder **10.0.15063.0** lautet. Diese Versionen von „netvsc.sys“ verhindern unter Umständen die Interaktion des Systems mit der Azure-Plattform.


## <a name="solution"></a>Lösung

[Erstellen Sie eine Momentaufnahme des Systemdatenträgers](../windows/snapshot-copy-managed-disk.md) des betroffenen virtuellen Computers als Sicherung, bevor Sie die unten angegebenen Schritte ausführen. Verwenden Sie zum Beheben dieses Problems die serielle Konsole, oder [reparieren Sie den virtuellen Computer offline](#repair-the-vm-offline), indem Sie den Systemdatenträger des virtuellen Computers an einen virtuellen Wiederherstellungscomputer anfügen.


### <a name="use-the-serial-console"></a>Verwenden der seriellen Konsole

Stellen Sie eine Verbindung mit der [seriellen Konsole her, und öffnen Sie eine PowerShell-Instanz](serial-console-windows.md). Führen Sie anschließend die folgenden Schritte aus.

> [!NOTE]
> Ist die serielle Konsole auf Ihrem virtuellen Computer nicht aktiviert, gehen Sie zum Abschnitt [Reparieren des virtuellen Computers im Offlinestatus](#repair-the-vm-offline).

1. Führen Sie in einer PowerShell-Instanz den folgenden Befehl aus, um die Version der Datei abzurufen (**c:\windows\system32\drivers\netvsc.sys**):

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. Laden Sie das entsprechende Update auf einen neuen oder vorhandenen Datenträger herunter, der an einen funktionierenden virtuellen Computer in der gleichen Region angefügt ist:

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) oder ein neueres Update
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) oder ein neueres Update

3. Trennen Sie den Hilfsprogrammdatenträger vom funktionierenden virtuellen Computer, und fügen Sie ihn an den fehlerhaften virtuellen Computer an.

4. Führen Sie den folgenden Befehl aus, um das Update auf dem virtuellen Computer zu installieren:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. Starten Sie den virtuellen Computer neu.

### <a name="repair-the-vm-offline"></a>Reparieren des virtuellen Computers im Offlinestatus

1. [Fügen Sie den Systemdatenträger an einen virtuellen Wiederherstellungscomputer an.](../windows/troubleshoot-recovery-disks-portal.md)

2. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen Wiederherstellungscomputer her.

3. Stellen Sie sicher, dass der Datenträger in der Datenträgerverwaltungskonsole als **Online** gekennzeichnet ist. Achten Sie auf den Laufwerkbuchstaben, der dem angefügten Systemdatenträger zugewiesen ist.

4. Erstellen Sie eine Kopie des Ordners **\Windows\System32\config** für den Fall, dass ein Rollback der Änderungen erforderlich ist.

5. Starten Sie auf dem virtuellen Wiederherstellungscomputer den Registrierungs-Editor (regedit.exe).

6. Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE** aus, und wählen Sie dann im Menü die Optionen **Datei** > **Struktur laden** aus.

7. Navigieren Sie im Ordner **\Windows\System32\config** zur Datei „SYSTEM“.

8. Wählen Sie **Öffnen** aus, geben Sie den Namen **BROKENSYSTEM** ein, und erweitern Sie den Schlüssel **HKEY_LOCAL_MACHINE**. Suchen Sie dann nach dem zusätzlichen Schlüssel mit dem Namen **BROKENSYSTEM**.

9. Navigieren Sie zum folgenden Speicherort:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Überprüfen Sie in jedem Unterschlüssel (etwa 0000) den Wert **DriverDesc**, der als **Microsoft HYPER-V-Netzwerkadapter** angezeigt wird.

11. Überprüfen Sie im Unterschlüssel den Wert **DriverVersion**. Dies ist die Treiberversion des Netzwerkadapters des virtuellen Computers.

12. Laden Sie das entsprechende Update herunter:

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) oder ein neueres Update
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) oder ein neueres Update

13. Fügen Sie den Systemdatenträger als Datenträger auf einem virtuellen Wiederherstellungscomputer an, auf dem Sie das Update herunterladen können.

14. Führen Sie den folgenden Befehl aus, um das Update auf dem virtuellen Computer zu installieren:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Führen Sie den folgenden Befehl aus, um die Bereitstellung der Strukturen aufzuheben:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Trennen Sie den Systemdatenträger, und erstellen Sie den virtuellen Computer erneut](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

Falls Sie weitere Hilfe benötigen, [wenden Sie sich an den Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um das Problem schnell beheben zu lassen.
