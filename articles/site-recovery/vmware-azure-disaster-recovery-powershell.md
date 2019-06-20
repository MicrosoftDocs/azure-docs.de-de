---
title: Einrichten der Notfallwiederherstellung von virtuellen VMware-Computern in Azure mithilfe von PowerShell in Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Replikation und Failover in Azure für die Notfallwiederherstellung von virtuellen VMware-Computern mithilfe von PowerShell in Azure Site Recovery einrichten.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 04/08/2019
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 5490149f199c2d7887716ceae3f035527ad33961
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66170051"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Einrichten der Notfallwiederherstellung von virtuellen VMware-Computern in Azure mithilfe von PowerShell

In diesem Artikel erfahren Sie etwas über das Replizieren virtueller VMware-Computer in VMM-Clouds und das Ausführen eines Failovers für diese mithilfe von Azure Site Recovery-PowerShell.

Folgendes wird vermittelt:

> [!div class="checklist"]
> - Erstellen eines Recovery Services-Tresors und Einrichten des Tresorkontexts.
> - Überprüfen der Registrierung des Servers im Tresor.
> - Einrichten der Replikation einschließlich einer Replikationsrichtlinie. Hinzufügen Ihres vCenter-Servers und Erkennung virtueller Computer.
> - Hinzufügen eines vCenter-Servers und Erkennung
> - Erstellen von Speicherkonten zum Speichern von Replikationsdaten und Replizieren der virtuellen Computer.
> - Ausführen eines Failovers. Konfigurieren von Failovereinstellungen, Vornehmen von Einstellungen für das Replizieren virtueller Computer.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitung:

- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](vmware-azure-architecture.md) verstehen.
- Überprüfen Sie die [Supportanforderungen](site-recovery-support-matrix-to-azure.md) für alle Komponenten.
- Sie verfügen über das Azure PowerShell-Modul `Az`. Wenn Sie PowerShell installieren oder aktualisieren müssen, befolgen Sie die Anweisungen unter [Handbuch zum Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="log-into-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Cmdlet „Connect-AzAccount“ bei Ihrem Azure-Konto an:

```azurepowershell
Connect-AzAccount
```
Wählen Sie das Azure-Abonnement aus, in dem Sie Ihre VMware-VMs replizieren möchten. Verwenden Sie das Cmdlet „Get-AzSubscription“ zum Abrufen der Liste der Azure-Abonnements, auf die Sie Zugriff haben. Wählen Sie mit dem Cmdlet „Select-AzSubscription“ das gewünschte Azure-Abonnement aus, mit dem Sie arbeiten möchten.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Richten Sie einen Recovery Services-Tresor ein.

1. Erstellen Sie eine Ressourcengruppe, in der der Recovery Services-Tresor erstellt werden soll. Im nachstehenden Beispiel hat die Ressourcengruppe den Namen VMwareDRtoAzurePS und wird in der Region „Asien, Osten“ erstellt.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Erstellen Sie einen Recovery Services-Tresor. Im nachstehenden Beispiel hat der Recovery Services-Tresor den Namen VMwareDRToAzurePs und wird in der Region „Asien, Osten“ und in der Ressourcengruppe, die im vorherigen Schritt erstellt wurde, erstellt.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. Laden Sie den Tresorregistrierungsschlüssel für den Tresor herunter. Mit dem Tresorregistrierungsschlüssel wird der lokale Konfigurationsserver bei diesem Tresor registriert. Die Registrierung ist ein Teil des Installationsvorgangs der Konfigurationsserversoftware.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Verwenden Sie den heruntergeladenen Registrierungsschlüssel, und führen Sie die Schritte in den nachstehend angegebenen Artikeln aus, um die Installation und Registrierung des Konfigurationsservers abzuschließen.
   - [Auswählen Ihrer Schutzziele](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [Einrichten der Quellumgebung](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>Festlegen des Tresorkontexts

Legen Sie mit dem Cmdlet Set-ASRVaultContext den Tresorkontext fest. Nach dem Festlegen werden nachfolgende Azure Site Recovery-Vorgänge in der PowerShell-Sitzung im Kontext des ausgewählten Tresors ausgeführt.

> [!TIP]
> Im Lieferumfang des Azure Site Recovery-PowerShell-Moduls (Az.RecoveryServices) sind einfach zu verwendende Aliase für die meisten Cmdlets enthalten. Die Cmdlets im Model weisen die Form *\<Vorgang>-**AzRecoveryServicesAsr**\<Objekt>* auf und verfügen über gleichwertige Aliase in der Form *\<Vorgang>-**ASR**\<Objekt>* . In diesem Artikel werden für eine bessere Lesbarkeit die Cmdlet-Aliase verwendet.

Im nachstehenden Beispiel wird mit den Tresordetails aus der $vault-Variable der Tresorkontext für die PowerShell-Sitzung angegeben.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Als Alternative zum Cmdlet „Set-ASRVaultContext“ kann der Tresorkontext auch mithilfe des Cmdlets „Import-AzRecoveryServicesAsrVaultSettingsFile“ festgelegt werden. Geben Sie für das Cmdlet „Import-AzRecoveryServicesAsrVaultSettingsFile“ mithilfe des Parameters „-path“ den Pfad der Tresorregistrierungsschlüssel-Datei an. Beispiel:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
In späteren Abschnitten dieses Artikels wird davon ausgegangen, dass der Tresorkontext für Azure Site Recovery-Vorgänge festgelegt wurde.

## <a name="validate-vault-registration"></a>Überprüfen der Tresorregistrierung

In diesem Beispiel liegt Folgendes vor:

- Ein Konfigurationsserver (**ConfigurationServer**) wurde bei diesem Tresor registriert.
- Ein zusätzlicher Prozessserver (**ScaleOut-ProcessServer**) wurde bei *ConfigurationServer* registriert.
- Konten (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) wurden auf dem Konfigurationsserver eingerichtet. Mit diesen Konten wird der vCenter-Server für die Erkennung virtueller Computer hinzugefügt und die Mobility Service-Clientsoftware auf Windows- und Linux-Servern, die repliziert werden sollen, per Push installiert.

1. Registrierte Konfigurationsserver werden von einem Fabricobjekt in Site Recovery dargestellt. Rufen Sie die Liste der Fabricobjekte im Tresor ab, und identifizieren Sie den Konfigurationsserver.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-ASRFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. Identifizieren Sie die Prozessserver, die zum Replizieren von Computern verwendet werden können.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   In obiger Ausgabe gehört ***$ProcessServers[0]*** zu *ScaleOut-ProcessServer*, und ***$ProcessServers[1]*** gehört zu der Prozessserverrolle auf *ConfigurationServer*.

3. Identifizieren Sie Konten, die auf dem Konfigurationsserver eingerichtet wurden.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   In obiger Ausgabe gehört ***$AccountHandles[0]*** zum Konto *vCenter_account*, ***$AccountHandles[1]*** zu *WindowsAccount* und ***$AccountHandles[2]*** zu *LinuxAccount*.

## <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

In diesem Schritt werden zwei Replikationsrichtlinien erstellt. Eine Richtlinie zum Replizieren von virtuellen VMware-Computern in Azure und die andere zum Replizieren virtueller Failovercomputer, die in Azure ausgeführt werden, auf den lokalen VMware-Standort.

> [!NOTE]
> Die meisten Azure Site Recovery-Vorgänge werden asynchron ausgeführt. Wenn Sie einen Vorgang einleiten, wird ein Azure Site Recovery-Auftrag übermittelt und ein Auftragsverfolgungsobjekt zurückgegeben. Dieses Auftragsverfolgungsobjekt kann zum Überwachen des Status des Vorgangs verwendet werden.

1. Erstellen Sie eine Replikationsrichtlinie mit dem Namen *ReplicationPolicy* zum Replizieren virtueller VMware-Computer in Azure mit den angegebenen Eigenschaften.

   ```azurepowershell
   $Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Erstellen Sie eine Replikationsrichtlinie für Failbacks von Azure auf die lokalen VMware-Standorte.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Verwenden Sie die Auftragsdetails in *$Job_FailbackPolicyCreate* zum Nachverfolgen des Vorgangs bis zum Abschluss.

   * Erstellen Sie eine Schutzcontainerzuordnung zum Zuordnen von Replikationsrichtlinien beim Konfigurationsserver.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Hinzufügen eines vCenter-Servers und Durchführen einer Erkennung virtueller Computer

Fügen Sie einen vCenter-Server anhand der IP-Adresse oder des Hostnamens hinzu. Der Parameter **-port** gibt den Port auf dem vCenter-Server für die Verbindung an, der Parameter **-Name** gibt einen Anzeigenamen für den vCenter-Server an, und der Parameter **-Account** gibt das Kontohandle auf dem Konfigurationsserver zum Erkennen von virtuellen Computern an, die vom vCenter-Server verwaltet werden.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>Erstellen von Speicherkonten für die Replikation

In diesem Schritt werden nur die Speicherkonten für die Replikation erstellt. Diese Speicherkonten werden später zum Replizieren virtueller Computer verwendet. Stellen Sie sicher, dass die Speicherkonten in derselben Azure-Region wie der Tresor erstellt werden. Sie können diesen Schritt überspringen, wenn Sie ein vorhandenes Speicherkonto für die Replikation verwenden möchten.

> [!NOTE]
> Beim Replizieren lokaler virtuellen Computer in einem Storage Premium-Konto müssen Sie ein zusätzliches Storage Standard-Konto (Protokollspeicherkonto) angeben. Das Protokollspeicherkonto enthält Replikationsprotokolle als temporären Speicher, bis die Protokolle auf das Storage Premium-Ziel angewendet werden können.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>Replizieren von VMware-VMs

Es dauert etwa 15 bis 20 Minuten, bis virtuelle Computer auf dem vCenter-Server erkannt werden. Nach der Erkennung wird in Azure Site Recovery für jeden ermittelten virtuellen Computer ein Objekt für ein schützbares Element erstellt. In diesem Schritt werden drei der erkannten virtuellen Computer in den im vorherigen Schritt erstellten Azure Storage-Konten repliziert.

Sie benötigen die folgenden Details zum Schützen eines erkannten virtuellen Computers:

* Das zu replizierende schützbare Element.
* Das Speicherkonto, in dem der virtuelle Computer repliziert werden soll. Darüber hinaus ist ein Protokollspeicher erforderlich, um virtuelle Computer in einem Storage Premium-Konto zu schützen.
* Der für die Replikation verwendete Prozessserver. Die Liste der verfügbaren Prozessserver wurde abgerufen und in den Variablen ***$ProcessServers[0]*** *(ScaleOut-ProcessServer)* und ***$ProcessServers[1]*** *(ConfigurationServer)* gespeichert.
* Das Konto, über das die Mobility Service-Clientsoftware per Push auf den Computern installiert werden soll. Die Liste der verfügbaren Konten wurde abgerufen und in der Variable ***$AccountHandles*** gespeichert.
* Die Schutzcontainerzuordnung der Replikationsrichtlinie, die für die Replikation verwendet werden soll.
* Die Ressourcengruppe, in der virtuelle Computer bei einem Failover erstellt werden müssen.
* (Optional:) Das virtuelle Azure-Netzwerk und das Subnetz, mit denen der virtuelle Failovercomputer verbunden werden soll.

Replizieren Sie nun die folgenden virtuellen Computer mit den Einstellungen in dieser Tabelle.


|Virtueller Computer  |Prozessserver        |Speicherkonto              |Protokollspeicherkonto  |Richtlinie           |Konto für die Installation des Mobilitätsdiensts|Zielressourcengruppe  | Virtuelles Zielnetzwerk  |Zielsubnetz  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableReplication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Nachdem der Auftrag zum Aktivieren der Replikation erfolgreich abgeschlossen wurde, wird eine erste Replikation für die virtuellen Computer gestartet. Die erste Replikation kann abhängig von der Menge der zu replizierenden Daten und der für die Replikation verfügbaren Bandbreite einige Zeit dauern. Wenn die erste Replikation abgeschlossen ist, wird der virtuelle Computer in einen geschützten Zustand überführt. Nachdem der virtuelle Computer einen geschützten Zustand erreicht hat, können Sie ein Testfailover für den virtuellen Computer ausführen, ihn Wiederherstellungsplänen hinzufügen usw.

Sie können den Replikationsstatus und die Replikationsintegrität des virtuellen Computers mit dem Cmdlet Get-ASRReplicationProtectedItem überprüfen.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Konfigurieren der Failovereinstellungen

Die Failovereinstellungen für geschützte Computer können mit dem Cmdlet Set-ASRReplicationProtectedItem aktualisiert werden. Einige der Einstellungen, die mit diesem Cmdlet aktualisiert werden können, sind die folgenden:
* Name des virtuellen Computers, der bei einem Failover erstellt werden soll
* VM-Größe des virtuellen Computers, der bei einem Failover erstellt werden soll
* Virtuelles Azure-Netzwerk und Subnetz, mit denen die Netzwerkschnittstellenkarten des virtuellen Computers bei einem Failover verbunden werden sollen
* Failover auf verwalteten Datenträgern
* Anwenden des Azure-Hybridvorteils
* Weisen Sie eine statische IP-Adresse aus dem virtuellen Zielnetzwerk zu, die dem virtuellen Computer bei einem Failover zugewiesen werden soll.

In diesem Beispiel aktualisieren wir die VM-Größe des virtuellen Computers, der bei einem Failover für den virtuellen Computer *Win2K12VM1* erstellt werden soll, und geben an, dass der virtuelle Computer bei einem Failover verwaltete Datenträger verwenden soll.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

1. Führen Sie eine DR-Übung (Testfailover) wie folgt aus:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Nachdem der Testfailover-Auftrag erfolgreich abgeschlossen wurde, sehen Sie, dass ein virtueller Computer mit dem Namenssuffix *-Test* (in diesem Fall „Win2K12VM1-Test“) in Azure erstellt wurde.
3. Sie können nun eine Verbindung mit dem virtuellen Testfailover-Computer herstellen und das Testfailover überprüfen.
4. Bereinigen Sie das Testfailover mit dem Cmdlet Start-ASRTestFailoverCleanupJob. Dieser Vorgang löscht den virtuellen Computer, der im Rahmen des Testfailovers erstellt wurde.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Failover in Azure

In diesem Schritt führen wir ein Failover des virtuellen Computers Win2K12VM1 auf einem bestimmten Wiederherstellungspunkt aus.

1. Rufen Sie eine Liste der für das Failover zu verwendenden verfügbaren Wiederherstellungspunkte auf:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Nachdem das Failover erfolgreich ausgeführt wurde, können Sie den Failovervorgang committen und die umgekehrte Replikation von Azure zurück zum lokalen VMware-Standort einrichten.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich in der [Azure Site Recovery-PowerShell-Referenz](https://docs.microsoft.com/powershell/module/Az.RecoveryServices), wie Sie mehr Aufgaben automatisieren können.
