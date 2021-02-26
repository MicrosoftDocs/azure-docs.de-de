---
title: Verschieben virtueller Azure-Computer in ein neues Abonnement oder in eine neue Ressourcengruppe
description: Verwenden Sie Azure Resource Manager, um virtuelle Computer in eine neue Ressourcengruppe oder ein neues Abonnement zu verschieben.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ad7023f309f1ca948711eaa9bdf3867d2ef7a6f8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104908"
---
# <a name="move-guidance-for-virtual-machines"></a>Anleitung zum Verschieben virtueller Computer

In diesem Artikel werden Szenarien beschrieben, die derzeit nicht unterstützt werden, und die Schritte zum Verschieben virtueller Computer mit Sicherungen.

## <a name="scenarios-not-supported"></a>Nicht unterstützte Szenarien

Folgende Szenarios werden noch nicht unterstützt:

* Eine Virtual Machine Scale Sets-Instanz mit Load Balancer der Standard-SKU oder einer öffentlichen IP-Adresse der Standard-SKU kann nicht verschoben werden.
* Virtuelle Computer in einem vorhandenen virtuellen Netzwerk können nicht zu einem neuen Abonnement verschoben werden, wenn Sie nicht alle Ressourcen im virtuellen Netzwerk verschieben.
* Von Marketplace-Ressourcen erstellte virtuelle Computer, an die Pläne angefügt sind, können nicht abonnementübergreifend verschoben werden. Eine mögliche Problemumgehung finden Sie unter [Virtuelle Computer mit Marketplace-Plänen](#virtual-machines-with-marketplace-plans).
* Virtuelle Computer mit niedriger Priorität und VM-Skalierungsgruppen mit niedriger Priorität können nicht ressourcengruppen- oder abonnementübergreifend verschoben werden.
* Virtuelle Computer in einer Verfügbarkeitsgruppe können nicht einzeln verschoben werden.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Sie können einen virtuellen Computer, der in einen Schlüsseltresor integriert ist, nicht verschieben, um [Azure Disk Encryption für virtuelle Linux-Computer](../../../virtual-machines/linux/disk-encryption-overview.md) oder [Azure Disk Encryption für virtuelle Windows-Computer](../../../virtual-machines/windows/disk-encryption-overview.md) zu implementieren. Um den virtuellen Computer zu verschieben, müssen Sie die Verschlüsselung deaktivieren.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>Virtuelle Computer mit Marketplace-Plänen

Von Marketplace-Ressourcen erstellte virtuelle Computer, an die Pläne angefügt sind, können nicht abonnementübergreifend verschoben werden. Um diese Einschränkung zu umgehen, können Sie die Bereitstellung der virtuellen Computer im aktuellen Abonnement aufheben und sie im neuen Abonnement erneut bereitstellen. Die folgenden Schritte helfen Ihnen, den virtuellen Computer im neuen Abonnement neu zu erstellen. Möglicherweise funktionieren sie jedoch nicht für alle Szenarien. Wenn der Plan nicht mehr im Marketplace verfügbar ist, werden diese Schritte nicht funktionieren.

1. Kopieren Sie die Informationen zum Plan.

1. Klonen Sie entweder den Datenträger des Betriebssystems in das Zielabonnement, oder verschieben Sie den ursprünglichen Datenträger, nachdem Sie den virtuellen Computer aus dem Quellabonnement gelöscht haben.

1. Akzeptieren Sie im Zielabonnement die Marketplace-Geschäftsbedingungen für Ihren Plan. Sie können die Geschäftsbedingungen akzeptieren, indem Sie den folgenden PowerShell-Befehl ausführen:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   Oder Sie können eine neue Instanz eines virtuellen Computers mit dem Plan über das Portal erstellen. Sie können den virtuellen Computer löschen, nachdem Sie die Geschäftsbedingungen des neuen Abonnements akzeptiert haben.

1. Im Zielabonnement erstellen Sie den virtuellen Computer von dem geklonten Betriebssystemdatenträger mithilfe von PowerShell, der Befehlszeilenschnittstelle oder einer Azure Resource Manager-Vorlage neu. Schließen Sie den Marketplace-Plan ein, der dem Datenträger angefügt ist. Die Informationen zum Plan sollten mit dem Plan übereinstimmen, den Sie für das neue Abonnement erworben haben.

## <a name="virtual-machines-with-azure-backup"></a>Virtuelle Computer mit Azure Backup

Um mit Azure Backup konfigurierte virtuelle Computer zu verschieben, müssen Sie die Wiederherstellungspunkte im Tresor löschen.

Wenn [vorläufiges Löschen](../../../backup/soft-delete-virtual-machines.md) für Ihren virtuellen Computer aktiviert ist, können Sie den virtuellen Computer nicht verschieben, solange diese Wiederherstellungspunkte noch vorhanden sind. [Deaktivieren Sie entweder vorläufiges Löschen](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete), oder warten Sie 14 Tage nach dem Löschen der Wiederherstellungspunkte.

### <a name="portal"></a>Portal

1. Halten Sie die Sicherung vorübergehend an, und bewahren Sie Sicherungsdaten auf.
2. Gehen Sie folgendermaßen vor, um mit Azure Backup konfigurierte virtuelle Computer zu verschieben:

   1. Ermitteln Sie den Speicherort Ihres virtuellen Computers.
   2. Suchen Sie eine Ressourcengruppe mit dem folgenden Namensmuster: `AzureBackupRG_<VM location>_1`. Der Name weist z. B. das Format *AzureBackupRG_westus2_1* auf.
   3. Aktivieren Sie im Azure-Portal die Option **Ausgeblendete Typen anzeigen**.
   4. Suchen Sie die Ressource mit dem Typ **Microsoft.Compute/restorePointCollections** und dem Namensmuster `AzureBackup_<VM name>_###########`.
   5. Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.
   6. Nach Abschluss des Löschvorgangs können Sie den virtuellen Computer verschieben.

3. Verschieben Sie die VM in die Zielressourcengruppe.
4. Nehmen Sie die Sicherung wieder auf.

### <a name="powershell"></a>PowerShell

1. Ermitteln Sie den Speicherort Ihres virtuellen Computers.

1. Suchen Sie nach einer Ressourcengruppe mit diesem Namensmuster: `AzureBackupRG_<VM location>_1`. Der Name kann z. B. `AzureBackupRG_westus2_1` lauten.

1. Wenn Sie nur einen virtuellen Computer verschieben, rufen Sie die Wiederherstellungspunktsammlung für diesen virtuellen Computer ab.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Wenn Sie alle virtuellen Computer mit Sicherungen an diesem Standort verschieben, rufen Sie die Wiederherstellungspunktsammlungen für diese virtuellen Computer ab.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Löschen Sie jede Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Ermitteln Sie den Speicherort Ihres virtuellen Computers.

1. Suchen Sie nach einer Ressourcengruppe mit diesem Namensmuster: `AzureBackupRG_<VM location>_1`. Der Name kann z. B. `AzureBackupRG_westus2_1` lauten.

1. Wenn Sie nur einen virtuellen Computer verschieben, rufen Sie die Wiederherstellungspunktsammlung für diesen virtuellen Computer ab.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Löschen Sie diese Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Wenn Sie alle virtuellen Computer mit Sicherungen an diesem Standort verschieben, rufen Sie die Wiederherstellungspunktsammlungen für diese virtuellen Computer ab.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Löschen Sie jede Ressource. Dieser Vorgang löscht nur die sofortigen Wiederherstellungspunkte und nicht die gesicherten Daten im Tresor.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Nächste Schritte

* Befehle zum Verschieben von Ressourcen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../move-resource-group-and-subscription.md).

* Weitere Informationen zum Verschieben von Recovery Services-Tresoren für die Sicherung finden Sie unter [Einschränkungen von Recovery Services](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).
