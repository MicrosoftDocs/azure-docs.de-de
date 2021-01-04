---
title: Häufige Fehler bei der Migration von einer klassischen Bereitstellung zu einer Azure Resource Manager-Bereitstellung
description: In diesem Artikel werden die Fehler aufgeführt und beschrieben, die bei der Migration von IaaS-Ressourcen von der Azure-Dienstverwaltung zu Azure Resource Manager am häufigsten auftreten.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 110d7186db97f6fac91b8fd785384a1c2ed7a8cd
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301665"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>Fehler, die häufig bei der Migration von einer klassischen Bereitstellung zu einer Azure Resource Manager-Bereitstellung auftreten

> [!IMPORTANT]
> Derzeit nutzen etwa 90 % der IaaS-VMs [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Seit dem 28. Februar 2020 gelten klassische VMs als veraltet. Sie werden am 1. März 2023 vollständig eingestellt. [Erfahren Sie mehr]( https://aka.ms/classicvmretirement) zu dieser Einstellung und den [Auswirkungen auf Sie](classic-vm-deprecation.md#how-does-this-affect-me).

In diesem Artikel werden die Fehler aufgeführt und beschrieben, die bei der Migration von IaaS-Ressourcen vom klassischen Azure-Bereitstellungsmodell zum Azure Resource Manager-Stapel am häufigsten auftreten.


## <a name="list-of-errors"></a>Liste der Fehler

| Fehlerzeichenfolge | Minderung |
| --- | --- |
| Interner Serverfehler |Dies ist in einigen Fällen ein vorübergehender Fehler, der nach einem erneuten Versuch nicht mehr auftritt. Wenn der Fehler weiterhin auftritt, [wenden Sie sich an den Azure-Support](../azure-portal/supportability/how-to-create-azure-support-request.md), da in diesem Fall eine Untersuchung der Plattformprotokolle erforderlich ist. <br><br> **HINWEIS:** Sobald der Incident vom Supportteam aufgenommen wurde, versuchen Sie nicht, das Problem selbst zu lösen, da dies unerwartete Folgen für Ihre Umgebung nach sich ziehen könnte. |
| Die Migration von Bereitstellung "{deployment-name}" im HostedService "{hosted-service-name}" wird nicht unterstützt, da es sich um eine PaaS-Bereitstellung (Web/Worker) handelt. |Dies passiert, wenn eine Bereitstellung eine Web- oder Workerrolle enthält. Da die Migration nur für virtuelle Computer unterstützt wird, entfernen Sie die Web- oder Workerrolle aus der Bereitstellung, und wiederholen Sie die Migration. |
| Bei der Bereitstellung von Vorlage "{template-name}" ist ein Fehler aufgetreten. CorrelationId={guid} |Im Back-End des Migrationsdienst verwenden wir Azure Resource Manager-Vorlagen, um Ressourcen im Azure Resource Manager-Stapel zu erstellen. Da Vorlagen idempotent sind, können Sie den Migrationsvorgang üblicherweise sicher wiederholen, um diesen Fehler zu beheben. Wenn der Fehler weiterhin auftritt, [wenden Sie sich an den Azure-Support](../azure-portal/supportability/how-to-create-azure-support-request.md), und geben Sie die CorrelationId an. <br><br> **HINWEIS:** Sobald der Incident vom Supportteam aufgenommen wurde, versuchen Sie nicht, das Problem selbst zu lösen, da dies unerwartete Folgen für Ihre Umgebung nach sich ziehen könnte. |
| Das virtuelle Netzwerk "{virtual-network-name}" ist nicht vorhanden. |Dieser Fehler kann auftreten, wenn Sie das virtuelle Netzwerk im neuen Azure-Portal erstellt haben. Der tatsächliche Name des virtuellen Netzwerk folgt dem Muster „Gruppe * \<VNET name>“. |
| Der virtuelle Computer "{vm-name}" im HostedService "{hosted-service-name}" enthält die Erweiterung "{extension-name}", die in Azure Resource Manager nicht unterstützt wird. Es empfiehlt sich, sie vom virtuellen Computer zu deinstallieren, bevor Sie die Migration fortsetzen. |XML-Erweiterungen wie „BGInfo 1.\*“ werden in Azure Resource Manager nicht unterstützt. Daher können diese Erweiterungen nicht migriert werden. Wenn die Installation dieser Erweiterungen auf dem virtuellen Computer beibehalten wird, werden sie vor dem Abschließen der Migration automatisch deinstalliert. |
| Die VM „{vm-name}“ im HostedService „{hosted-service-name}“ enthält die Erweiterung „VMSnapshot/VMSnapshotLinux“, für die eine Migration derzeit nicht unterstützt wird. Deinstallieren Sie sie von der VM, und fügen Sie sie nach Abschluss der Migration über Azure Resource Manager wieder hinzu. |Dies ist das Szenario, in dem der virtuelle Computer für Azure Backup konfiguriert ist. Da dieses Szenario zurzeit nicht unterstützt wird, befolgen Sie die Problemumgehung unter https://aka.ms/vmbackupmigration. |
| Die VM "{vm-name}" im HostedService "{hosted-service-name}" enthält die Erweiterung "{extension-name}", deren Status nicht von der VM gemeldet wird. Daher kann diese VM nicht migriert werden. Stellen Sie sicher, dass der Erweiterungsstatus gemeldet wird, oder deinstallieren Sie die Erweiterung von der VM, und wiederholen Sie die Migration. <br><br> Die VM "{vm-name}" im HostedService "{hosted-service-name}" enthält die Erweiterung "{extension-name}", die den Handlerstatus "{handler-status}" meldet. Daher kann die VM nicht migriert werden. Stellen Sie sicher, dass der Erweiterungshandlerstatus "{handler-status}" gemeldet wird, oder deinstallieren Sie die Erweiterung von der VM, und wiederholen Sie die Migration. <br><br> Der VM-Agent für die VM "{vm-name}" im HostedService "{hosted-service-name}" meldet für den Agent den Gesamtstatus "Nicht bereit". Daher wird die VM möglicherweise nicht migriert, wenn sie über eine migrierbare Erweiterung verfügt. Stellen Sie sicher, dass der VM-Agent den Gesamtstatus des Agents als "Bereit" meldet. Lesen Sie https://aka.ms/classiciaasmigrationfaqs. |Gast-Agent- und VM-Erweiterungen in Azure benötigen ausgehenden Internetzugriff auf das Speicherkonto des virtuellen Computers, um ihren Status aufzufüllen. Häufige Fehlerursachen für den Statusfehler sind folgende: <li> Eine Netzwerksicherheitsgruppe blockiert den ausgehenden Zugriff auf das Internet. <li> Im virtuellen Netzwerk sind lokale DNS-Server vorhanden, und die DNS-Verbindung wurde unterbrochen. <br><br> Wenn weiterhin nicht unterstützte Status angezeigt werden, können Sie die Erweiterungen deinstallieren, um diese Prüfung zu überspringen und mit der Migration fortzufahren. |
| Die Migration von Bereitstellung "{deployment-name}" im HostedService "{hosted-service-name}" wird nicht unterstützt, da die Bereitstellung mehrere Verfügbarkeitsgruppen aufweist. |Zurzeit können nur gehostete Dienste mit höchstens einer Verfügbarkeitsgruppe migriert werden. Verschieben Sie zur Umgehung dieses Problems die zusätzlichen Verfügbarkeitsgruppen und virtuellen Computer in diesen Verfügbarkeitsgruppen in einen anderen gehosteten Dienst. |
| Die Migration von Bereitstellung "{deployment-name}" im HostedService "{hosted-service-name}" wird nicht unterstützt, da die Bereitstellung über virtuelle Computer verfügt, die nicht Teil der Verfügbarkeitsgruppe sind, obwohl der HostedService einen virtuellen Computer enthält. |Um dieses Problem zu umgehen, verschieben Sie entweder alle virtuellen Computer in eine einzige Verfügbarkeitsgruppe, oder entfernen Sie alle virtuellen Computer aus der Verfügbarkeitsgruppe im gehosteten Dienst. |
| Das Speicherkonto / der HostedService / das virtuelle Netzwerk {virtual-network-name} wird gerade migriert und kann daher nicht geändert werden. |Dieser Fehler tritt auf, wenn der Vorgang zur Vorbereitung der Migration für die Ressource abgeschlossen ist und ein Vorgang ausgelöst wird, der die Ressource ändern würde. Aufgrund der Sperre auf der Verwaltungsebene nach dem Vorbereitungsvorgang werden alle Änderungen an der Ressource blockiert. Um die Verwaltungsebene zu entsperren, können Sie den Vorgang zum Commit der Migration ausführen, um die Migration abzuschließen, oder Sie führen den Vorgang zum Abbrechen der Migration aus, um ein Rollback des Vorbereitungsvorgangs auszuführen. |
| Die Migration ist für den HostedService „{hosted-service-name}“ nicht zulässig, da die darin enthaltene VM „{vm-name}“ den Zustand „RoleStateUnknown“ aufweist. Die Migration ist nur dann zulässig, wenn sich die VM in einem der folgenden Zustände befindet: Wird ausgeführt, Beendet, Beendet (Zuordnung aufgehoben). |Für den virtuellen Computer findet möglicherweise gerade eine Statusübergang statt. Dies geschieht üblicherweise während eines Updatevorgangs für HostedService, beispielsweise während eines Neustarts, der Installation einer Erweiterung usw. Es empfiehlt sich, den Updatevorgang des HostedService abzuschließen, bevor Sie versuchen, die Migration durchzuführen. |
| Die Bereitstellung „{deployment-name}“ im HostedService „{hosted-service-name}“ enthält den virtuellen Computer „{vm-name}“ mit dem Datenträger „{data-disk-name}“, dessen physische Blobgröße ({size-of-the-vhd-blob-backing-the-data-disk} Bytes) nicht der logischen VM-Datenträgergröße ({size-of-the-data-disk-specified-in-the-vm-api} Bytes) entspricht. Die Migration wird ohne Angabe einer Größe für den Datenträger für den virtuellen Azure Resource Manager-Computer fortgesetzt. | Dieser Fehler kann auftreten, wenn Sie die VHD-Blobgröße angepasst haben, ohne die Größe im VM-API-Modell zu aktualisieren. Ausführliche Schritte zur Behebung finden Sie [weiter unten](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes).|
| Beim Überprüfen von Datenträger "<Name des Datenträgers>" mit dem Medienlink "<Datenträger-URI>" für den virtuellen Computer "<Name des virtuellen Computers>" im Clouddienst "<Name des Clouddiensts>" ist eine Speicherausnahme aufgetreten. Stellen Sie sicher, dass dieser virtuelle Computer Zugriff auf den Medienlink der virtuellen Festplatte hat. | Dieser Fehler kann auftreten, wenn die Datenträger des virtuellen Computers gelöscht wurden oder der Zugriff auf sie nicht mehr möglich ist. Vergewissern Sie sich, dass die Datenträger des virtuellen Computers vorhanden sind.|
| Die VM "<VM-Name>" im HostedService "<Name des Clouddiensts>" enthält einen Datenträger mit dem MediaLink "<VHD-URI>". Dieser weist einen Blobnamen "<VHD-Blobname>" auf, der in Azure Resource Manager nicht unterstützt wird. | Dieser Fehler tritt auf, wenn der Name des Blobs einen Schrägstrich (/) enthält. Dies wird vom Compute-Ressourcenanbieter derzeit nicht unterstützt. |
| Für die Bereitstellung „{Bereitstellungsname}“ im HostedService „{Clouddienstname}“ ist eine Migration nicht zulässig, da sie sich nicht im regionalen Bereich befindet. Informationen zum Verschieben dieser Bereitstellung in den regionalen Bereich finden Sie unter „http:\//aka.ms/regionalscope“. | 2014 wurde für Azure angekündigt, dass Netzwerkressourcen aus dem Clusterebenenbereich in den regionalen Bereich verschoben werden. Ausführlichere Informationen finden Sie unter [https://aka.ms/regionalscope](https://aka.ms/regionalscope). Dieser Fehler tritt auf, wenn für die zu migrierende Bereitstellung kein Updatevorgang durchgeführt wurde, bei dem sie automatisch in einen regionalen Bereich verschoben wird. Die beste Problemumgehung besteht darin, einem virtuellen Computer entweder einen Endpunkt oder einen Datenträger hinzuzufügen und dann zu versuchen, den Migrationsvorgang erneut durchzuführen. <br> Informationen hierzu finden Sie unter [Gewusst wie: Einrichten von Endpunkten auf einem klassischen virtuellen Windows-Computer in Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) bzw. [Anfügen eines Datenträgers an einen virtuellen Windows-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde](/azure/virtual-machines/linux/attach-disk-portal).|
| Eine Migration wird für das virtuelle Netzwerk „{VNET-Name}“ nicht unterstützt, weil es PaaS-Bereitstellungen ohne Gateway umfasst. | Dieser Fehler tritt auf, wenn Sie PaaS-Bereitstellungen ohne Gateway wie Application Gateway- oder API Management-Dienste haben, die mit dem virtuellen Netzwerk verbunden sind.|


## <a name="detailed-mitigations"></a>Ausführliche Behebungen

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtueller Computer mit einem Datenträger, dessen physische Blobgröße in Bytes nicht der logischen VM-Datenträgergröße in Bytes entspricht.

Dieser Fall tritt ein, wenn die logische Größe des Datenträgers nicht auf die tatsächliche Größe der VHD-Blobgröße abgestimmt ist. Dies lässt sich problemlos mit den folgenden Befehlen überprüfen:

#### <a name="verifying-the-issue"></a>Überprüfen des Problems

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Beheben des Problems

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Verschieben eines virtuellen Computers in ein anderes Abonnement nach Abschluss der Migration

Nach Abschluss des Migrationsvorgangs möchten Sie den virtuellen Computer möglicherweise in ein anderes Abonnement verschieben. Wenn Sie allerdings ein Geheimnis oder ein Zertifikat auf dem virtuellen Computer festgelegt haben, das auf eine Key Vault-Ressource verweist, ist die Verschiebung derzeit nicht möglich. Dies können Sie wie folgt umgehen: 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Ausführliche technische Informationen zur plattformgestützten Migration vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planen der Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von PowerShell](migration-classic-resource-manager-ps.md)
* [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe der Befehlszeilenschnittstelle](migration-classic-resource-manager-cli.md)
* [Communitytools zur Unterstützung beim Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Antworten auf die am häufigsten gestellten Fragen zum Migrieren von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](migration-classic-resource-manager-faq.md)
