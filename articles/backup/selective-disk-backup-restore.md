---
title: Selektive Datenträgersicherung und -wiederherstellung für Azure-VMs
description: In diesem Artikel lernen Sie die selektive Datenträgersicherung und -wiederherstellung mithilfe der Azure-VM-Sicherungslösung kennen.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: 95104f231e7b4d4d2135ac3c5dde27512d465775
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746992"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Selektive Datenträgersicherung und -wiederherstellung für Azure-VMs

Azure Backup unterstützt die Sicherung aller Datenträger (Betriebssystem und Daten) einer VM mithilfe der VM-Sicherungslösung. Mithilfe der Funktionalität der selektiven Datenträgersicherung und -wiederherstellung können Sie nun eine Teilmenge der Datenträger auf einem virtuellen Computer sichern. Dies stellt eine effiziente und kostengünstige Lösung für Ihre Sicherungs- und Wiederherstellungsanforderungen dar. Jeder Wiederherstellungspunkt enthält nur die Datenträger, die im Sicherungsvorgang enthalten sind. So können Sie außerdem während des Wiederherstellungsvorgangs vom angegebenen Wiederherstellungspunkt aus eine Teilmenge der Datenträger wiederherstellen. Dies gilt sowohl für Wiederherstellungen von Momentaufnahmen als auch vom Tresor.

## <a name="scenarios"></a>Szenarien

Diese Lösung ist besonders in den folgenden Szenarien nützlich:

1. Sie verfügen über kritische Daten, die auf nur einem Datenträger oder einer Teilmenge der Datenträger gesichert werden sollen, und Sie möchten die übrigen, an einen virtuellen Computer angeschlossenen Datenträger nicht sichern, um die Sicherungsspeicherkosten zu minimieren.
2. Sie verfügen für einen Teil Ihrer VM oder Daten über andere Sicherungslösungen. Sie sichern beispielsweise Ihre Datenbanken oder Daten mit einer anderen Workloadsicherungslösung und möchten für die übrigen Daten oder Datenträger eine Sicherung auf Azure-VM-Ebene verwenden, um ein effizientes und robustes System mit den besten verfügbaren Funktionen zu erstellen.

Mithilfe von PowerShell oder Azure CLI können Sie die selektive Datenträgersicherung der Azure-VM konfigurieren.  Mithilfe eines Skripts können Sie Datenträger mit ihren LUN-Nummern einschließen oder ausschließen.  Zurzeit ist die Möglichkeit, die selektive Datenträgersicherung über das Azure-Portal zu konfigurieren, auf die Option **Nur Betriebssystemdatenträger sichern** beschränkt. Sie können also die Sicherung Ihres virtuellen Azure-Computers mit dem Betriebssystemdatenträger konfigurieren und alle daran angefügten Datenträger ausschließen.

>[!NOTE]
> Der Betriebssystemdatenträger wird der VM-Sicherung standardmäßig hinzugefügt und kann nicht ausgeschlossen werden.

## <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Stellen Sie sicher, dass Sie Az CLI Version 2.0.80 oder höher verwenden. Sie können die CLI-Version mit diesem Befehl abrufen:

```azurecli
az --version
```

Melden Sie sich bei der Abonnement-ID an, die den Recovery Services-Tresor und die VM enthält:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>In jedem der folgenden Befehle wird nur der **resourcegroup** -Name (nicht das Objekt) benötigt, der dem Tresor entspricht.

### <a name="configure-backup-with-azure-cli"></a>Konfigurieren der Sicherung mit Azure CLI

Beim Konfigurieren des Schutzes müssen Sie die Datenträgerlisteneinstellung mit einem **inclusion** / **exclusion** -Parameter angeben und die LUN-Nummern der Datenträger angeben, die in die Sicherung eingeschlossen oder ausgeschlossen werden sollen.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

Wenn sich die VM nicht in derselben Ressourcengruppe wie der Tresor befindet, verweist **ResourceGroup** auf die Ressourcengruppe, in der der Tresor erstellt wurde. Geben Sie anstelle des VM-Namens die VM-ID wie folgt an.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Ändern des Schutzes für bereits gesicherte VMs mit Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Ausschließliche Sicherung des Betriebssystemdatenträgers beim Konfigurieren der Sicherung mit Azure CLI

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Ausschließliche Sicherung des Betriebssystemdatenträgers beim Ändern des Schutzes mit Azure CLI

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Wiederherstellen von Datenträgern mit Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Ausschließliches Wiederherstellen von Betriebssystemdatenträgern mit Azure CLI

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Abrufen des geschützten Elements zum Abrufen von Datenträgerausschlussdetails mit Azure CLI

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Dem geschützten Element wird wie folgt ein zusätzlicher **diskExclusionProperties** -Parameter hinzugefügt:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Abrufen des Sicherungsauftrags mit Azure CLI

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Mit diesem Befehl können Sie die Details zu den gesicherten Datenträgern und ausgeschlossenen Datenträgern wie unten dargestellt anzeigen:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Auflisten von Wiederherstellungspunkten mit Azure CLI

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Dadurch erhalten Sie Informationen zur Anzahl der an die VM angefügten und dort gesicherten Datenträger.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Abrufen des Wiederherstellungspunkts mit Azure CLI

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Jeder Wiederherstellungspunkt verfügt über die Informationen der ein- und ausgeschlossenen Datenträger:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Entfernen von Datenträgerausschlusseinstellungen und Abrufen eines geschützten Elements mit Azure CLI

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Wenn Sie diese Befehle ausführen, wird `"diskExclusionProperties": null` angezeigt.

