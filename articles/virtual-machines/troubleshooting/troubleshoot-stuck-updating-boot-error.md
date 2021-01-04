---
title: Startvorgang des virtuellen Azure-Computers bleibt bei Windows-Update hängen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Problem beheben, wenn der Startvorgang eines virtuellen Azure-Computers bei Windows-Update(s) hängen bleibt.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: fe2d444bbdc09ed5ab0205d4139591e3288e3358
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734564"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Startvorgang des virtuellen Azure-Computers bleibt bei Windows-Update hängen

In diesem Artikel wird beschrieben, wie Sie das Problem beheben, wenn Ihr virtueller Computer (Virtual Machine, VM) beim Startvorgang in der Windows Update-Phase hängen bleibt. 


## <a name="symptom"></a>Symptom

 Eine Windows-VM wird nicht gestartet. Beim Überprüfen der Screenshots im Fenster [Startdiagnose](../troubleshooting/boot-diagnostics.md) sehen Sie, dass der Start während des Updatevorgangs hängen bleibt. Im Folgenden finden Sie Beispiele für Meldungen, die angezeigt werden können:

- Windows ##% wird installiert. Schalten Sie den Computer nicht aus. Dies kann eine Weile dauern. Ihr PC wird mehrmals neu gestartet.
- Lassen Sie den PC bis zum Abschluss dieses Vorgangs eingeschaltet. Update # von # wird installiert... 
- Einrichtung der Updates konnte nicht abgeschlossen werden. Änderungen werden rückgängig gemacht. Schalten Sie den Computer nicht aus.
- Fehler beim Konfigurieren der Windows-Updates. Änderungen werden rückgängig gemacht. Schalten Sie den Computer nicht aus.
- Fehler <Fehlercode> beim Updatevorgang ##### von ##### (\Regist...)
- Schwerer Fehler <Fehlercode> beim Updatevorgang ##### von ##### ($$...)


## <a name="solution"></a>Lösung

Abhängig von der Anzahl der Updates, die installiert werden oder für die ein Rollback ausgeführt wird, kann der Updatevorgang eine Weile dauern. Lassen Sie den virtuellen Computer 8 Stunden lang in diesem Zustand. Wenn sich der virtuelle Computer nach Ablauf dieses Zeitraums immer noch in diesem Zustand befindet, starten Sie den virtuellen Computer über das Azure-Portal neu, und testen Sie, ob er normal gestartet werden kann. Wenn dieser Schritt nicht funktioniert, versuchen Sie, das Problem mithilfe der folgenden Lösung zu beheben.

### <a name="remove-the-update-that-causes-the-problem"></a>Entfernen des Updates, das das Problem verursacht

1. Erstellen Sie eine Momentaufnahme des Betriebssystemdatenträgers des betroffenen virtuellen Computers als Sicherung. Weitere Informationen finden Sie unter [Erstellen einer Momentaufnahme eines Datenträgers](../windows/snapshot-copy-managed-disk.md). 
2. [Fügen Sie den Betriebssystemdatenträger an einen virtuellen Computer für die Wiederherstellung an](troubleshoot-recovery-disks-portal-windows.md).
3. Nachdem Sie den Betriebssystemdatenträger an den virtuellen Wiederherstellungscomputer angefügt haben, führen Sie **diskmgmt.msc** aus, um die Datenträgerverwaltung zu öffnen, und vergewissern Sie sich, dass der angefügte Datenträger **ONLINE** ist. Notieren Sie sich den Laufwerkbuchstaben, der dem angefügten Betriebssystemdatenträger zugewiesen ist, der den Ordner „\Windows“ enthält. Wenn der Datenträger verschlüsselt ist, entschlüsseln Sie den Datenträger, bevor Sie mit den nächsten Schritten in diesem Dokument fortfahren.

4. Öffnen Sie eine Eingabeaufforderungsinstanz mit erhöhten Rechten („Als Administrator ausführen“). Führen Sie den folgenden Befehl aus, um die Liste mit den Updatepaketen abzurufen, die sich auf dem angefügten Betriebssystemdatenträger befinden:

    ```console
    dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt
    ```

    Ist dem angefügten Betriebssystemdatenträger beispielsweise Laufwerk „F“ zugeordnet, führen Sie den folgenden Befehl aus:

    ```console
    dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
    ```

5. Öffnen Sie die Datei „C:\temp\Patch_level.txt“, und lesen Sie die Datei von unten nach oben. Suchen Sie nach dem Update mit dem Status **Installation steht aus** oder **Deinstallation steht aus**.  Im Folgenden finden Sie ein Beispiel für den Updatestatus:

    ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Entfernen Sie das Update, das das Problem verursacht:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Beispiel: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Abhängig von der Größe des Pakets benötigt das DISM-Tool eine Weile, um die Deinstallation zu verarbeiten. Normalerweise wird der Vorgang innerhalb von 16 Minuten abgeschlossen.

7. [Trennen Sie den Betriebssystemdatenträger, und erstellen die VM neu.](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk) Überprüfen Sie dann, ob das Problem behoben ist.
