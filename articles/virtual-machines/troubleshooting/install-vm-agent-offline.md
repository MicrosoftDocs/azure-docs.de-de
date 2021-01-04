---
title: Installieren des Azure-VM-Agents im Offlinemodus | Microsoft Docs
description: Erfahren Sie mehr über das Installieren des Azure-VM-Agents im Offlinemodus.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002611"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Installieren des Azure-VM-Agents im Offlinemodus 

Azure Virtual Machine Agent (VM Agent) bietet nützliche Features wie das Zurücksetzen lokaler Administratorkennwörter und das Übertragen von Skripts per Push. In diesem Artikel erfahren Sie, wie Sie den VM-Agent für eine virtuellen Windows-Computer (VM) offline installieren. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Gründe für das Installieren des VM-Agents im Offlinemodus

In den folgenden Szenarien können Sie den VM-Agent im Offlinemodus installieren:

- Auf der bereitgestellten Azure-VM ist der VM-Agent nicht installiert, oder er funktioniert nicht.
- Sie haben das Administratorkennwort für den virtuellen Computer vergessen oder können nicht drauf zugreifen.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Installieren des VM-Agents im Offlinemodus

Mit den folgenden Schritten installieren Sie den VM-Agent im Offlinemodus:

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Schritt 1: Anfügen des Betriebssystem-Datenträgers des virtuellen Computers als Datenträger an einen anderen virtuellen Computer

1. Erstellen Sie eine Momentaufnahme für den Betriebssystemdatenträger der betroffenen VM, erstellen Sie aus der Momentaufnahme einen Datenträger, und fügen Sie diesen an eine VM zur Problembehebung an. Weitere Informationen finden Sie unter [Beheben von Problemen mit einer Windows-VM durch Hinzufügen des Betriebssystemdatenträgers zu einer Wiederherstellungs-VM im Azure-Portal](troubleshoot-recovery-disks-portal-windows.md). Bei einer klassischen VM löschen Sie die VM, behalten den Betriebssystemdatenträger bei und fügen diesen an eine VM zur Problembehebung an.

2.  Stellen Sie eine Verbindung mit der Problembehebungs-VM her. Öffnen Sie **Computerverwaltung** > **Datenträgerverwaltung**. Stellen Sie sicher, dass der Betriebssystemdatenträger online ist und dass den Datenträgerpartitionen Laufwerkbuchstaben zugewiesen sind.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Schritt 2: Ändern des Betriebssystem-Datenträgers zum Installieren des Azure-VM-Agents

1.  Stellen Sie eine Remotedesktopverbindung mit der Problembehebungs-VM her.

2.  Navigieren Sie auf der Problembehebungs-VM zum angefügten Betriebssystemdatenträger, und öffnen Sie den Ordner „\windows\system32\config“. Kopieren Sie alle Dateien in diesem Ordner als Sicherung für den Fall, dass ein Zurücksetzen erforderlich ist.

3.  Starten Sie den **Registrierungs-Editor** (regedit.exe).

4.  Wählen Sie den Schlüssel **HKEY_LOCAL_MACHINE**. Wählen Sie im Menü **Datei** > **Struktur laden**:

    ![Laden der Struktur](./media/install-vm-agent-offline/load-hive.png)

5.  Navigieren Sie auf dem angefügten Betriebssystemdatenträger zum Ordner \windows\system32\config\SYSTEM. Geben Sie beim Namen der Struktur **BROKENSYSTEM** ein. Die neue Registrierungsstruktur wird unter dem Schlüssel **HKEY_LOCAL_MACHINE** angezeigt.

6.  Navigieren Sie auf dem angefügten Betriebssystemdatenträger zum Ordner \windows\system32\config\SOFTWARE. Geben Sie beim Namen der Struktursoftware **BROKENSYSTEM** ein.