## <a name="using-powershell"></a>PowerShell

Stellen Sie sicher, dass Sie Azure PowerShell Version 3.7.0 oder höher verwenden.

### <a name="enable-backup-with-powershell"></a>Aktivieren der Sicherung mit PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>Ausschließliche Sicherung des Betriebssystemdatenträgers beim Konfigurieren der Sicherung mit PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>Abrufen des Sicherungselementobjekts zur Übergabe beim Ändern des Schutzes mit PowerShell

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $Vault.ID -FriendlyName "V2VM"
```

Sie müssen das oben genannte **$item** -Objekt in den folgenden Cmdlets an den Parameter **–Item** übergeben.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>Ändern des Schutzes für bereits gesicherte VMs mit PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>Ausschließliche Sicherung des Betriebssystemdatenträgers beim Ändern des Schutzes mit PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>Zurücksetzen der Datenträgerausschlusseinstellung mit PowerShell

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Wiederherstellen von selektiven Datenträgern mit PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [Strings]
```

### <a name="restore-only-os-disk-with-powershell"></a>Ausschließliches Wiederherstellen von Betriebssystemdatenträgern mit PowerShell

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Im Azure-Portal können Sie die ein- und ausgeschlossenen Datenträger im Detailbereich der VM-Sicherung und im Detailbereich des Sicherungsauftrags anzeigen.  Wenn Sie während der Wiederherstellung den Wiederherstellungspunkt für die Wiederherstellung auswählen, können Sie die gesicherten Datenträger in diesem Wiederherstellungspunkt anzeigen.

Hier können Sie die ein- und ausgeschlossenen Datenträger für einen virtuellen Computer im Portal im Detailbereich der VM-Sicherung anzeigen:

![Anzeigen von ein- und ausgeschlossenen Datenträgern im Detailbereich der Sicherung](./media/selective-disk-backup-restore/backup-details.png)

Hier können Sie die ein- und ausgeschlossenen Datenträger in einer Sicherung im Detailbereich des Auftrags anzeigen:

![Anzeigen von ein- und ausgeschlossenen Datenträgern im Detailbereich des Auftrags](./media/selective-disk-backup-restore/job-details.png)

Hier können Sie die gesicherten Datenträger während der Wiederherstellung anzeigen, wenn Sie den Wiederherstellungspunkt für die Wiederherstellung auswählen:

![Anzeigen gesicherter Datenträger während der Wiederherstellung](./media/selective-disk-backup-restore/during-restore.png)

Das Konfigurieren der selektiven Datenträgersicherung für eine VM über das Azure-Portal ist auf die Option **Nur Betriebssystemdatenträger sichern** beschränkt. Um die selektive Datenträgersicherung auf einer bereits gesicherten VM oder für den erweiterten Ein- oder Ausschluss bestimmter Datenträger eines virtuellen Computers zu verwenden, setzen Sie PowerShell oder Azure CLI ein.

>[!NOTE]
>Wenn Daten über mehrere Datenträger verteilt sind, stellen Sie sicher, dass alle abhängigen Datenträger in der Sicherung enthalten sind. Wenn Sie nicht alle abhängigen Datenträger in einem Volume sichern, wird das Volume, in dem einige nicht gesicherte Datenträger enthalten sind, während der Wiederherstellung nicht erstellt.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>„Nur Betriebssystemdatenträger sichern“ im Azure-Portal

Wenn Sie die Sicherung im Azure-Portal aktivieren, können Sie die Option **Nur Betriebssystemdatenträger sichern** auswählen. Sie können also die Sicherung Ihres virtuellen Azure-Computers mit dem Betriebssystemdatenträger konfigurieren und alle daran angefügten Datenträger ausschließen.

