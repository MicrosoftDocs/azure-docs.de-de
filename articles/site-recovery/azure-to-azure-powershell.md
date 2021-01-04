---
title: Notfallwiederherstellung für Azure-VMs mit Azure PowerShell und Azure Site Recovery
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für virtuelle Azure-Computer mit Azure Site Recovery über Azure PowerShell einrichten.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 1570bd9dfa62caa749d5a3983b93c2555be058ec
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348728"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Einrichten der Notfallwiederherstellung für virtuelle Azure-Computer über Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung für virtuelle Azure-Computer über Azure PowerShell einrichten und testen.

Folgendes wird vermittelt:

> [!div class="checklist"]
> - Erstellen Sie einen Recovery Services-Tresor.
> - Festlegen des Tresorkontexts für die PowerShell-Sitzung
> - Vorbereiten des Tresors für die Replikation virtueller Azure-Computer
> - Erstellen von Netzwerkzuordnungen
> - Erstellen Sie Speicherkonten zum Replizieren der virtuellen Computer.
> - Replizieren virtueller Azure-Computer in eine Wiederherstellungsregion für die Notfallwiederherstellung
> - Führen Sie ein Testfailover aus, und überprüfen und bereinigen Sie das Testfailover.
> - Führen Sie ein Failover auf die Wiederherstellungsregion aus.