7. Wenn auf dem angefügten Betriebssystem der VM-Agent installiert ist, führen Sie eine Sicherung der aktuellen Konfiguration durch. Wenn der VM-Agent nicht installiert ist, fahren Sie mit dem nächsten Schritt fort.
      
    1. Benennen Sie den Ordner \windowsazure in \windowsazure.old um.

    2. Exportieren Sie die folgenden Registrierungen:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Verwenden Sie die vorhandenen Dateien auf den Problembehebungs-VM als Repository für die VM-Agent-Installation. Führen Sie die folgenden Schritte aus:

    1. Exportieren Sie über die Problembehebungs-VM die folgenden Unterschlüssel im Registrierungsformat (.reg): 
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE  \SYSTEM\ControlSet001\Services\RdAgent

          ![Exportieren der Unterschlüssel der Registrierung](./media/install-vm-agent-offline/backup-reg.png)

    2. Bearbeiten Sie die Registrierungsdateien. Ändern Sie in jeder Datei den Eingabewert **SYSTEM** in **BROKENSYSTEM** (wie in den folgenden Abbildungen gezeigt), und speichern Sie die Datei. Merken Sie sich den **ImagePath** des aktuellen VM-Agents. Wir müssen den entsprechenden Ordner auf den angefügten Betriebssystemdatenträger kopieren. 

        ![Ändern der Unterschlüsselwerte der Registrierung](./media/install-vm-agent-offline/change-reg.png)

    3. Importieren Sie durch Doppelklicken auf jede Registrierungsdatei die Registrierungsdateien in das Repository.

    4. Vergewissern Sie sich, dass die folgenden beiden Unterschlüssel in die Struktur **BROKENSYSTEM** importiert wurden:
        - WindowsAzureGuestAgent
        - RdAgent

    5. Kopieren Sie den Installationsordner des aktuellen VM-Agents auf den angefügten Betriebssystemdatenträger: 

        1.  Erstellen Sie auf dem angefügten Betriebssystemdatenträger einen Ordner namens „WindowsAzure“ im Stammpfad.

        2.  Wechseln Sie auf der Problembehebungs-VM zu „C:\WindowsAzure“, und suchen Sie nach einem beliebigen Ordner namens „C:\WindowsAzure\GuestAgent_X.X.XXXX.XXX“. Kopieren Sie den Ordner „GuestAgent“, der die neueste Versionsnummer von „C:\WindowsAzure“ hat, in den Ordner „WindowsAzure“ auf dem angefügten Betriebssystemdatenträger. Wenn Sie nicht sicher sind, welcher Ordner kopiert werden sollte, kopieren Sie alle „GuestAgent“-Ordner. Die folgende Abbildung zeigt ein Beispiel für den Ordner „GuestAgent“, der auf den angefügten Betriebssystemdatenträger kopiert wird.

             ![Kopieren des Ordners „GuestAgent“](./media/install-vm-agent-offline/copy-files.png)

9.  Wählen Sie **BROKENSYSTEM**. Wählen Sie im Menü **Datei** > **Struktur laden**.

10.  Wählen Sie **BROKENSOFTWARE**. Wählen Sie im Menü **Datei** > **Struktur laden**.

11.  Trennen Sie den Betriebssystemdatenträger, und [ändern Sie ihn für die betroffene VM](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Bei einer klassischen VM erstellen Sie eine neue VM mithilfe des reparierten Betriebssystemdatenträgers.

12.  Greifen Sie auf den virtuellen Computer zu. Prüfen Sie, ob RdAgent ausgeführt wird und die Protokolle generiert werden.

Wenn Sie den virtuellen Computer mit dem Resource Manager-Bereitstellungsmodell erstellt haben, ist der Vorgang abgeschlossen.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Verwenden der ProvisionGuestAgent-Eigenschaft für klassische virtuelle Computer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Wenn Sie den virtuellen Computer mit dem klassischen Bereitstellungsmodell erstellt haben, verwenden Sie das Azure PowerShell-Modul, um die **ProvisionGuestAgent**-Eigenschaft zu aktualisieren. Die Eigenschaft informiert Azure, dass der VM-Agent auf dem virtuellen Computer installiert ist.

Führen Sie zum Festlegen der **ProvisionGuestAgent**-Eigenschaft die folgenden Befehle in Azure PowerShell aus:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Führen Sie anschließend den Befehl `Get-AzureVM` aus. Beachten Sie, dass die **GuestAgentStatus**-Eigenschaft jetzt mit Daten gefüllt ist:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über den Agent für virtuelle Azure-Computer](../extensions/agent-windows.md)
- [Erweiterungen und Features für virtuelle Computer für Windows](../extensions/features-windows.md)