![Konfigurieren der ausschließlichen Sicherung des Betriebssystemdatenträgers](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Verwenden der Azure-REST-API

Sie können Azure VM Backup mit einigen wenigen ausgewählten Datenträgern konfigurieren, oder Sie können den Schutz einer vorhandenen VM so ändern, dass einige Datenträger eingeschlossen/ausgeschlossen werden, wie [hier](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup) dokumentiert.

## <a name="selective-disk-restore"></a>Selektive Datenträgerwiederherstellung

Selektive Datenträgerwiederherstellung ist eine zusätzliche Funktionalität, die Sie nutzen können, wenn Sie die selektive Datenträgersicherung aktivieren. Mit dieser Funktionalität können Sie selektiv Datenträger von allen in einem Wiederherstellungspunkt gesicherten Datenträgern wiederherstellen. Dies ist effizienter und zeitsparend in Szenarios, in denen Sie wissen, welche Datenträger wiederhergestellt werden müssen.

- Der Betriebssystemdatenträger ist standardmäßig in der Sicherung und Wiederherstellung des virtuellen Computers eingeschlossen und kann nicht ausgeschlossen werden.
- Die selektive Datenträgerwiederherstellung wird nur für Wiederherstellungspunkte unterstützt, die nach der Aktivierung der Datenträgerausschlussfunktion erstellt wurden.
- Sicherungen mit der Datenträgerausschluss-Einstellung **EIN** unterstützen nur die Option **Datenträgerwiederherstellung** . Die Wiederherstellungsoptionen **VM-Wiederherstellung** oder **Vorhandene ersetzen** werden in diesem Fall nicht unterstützt.

![Die Optionen zum Wiederherstellen der VM und zum Ersetzen der vorhandenen VM stehen während des Wiederherstellungsvorgangs nicht zur Verfügung.](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Einschränkungen

Die Funktionalität der selektiven Datenträgersicherung wird für klassische und verschlüsselte VMs nicht unterstützt. Daher werden mit Azure Disk Encryption (ADE) mithilfe von BitLocker für die Verschlüsselung virtueller Windows-Computer verschlüsselte Azure-VMs und das dm-crypt-Feature für virtuelle Linux-Computer nicht unterstützt.

Die Wiederherstellungsoptionen **Neue VM erstellen** und **Vorhandene ersetzen** werden für den virtuellen Computer, für den die Funktionalität der selektiven Datenträgersicherung aktiviert ist, nicht unterstützt.

Zurzeit unterstützt die Azure VM-Sicherung keine VMs mit angefügten Ultra-Datenträgern oder freigegebenen Datenträgern. Eine selektive Datenträgersicherung kann in Fällen, die den Datenträger ausschließen und die VM sichern, nicht verwendet werden.

## <a name="billing"></a>Abrechnung

Die Sicherung virtueller Azure-Computer folgt dem vorhandenen, [hier](https://azure.microsoft.com/pricing/details/backup/) im Detail erläuterten Preismodell.

Die **Kosten der geschützten Instanz (PI, Protected Instance)** werden nur dann für den Betriebssystemdatenträger berechnet, wenn Sie sich für die Sicherung mithilfe der Option **Nur Betriebssystemdatenträger** entscheiden.  Wenn Sie die Sicherung konfigurieren und mindestens einen Datenträger auswählen, werden die PI-Kosten für alle Datenträger berechnet, die dem virtuellen Computer angefügt sind. Die **Sicherungsspeicherkosten** werden nur auf der Grundlage der eingeschlossenen Datenträger berechnet, sodass Sie bei den Speicherkosten sparen. Die **Momentaufnahmenkosten** werden immer für alle Datenträger der VM (ein- und ausgeschlossene) berechnet.

Wenn Sie das Feature „Regionsübergreifende Replikation“ (Cross Region Restore, CRR) gewählt haben, werden die [CRR-Preise](https://azure.microsoft.com/pricing/details/backup/) auf die Kosten für Sicherungsspeicher (nach Ausschluss des Datenträgers) angewendet.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Wie werden die PI-Kosten nur für die Sicherung des Betriebssystemdatenträgers in Windows und Linux berechnet?

Die PI-Kosten werden basierend auf der tatsächlichen (genutzten) Größe der VM berechnet.

- Windows: Die Berechnung des genutzten Speicherplatzes basiert auf dem Laufwerk, auf dem das Betriebssystem gespeichert ist (normalerweise Laufwerk C:).
- Linux: Die Berechnung des genutzten Speicherplatzes basiert auf dem Gerät, auf dem das Stammdateisystem ( / ) eingebunden ist.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Ich habe nur die Sicherung des Betriebssystemdatenträgers konfiguriert. Warum wird die Momentaufnahme für alle Datenträger ausgeführt?

Mithilfe der Features für selektive Datenträgersicherung können Sie die Speicherkosten für den Sicherungstresor einsparen, indem Sie die in der Sicherung enthaltenen Datenträger härten. Allerdings wird die Momentaufnahme für alle Datenträger erstellt, die an die VM angefügt sind. Deshalb werden die Momentaufnahmenkosten immer für alle Datenträger der VM (ein- und ausgeschlossene) berechnet. Weitere Informationen finden Sie unter [Abrechnung](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>Ich kann die Sicherung für den virtuellen Azure-Computer durch Ausschließen des Ultra-Datenträgers oder der an die VM angefügten freigegebenen Datenträger nicht konfigurieren.

Das Feature „Selektive Datenträgersicherung“ ist eine Funktion, die basierend auf der Lösung „Sicherung virtueller Azure-Computer“ bereitgestellt wird. Zurzeit unterstützt die Azure VM-Sicherung keine VMs mit angefügtem Ultra-Datenträger oder freigegebenen Datenträgern.

## <a name="next-steps"></a>Nächste Schritte

- [Unterstützungsmatrix für die Sicherung virtueller Azure-Computer](backup-support-matrix-iaas.md)
- [Häufig gestellte Fragen – Sicherung von Azure-VMs](backup-azure-vm-backup-faq.md)