> [!NOTE]
> Möglicherweise stehen nicht alle Szenariofunktionen, die über das Portal verfügbar sind, über Azure PowerShell bereit. Unter anderem werden die folgenden Szenariofunktionen derzeit nicht über Azure PowerShell unterstützt:
> - Die Möglichkeit festzulegen, dass alle Datenträger eines virtuellen Computers repliziert werden sollen, ohne jeden einzelnen Datenträger des virtuellen Computers explizit angeben zu müssen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitungen:
- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](azure-to-azure-architecture.md) verstehen.
- Überprüfen Sie die [Supportanforderungen](azure-to-azure-support-matrix.md) für alle Komponenten.
- Sie verfügen über das Azure PowerShell-Modul `Az`. Wenn Sie PowerShell installieren oder aktualisieren müssen, befolgen Sie die Anweisungen unter [Handbuch zum Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Anmelden bei Ihrem Microsoft Azure-Abonnement

Melden Sie sich mit dem Cmdlet `Connect-AzAccount` bei Ihrem Azure-Abonnement an.

```azurepowershell
Connect-AzAccount
```

Wählen Sie Ihr Azure-Abonnement. Verwenden Sie das Cmdlet `Get-AzSubscription` zum Abrufen der Liste der Azure-Abonnements, auf die Sie Zugriff haben. Wählen Sie mit dem Cmdlet `Set-AzContext` das Azure-Abonnement aus, mit dem Sie arbeiten möchten.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Abrufen von Details des zu replizierenden virtuellen Computers

In diesem Artikel wird ein virtueller Computer in der Region „USA, Osten“ in der Region „USA, Westen 2“ repliziert und wiederhergestellt. Der zu replizierende virtuelle Computer enthält einen Betriebssystemdatenträger und einen einzelnen Datenträger für Daten. Der Name des virtuellen Computers im Beispiel lautet `AzureDemoVM`.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Rufen Sie detaillierte Informationen zu den Datenträgern des virtuellen Computers ab. Die Datenträgerdetails werden später beim Starten der Replikation für den virtuellen Computer verwendet.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Erstellen Sie eine Ressourcengruppe, in der der Recovery Services-Tresor erstellt werden soll.

> [!IMPORTANT]
> * Der Recovery Services-Tresor und die zu schützenden virtuellen Computer müssen sich an verschiedenen Azure-Standorten befinden.
> * Die Ressourcengruppe des Recovery Services-Tresors und die zu schützenden virtuellen Computer müssen sich an verschiedenen Azure-Standorten befinden.
> * Der Recovery Services-Tresor und die Ressourcengruppe, der er angehört, können sich am gleichen Azure-Standort befinden.

In dem Beispiel in diesem Artikel befindet sich der zu schützende virtuelle Computer in der Region „USA, Osten“. Die für die Notfallwiederherstellung ausgewählte Wiederherstellungsregion ist die Region „USA, Westen 2“. Der Recovery Services-Tresor und die Ressourcengruppe des Tresors befinden sich beide in der Wiederherstellungsregion (USA, Westen 2).

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Erstellen Sie einen Recovery Services-Tresor. In diesem Beispiel wird ein Recovery Services-Tresor mit dem Namen `a2aDemoRecoveryVault` in der Region „USA, Westen 2“ erstellt.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Festlegen des Tresorkontexts

Legen Sie den Tresorkontext zur Verwendung in der PowerShell-Sitzung fest. Nach dem Festlegen des Tresorkontexts werden Azure Site Recovery-Vorgänge in der PowerShell-Sitzung im Kontext des ausgewählten Tresors ausgeführt.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Für eine Migration von Azure zu Azure können Sie den Tresorkontext auf den neu erstellten Tresor festlegen:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Vorbereiten des Tresors für die Replikation virtueller Azure-Computer

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Erstellen eines Site Recovery-Fabricobjekts zur Darstellung der primären Region (Quellregion)

Das Fabricobjekt im Tresor stellt eine Azure-Region dar. Das primäre Fabricobjekt wird zur Darstellung der Azure-Region erstellt, der die im Tresor zu schützenden virtuellen Computer angehören. In dem Beispiel in diesem Artikel befindet sich der zu schützende virtuelle Computer in der Region „USA, Osten“.

- Pro Region kann nur ein Fabricobjekt erstellt werden.
- Wenn Sie zuvor im Azure-Portal die Site Recovery-Replikation für einen virtuellen Computer aktiviert haben, erstellt Site Recovery automatisch ein Fabricobjekt. Ist für eine Region bereits ein Fabricobjekt vorhanden, können Sie kein neues erstellen.

Bevor Sie beginnen, beachten Sie Folgendes: Site Recovery-Vorgänge werden asynchron ausgeführt. Wenn Sie einen Vorgang einleiten, wird ein Azure Site Recovery-Auftrag übermittelt und ein Auftragsverfolgungsobjekt zurückgegeben. Verwenden Sie das Auftragsverfolgungsobjekt, um den aktuellen Status des Auftrags (`Get-AzRecoveryServicesAsrJob`) abzurufen und den Status des Vorgangs zu überwachen.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Wenn virtuelle Computer aus mehreren Azure-Regionen im gleichen Tresor geschützt werden sollen, erstellen Sie ein Fabricobjekt für jede Azure-Quellregion.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Erstellen eines Site Recovery-Fabricobjekts zur Darstellung der Wiederherstellungsregion

Dieses Fabricobjekt stellt den Azure-Standort für die Wiederherstellung dar. Bei einem Failover werden virtuelle Computer in der durch das Wiederherstellungsfabric dargestellten Wiederherstellungsregion repliziert und wiederhergestellt. Die in diesem Beispiel verwendete Azure-Wiederherstellungsregion ist „USA, Westen 2“.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Erstellen eines Site Recovery-Schutzcontainers im primären Fabric

Der Schutzcontainer wird zum Gruppieren replizierter Elemente innerhalb eines Fabrics verwendet.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Erstellen eines Site Recovery-Schutzcontainers im Wiederherstellungsfabric

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

#### <a name="fabric-and-container-creation-when-enabling-zone-to-zone-replication"></a>Fabric- und Containererstellung beim Aktivieren der Replikation zwischen Zonen

Beim Aktivieren der Replikation zwischen Zonen wird nur ein Fabric erstellt. Es werden jedoch zwei Container erstellt. Verwenden Sie für die Region „Europa, Westen“ die folgenden Befehle, um den primären Container und den Schutzcontainer abzurufen:

```azurepowershell
$primaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-container"
$recoveryPprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $fabric -Name "asr-a2a-default-westeurope-t-container"
```

### <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Erstellen einer Schutzcontainerzuordnung zwischen dem primären Schutzcontainer und dem Schutzcontainer für die Wiederherstellung

Eine Schutzcontainerzuordnung ordnet den primären Schutzcontainer einem Schutzcontainer für die Wiederherstellung und einer Replikationsrichtlinie zu. Erstellen Sie eine Zuordnung für jede Replikationsrichtlinie, die Sie zum Replizieren virtueller Computer zwischen den beiden Schutzcontainern verwenden.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

#### <a name="protection-container-mapping-creation-when-enabling-zone-to-zone-replication"></a>Erstellen der Schutzcontainerzuordnung beim Aktivieren der Replikation zwischen Zonen

Verwenden Sie beim Aktivieren der Replikation zwischen Zonen den folgenden Befehl, um die Schutzcontainerzuordnung zu erstellen. Wenn die Region „Europa, Westen“ ist, lautet der Befehl:

```azurepowershell
$protContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimprotectionContainer -Name "westeurope-westeurope-24-hour-retention-policy-s"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Erstellen einer Schutzcontainerzuordnung für Failbacks (umgekehrte Replikation nach einem Failover)

Wenn ein virtueller Computer nach dem Failover wieder in die ursprüngliche Azure-Region zurückgeführt werden kann, führen Sie ein Failback aus. Bei einem Failback wird eine umgekehrte Replikation des virtuellen Computers, für den ein Failover erfolgte, aus der Failoverregion in die ursprüngliche Region ausgeführt. Bei einer umgekehrten Replikation werden die Rollen der ursprünglichen Region und der Wiederherstellungsregion getauscht. Die ursprüngliche Region wird nun zur neuen Wiederherstellungsregion, und die bisherige Wiederherstellungsregion wird nun zur primären Region. Die Schutzcontainerzuordnung für eine umgekehrte Replikation entspricht den getauschten Rollen der ursprünglichen Region und der Wiederherstellungsregion.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Erstellen eines Cachespeicherkontos und Zielspeicherkontos

Ein Cachespeicherkonto ist ein Standard-Speicherkonto in derselben Azure-Region wie der zu replizierende virtuelle Computer. Das Cachespeicherkonto wird zum vorübergehenden Speichern von Replikationsänderungen verwendet, bevor die Änderungen in die Azure-Wiederherstellungsregion verschoben werden. Sie können (müssen aber nicht) unterschiedliche Cachespeicherkonten für verschiedene Datenträger eines virtuellen Computers festlegen.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Bei virtuellen Computern, **die keine verwalteten Datenträger verwenden** , ist das Zielspeicherkonto das Speicherkonto in der Wiederherstellungsregion, in der Datenträger des virtuellen Computers repliziert werden. Das Zielspeicherkonto kann entweder ein Standard-Speicherkonto oder ein Premium-Speicherkonto sein. Wählen Sie die Art des erforderlichen Speicherkontos auf Grundlage der Datenänderungsrate (E/A-Schreibrate) für die Datenträger und der von Azure Site Recovery unterstützten Änderungsgrenzwerte für den Speichertyp aus.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Erstellen von Netzwerkzuordnungen

Eine Netzwerkzuordnung ordnet virtuelle Netzwerke in der primären Region virtuellen Netzwerken in der Wiederherstellungsregion zu. Die Netzwerkzuordnung gibt das virtuelle Azure-Netzwerk in der Wiederherstellungsregion an, auf das ein Failover eines virtuellen Computers im primären virtuellen Netzwerk ausgeführt werden soll. Ein virtuelles Azure-Netzwerk kann nur einem einzelnen virtuellen Azure-Netzwerk in einer Wiederherstellungsregion zugeordnet werden.

- Erstellen eines virtuellen Azure-Netzwerks in der Wiederherstellungsregion für ein Failover:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Rufen Sie das primäre virtuelle Netzwerk ab. Das VNET, mit dem der virtuelle Computer verbunden ist:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Erstellen einer Netzwerkzuordnung zwischen dem primären virtuellen Netzwerk und dem virtuellen Wiederherstellungsnetzwerk:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Erstellen einer Netzwerkzuordnung für die umgekehrte Richtung (Failback):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replizieren eines virtuellen Azure-Computers

Replizieren Sie den virtuellen Azure-Computer mit **verwalteten Datenträgern**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replizieren Sie den virtuellen Azure-Computer mit **nicht verwalteten Datenträgern**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Sobald der Replikationsvorgang erfolgreich gestartet wurde, werden Daten des virtuellen Computers in die Wiederherstellungsregion repliziert.

Zu Beginn des Replikationsprozesses wird zunächst durch Seeding eine Kopie der zu replizierenden Datenträger des virtuellen Computers in die Wiederherstellungsregion übernommen. Diese Phase wird als erste Replikationsphase bezeichnet.

Sobald die erste Replikation abgeschlossen ist, wird in die Phase der differenziellen Synchronisierung gewechselt. An diesem Punkt ist der virtuelle Computer geschützt, und es kann ein Testfailover ausgeführt werden. Nach Abschluss der ersten Replikation wechselt der Replikationsstatus des replizierten Elements, das den virtuellen Computer darstellt, in den Zustand **Geschützt**.

Überwachen Sie den Replikationsstatus und die Replikationsintegrität für den virtuellen Computer, indem Sie Details des entsprechenden replikationsgeschützten Elements abrufen.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Ausführen eines Testfailovers, Überprüfen und Bereinigen des Testfailovers

Wenn die Replikation für den virtuellen Computer den Status „Geschützt“ erreicht hat, kann ein Testfailover für den virtuellen Computer (für das replikationsgeschützte Element des virtuellen Computers) ausgeführt werden.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Führen Sie ein Testfailover aus.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Warten Sie, bis das Testfailover abgeschlossen wurde.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Wenn das Testfailover erfolgreich angeschlossen wurde, können Sie eine Verbindung mit dem virtuellen Testfailover-Computer herstellen und das Testfailover überprüfen.

Nachdem Sie den Test für den virtuellen Testfailover-Computer abgeschlossen haben, bereinigen Sie die Testkopie, indem Sie den Vorgang zum Bereinigen des Testfailovers starten. Dieser Vorgang löscht die Testkopie des virtuellen Computers, die vom Testfailover erstellt wurde.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Failover in Azure

Führen Sie ein Failover des virtuellen Computers auf einen bestimmten Wiederherstellungspunkt aus.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Wenn der Failoverauftrag erfolgreich war, können Sie ein Commit für das Failover ausführen.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Erneuter Schutz und Failback auf die Quellregion

Wenn Sie nach einem Failover zur ursprünglichen Region zurückkehren möchten, starten Sie eine umgekehrte Replikation für das replikationsgeschützte Element mit dem Cmdlet `Update-AzRecoveryServicesAsrProtectionDirection`.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Nachdem der erneute Schutz abgeschlossen ist, können Sie ein Failover in umgekehrter Richtung („USA, Westen“ auf „USA, Osten“) und ein Failback auf die Quellregion ausführen.

## <a name="disable-replication"></a>Deaktivieren der Replikation

Sie können die Replikation mithilfe des Cmdlets `Remove-AzRecoveryServicesAsrReplicationProtectedItem` deaktivieren.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicationProtectedItem
```

## <a name="next-steps"></a>Nächste Schritte

In der [Azure Site Recovery-PowerShell-Referenz ](/powershell/module/az.RecoveryServices) erfahren Sie, wie Sie andere Aufgaben wie das Erstellen von Wiederherstellungsplänen und das Testen des Failovers von Wiederherstellungsplänen mithilfe von PowerShell ausführen können.
