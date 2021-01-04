---
title: Konvertieren einer Windows-VM von nicht verwalteten Datenträgern in verwaltete Datenträger
description: Hier wird beschrieben, wie Sie eine Windows-VM von nicht verwalteten Datenträgern mithilfe von PowerShell im Resource Manager-Bereitstellungsmodell in verwaltete Datenträger konvertieren.
author: roygara
ms.service: virtual-machines-windows
ms.subservice: disks
ms.topic: how-to
ms.date: 07/12/2018
ms.author: rogarana
ms.openlocfilehash: 2168b3e3c55eaf06b93358f4623b181f134b4f9e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017344"
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Konvertieren einer Windows-VM von nicht verwalteten Datenträgern in verwaltete Datenträger

Wenn Sie über vorhandene virtuelle Windows-Computer (VMs) verfügen, die nicht verwaltete Datenträger verwenden, können Sie die VMs über den [Azure Managed Disks-Dienst](../managed-disks-overview.md) konvertieren, sodass verwaltete Datenträger verwendet werden. Bei diesem Prozess werden sowohl der Betriebssystemdatenträger als auch alle anderen angefügten Datenträger konvertiert.

 

## <a name="before-you-begin"></a>Voraussetzungen


* Lesen Sie den Abschnitt [Planen der Migration zu Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Lesen Sie die [häufig gestellten Fragen zu Managed Disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* Die ursprünglichen VHDs und das Speicherkonto, die vor der Konvertierung vom virtuellen Computer verwendet wurden, werden nicht gelöscht. Sie verursachen weiterhin Kosten. Um zu vermeiden, dass diese Artefakte in Rechnung gestellt werden, löschen Sie die ursprünglichen VHD-Blobs, nachdem Sie sichergestellt haben, dass die Konvertierung abgeschlossen ist. Wenn Sie nach diesen nicht angefügten Datenträgern suchen müssen, um sie zu löschen, lesen Sie den Artikel [Suchen und Löschen von nicht angefügten verwalteten und nicht verwalteten Azure-Datenträgern](find-unattached-disks.md).


## <a name="convert-single-instance-vms"></a>Konvertieren von Einzelinstanz-VMs
In diesem Abschnitt wird beschrieben, wie Sie für Einzelinstanz-VMs von Azure die Konvertierung von nicht verwalteten Datenträgern in verwaltete Datenträger durchführen. (Wenn Ihre VMs in einer Verfügbarkeitsgruppe enthalten sind, lesen Sie den nächsten Abschnitt.) 

1. Heben Sie die Zuordnung der VM mit dem Cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm) auf. Im folgenden Beispiel wird die Zuordnung für die VM `myVM` in der Ressourcengruppe `myResourceGroup` aufgehoben: 

   ```azurepowershell-interactive
   $rgName = "myResourceGroup"
   $vmName = "myVM"
   Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
   ```

2. Konvertieren Sie die VM mit dem Cmdlet [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) in verwaltete Datenträger. Mit dem folgenden Prozess wird der vorherige virtuelle Computer einschließlich des Betriebssystemdatenträgers und der anderen Datenträger konvertiert, und der virtuelle Computer wird gestartet:

   ```azurepowershell-interactive
   ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
   ```



## <a name="convert-vms-in-an-availability-set"></a>Konvertieren von virtuellen Computern in einer Verfügbarkeitsgruppe

Falls sich die VMs, die Sie in verwaltete Datenträger konvertieren möchten, in einer Verfügbarkeitsgruppe befinden, müssen Sie zuerst für die Verfügbarkeitsgruppe die Konvertierung in eine verwaltete Verfügbarkeitsgruppe durchführen.

1. Konvertieren Sie die Verfügbarkeitsgruppe mit dem Cmdlet [Update-AzAvailabilitySet](/powershell/module/az.compute/update-azavailabilityset). Im folgenden Beispiel wird die Verfügbarkeitsgruppe `myAvailabilitySet` aus der Ressourcengruppe `myResourceGroup` aktualisiert:

   ```azurepowershell-interactive
   $rgName = 'myResourceGroup'
   $avSetName = 'myAvailabilitySet'

   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
   ```

   Wenn die Region, in der sich die Verfügbarkeitsgruppe befindet, nur 2 verwaltete Fehlerdomänen umfasst, aber die Anzahl der nicht verwalteten Fehlerdomänen 3 beträgt, wird bei diesem Befehl eine ähnliche Fehlermeldung wie die Folgende angezeigt: „Die angegebene Fehlerdomänenanzahl 3 muss im Bereich 1 bis 2 liegen“. Um den Fehler zu beheben, ändern Sie die Fehlerdomäne in 2, und `Sku` in `Aligned` wie im Folgenden gezeigt:

   ```azurepowershell-interactive
   $avSet.PlatformFaultDomainCount = 2
   Update-AzAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
   ```

2. Heben Sie die Zuordnung der VMs in der Verfügbarkeitsgruppe auf, und konvertieren Sie sie. Das folgende Skript hebt mit dem Cmdlet [Stop-AzVM](/powershell/module/az.compute/stop-azvm) die Zuordnung aller virtuellen Computer auf, konvertiert sie mit [ConvertTo-AzVMManagedDisk](/powershell/module/az.compute/convertto-azvmmanageddisk) und startet sie als Teil des Konvertierungsprozesses automatisch neu:

   ```azurepowershell-interactive
   $avSet = Get-AzAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

   foreach($vmInfo in $avSet.VirtualMachinesReferences)
   {
     $vm = Get-AzVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   }
   ```


## <a name="troubleshooting"></a>Problembehandlung

Wenn während der Konvertierung ein Fehler auftritt oder sich eine VM aufgrund von Problemen bei einer vorherigen Konvertierung in einen fehlerhaften Zustand befindet, führen Sie das Cmdlet `ConvertTo-AzVMManagedDisk` erneut aus. Derartige Probleme können normalerweise durch eine einfache Wiederholung behoben werden.
Stellen Sie vor dem Konvertieren sicher, dass alle VM-Erweiterungen den Zustand „Bereitstellung erfolgreich“ aufweisen. Andernfalls tritt bei der Konvertierung ein Fehler mit Fehlercode 409 auf.

## <a name="convert-using-the-azure-portal"></a>Konvertieren über das Azure-Portal

Sie haben auch die Möglichkeit, nicht verwaltete Datenträger über das Azure-Portal in verwaltete Datenträger zu konvertieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie die VM in der Liste der VMs im Portal aus.
3. Wählen Sie auf dem Blatt für die VM aus dem Menü die Option **Datenträger** aus.
4. Wählen Sie oben auf dem Blatt **Datenträger** die Option **Zu Managed Disks migrieren** aus.
5. Wenn sich Ihre VM in einer Verfügbarkeitsgruppe befindet, wird auf dem Blatt **Zu Managed Disks migrieren** eine Warnung angezeigt, die besagt, dass Sie die Verfügbarkeitsgruppe zuerst konvertieren müssen. Die Warnung sollte einen Link enthalten, über den Sie die Verfügbarkeitsgruppe konvertieren können. Sobald die Verfügbarkeitsgruppe konvertiert wurde, oder wenn sich Ihre VM nicht in einer Verfügbarkeitsgruppe befindet, klicken Sie auf **Migrieren**, um den Prozess der Migration Ihrer Datenträger zu Managed Disks zu starten.

Nach Abschluss der Migration wird die VM angehalten und neu gestartet.

## <a name="next-steps"></a>Nächste Schritte

[Konvertieren von verwalteten Standarddatenträgern in Premium-Datenträger](convert-disk-storage.md)

Erstellen Sie eine schreibgeschützte Kopie eines virtuellen Computers mit [Momentaufnahmen](snapshot-copy-managed-disk.md).