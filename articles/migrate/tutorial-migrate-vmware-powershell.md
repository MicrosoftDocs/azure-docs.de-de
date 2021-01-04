---
title: 'Migrieren virtueller VMware-Computer zu Azure (ohne Agent): PowerShell'
description: Hier erfahren Sie, wie Sie eine Migration virtueller VMware-Computer ohne Agent mit Azure Migrate über PowerShell durchführen.
author: rahulg1190
ms.author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.openlocfilehash: 187e17bcf385ba6b3c17566fc00e5ee77cf009fe
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752480"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Migrieren virtueller VMware-Computer zu Azure (ohne Agent): PowerShell

In diesem Artikel erfahren Sie, wie Sie ermittelte virtuelle VMware-Computer ohne Agent migrieren und dabei Azure PowerShell für [Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) verwenden. 

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Abrufen ermittelter virtueller VMware-Computer in einem Azure Migrate-Projekt
> * Beginnen mit der Replikation von VMs
> * Aktualisieren von Eigenschaften für die Replikation virtueller Computer
> * Überwachen der Replikation
> * Führen Sie eine Testmigration aus, um sicherzustellen, dass alles wie erwartet funktioniert.
> * Durchführen einer vollständigen VM-Migration

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

1. [Absolvieren Sie das Tutorial zur Ermittlung](tutorial-discover-vmware.md), um Azure und VMware für die Migration vorzubereiten.
2. Es empfiehlt sich, das zweite Tutorial zum [Bewerten virtueller VMware-Computer](./tutorial-assess-vmware-azure-vm.md) zu absolvieren, bevor Sie sie zu Azure migrieren.
3. Sie verfügen über das Azure PowerShell-Modul `Az`. Wenn Sie PowerShell installieren oder aktualisieren müssen, führen Sie die [Schritte zum Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) aus.

## <a name="install-azure-migrate-powershell-module"></a>Installieren des Azure Migrate-PowerShell-Moduls

Das Azure Migrate-PowerShell-Modul ist als Vorschauversion verfügbar. Verwenden Sie zum Installieren des PowerShell-Moduls den folgenden Befehl: 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Anmelden bei Ihrem Microsoft Azure-Abonnement

Melden Sie sich mit dem Cmdlet `Connect-AzAccount` bei Ihrem Azure-Abonnement an.

```azurepowershell
Connect-AzAccount
```

Wählen Sie Ihr Azure-Abonnement. Verwenden Sie das Cmdlet `Get-AzSubscription` zum Abrufen der Liste der Azure-Abonnements, auf die Sie Zugriff haben. Wählen Sie mithilfe des Cmdlets `Set-AzContext` das Azure-Abonnement aus, das das zu verwendende Azure Migrate-Projekt enthält.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Abrufen des Azure Migrate-Projekts

Ein Azure Migrate-Projekt wird zum Speichern von Ermittlungs-, Bewertungs- und Migrationsmetadaten verwendet, die in der von Ihnen bewerteten oder migrierten Umgebung gesammelt werden.
In einem Projekt können Sie ermittelte Ressourcen nachverfolgen, Bewertungen orchestrieren und Migrationen durchführen.

Im Rahmen der Vorbereitung haben Sie bereits ein Azure Migrate-Projekt erstellt. Verwenden Sie das Cmdlet `Get-AzMigrateProject`, um Details zu einem Azure Migrate-Projekt abzurufen. Sie müssen den Namen des Azure Migrate-Projekts (`Name`) und den Namen der Ressourcengruppe des Azure Migrate-Projekts (`ResourceGroupName`) angeben.

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Abrufen ermittelter virtueller Computer in einem Azure Migrate-Projekt

Von Azure Migrate wird eine einfache [Azure Migrate-Appliance](migrate-appliance-architecture.md) verwendet. Die Azure Migrate-Appliance wurde im Rahmen der Vorbereitung als virtueller VMware-Computer bereitgestellt.

Geben Sie zum Abrufen eines bestimmten virtuellen VMware-Computers in einem Azure Migrate-Projekt den Namen des Azure Migrate-Projekts (`ProjectName`), die Ressourcengruppe des Azure Migrate-Projekts (`ResourceGroupName`) und den Namen des virtuellen Computers (`DisplayName`) an. 

> [!NOTE]
> **Beim Parameterwert für den VM-Namen (`DisplayName`) wird die Groß-/Kleinschreibung beachtet.**

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
Dieser virtuelle Computer wird im Rahmen dieses Tutorials migriert.

Mithilfe der Parameter `ProjectName` und `ResourceGroupName` können Sie auch alle virtuellen VMware-Computer in einem Azure Migrate-Projekt abrufen.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Wenn ein Azure Migrate-Projekt mehrere Appliances enthält, können Sie die Parameter `ProjectName`, `ResourceGroupName` und `ApplianceName` verwenden, um alle ermittelten virtuellen Computer unter Verwendung einer bestimmten Azure Migrate-Appliance abzurufen. 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Initialisieren der Replikationsinfrastruktur

[Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) nutzt mehrere Azure-Ressourcen für die VM-Migration. Folgende Ressourcen werden von der Servermigration in der gleichen Ressourcengruppe bereitgestellt, in der sich auch das Projekt befindet:

- **Service Bus**: Bei der Servermigration wird der Service Bus verwendet, um Nachrichten zur Replikationsorchestrierung an die Appliance zu senden.
- **Gatewayspeicherkonto**: Bei der Servermigration wird das Gatewayspeicherkonto verwendet, um Statusinformationen zu den replizierten VMs zu speichern.
- **Protokollspeicherkonto**: Die Azure Migrate-Appliance lädt Replikationsprotokolle für VMs in ein Protokollspeicherkonto hoch. Azure Migrate wendet die Replikationsinformationen auf die verwalteten Replikatdatenträger an.
- **Schlüsseltresor**: Von der Azure Migrate-Appliance wird der Schlüsseltresor verwendet, um Verbindungszeichenfolgen für den Service Bus zu verwalten, und Zugriffsschlüssel werden für die Speicherkonten genutzt, die bei der Replikation eingesetzt werden.

Führen Sie vor dem Replizieren des ersten virtuellen Computers im Azure Migrate-Projekt das folgende Skript aus, um die Replikationsinfrastruktur bereitzustellen. Durch dieses Skript werden die oben genannten Ressourcen bereitgestellt und konfiguriert, damit Sie mit der Migration Ihrer virtuellen VMware-Computer beginnen können.

> [!NOTE]
> Pro Azure Migrate-Projekt werden nur Migrationen in eine einzelne Azure-Region unterstützt. Nachdem Sie dieses Skript ausgeführt haben, kann die Zielregion für die Migration Ihrer virtuellen VMware-Computer nicht mehr geändert werden.
> Wenn Sie in Ihrem Azure Migrate-Projekt eine neue Appliance konfigurieren, müssen Sie das Skript `Initialize-AzMigrateReplicationInfrastructure` ausführen. 

Im Artikel wird die Replikationsinfrastruktur initialisiert, um die Migration der virtuellen Computer in die Region `Central US` zu ermöglichen. Sie können die [Datei aus dem GitHub-Repository herunterladen](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) oder mit dem folgenden Codeausschnitt ausführen: 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>Replizieren von VMs

Nach Abschluss der Ermittlung und der Initialisierung der Replikationsinfrastruktur können Sie mit der Replikation virtueller VMware-Computer in Azure beginnen. Sie können bis zu 300 Replizierungen gleichzeitig ausführen.

Die Replikationseigenschaften können wie folgt angegeben werden:

- **Zielabonnement und -ressourcengruppe:** Geben Sie das Abonnement und die Ressourcengruppe an, zu dem bzw. zu der der virtuelle Computer migriert werden soll. Geben Sie hierzu mithilfe des Parameters `TargetResourceGroupId` die Ressourcengruppen-ID an. 
- **Ziel-VNET und -subnetz:** Geben Sie mithilfe der Parameter `TargetNetworkId` und `TargetSubnetName` die ID der Azure Virtual Network-Instanz und den Namen des Subnetzes ein, zu der bzw. zu dem der virtuelle Computer migriert werden soll. 
- **Name des virtuellen Zielcomputers:** Geben Sie mithilfe des Parameters `TargetVMName` den Namen des zu erstellenden virtuellen Azure-Computers an.
- **Größe des virtuellen Zielcomputers:** Geben Sie mithilfe des Parameters `TargetVMSize` die Größe des virtuellen Azure-Computers an, die für den replizierenden virtuellen Computer verwendet werden soll. Wenn Sie also beispielsweise einen virtuellen Computer zu „D2_v2“ in Azure migrieren möchten, geben Sie für `TargetVMSize` den Wert „Standard_D2_v2“ an.  
- **Lizenz:** Wenn Sie den Azure-Hybridvorteil für Ihre Windows Server-Computer nutzen möchten, die durch aktive Software Assurance- oder Windows Server-Abonnements abgedeckt sind, geben Sie für den Parameter `LicenseType` den Wert „WindowsServer“ an. Geben Sie andernfalls für den Parameter `LicenseType` den Wert „NoLicenseType“ an.
- **Betriebssystemdatenträger:** Geben Sie den eindeutigen Bezeichner des Datenträgers an, auf dem sich der Bootloader und das Installationsprogramm des Betriebssystems befinden. Die zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets `Get-AzMigrateServer` abgerufen wurde.
- **Datenträgertyp:** Geben Sie den Wert für den Parameter `DiskType` wie folgt an:
    - Wenn Sie verwaltete Premium-Datenträger verwenden möchten, geben Sie für den Parameter `DiskType` den Wert „Premium_LRS“ an. 
    - Wenn Sie Standard-SSD-Datenträger verwenden möchten, geben Sie für den Parameter `DiskType` den Wert „StandardSSD_LRS“ an. 
    - Wenn Sie Standard-HDD-Datenträger verwenden möchten, geben Sie für den Parameter `DiskType` den Wert „Standard_LRS“ an. 
- **Infrastrukturredundanz:** Geben Sie die Option für die Infrastrukturredundanz wie folgt an: 
    - Verfügbarkeitszone, um den migrierten Computer einer bestimmten Verfügbarkeitszone in der Region anzuheften. Verteilen Sie mit dieser Option Server, die eine Anwendungsebene mit mehreren Knoten bilden, über Verfügbarkeitszonen. Diese Option ist nur verfügbar, wenn die für die Migration ausgewählte Zielregion Verfügbarkeitszonen unterstützt. Falls Sie Verfügbarkeitszonen verwenden möchten, geben Sie für den Parameter `TargetAvailabilityZone` den Wert der Verfügbarkeitszone an.
    - Verfügbarkeitsgruppe, um den migrierten Computer in einer Verfügbarkeitsgruppe zu platzieren. Um diese Option verwenden zu können, muss die ausgewählte Zielressourcengruppe über mindestens eine Verfügbarkeitsgruppe verfügen. Falls Sie eine Verfügbarkeitsgruppe verwenden möchten, geben Sie für den Parameter `TargetAvailabilitySet` die ID der Verfügbarkeitsgruppe an. 

### <a name="replicate-vms-with-all-disks"></a>Replizieren von virtuellen Computern mit allen Datenträgern
In diesem Tutorial werden alle Datenträger des ermittelten virtuellen Computers repliziert, und es wird ein neuer Name für den virtuellen Computer in Azure angegeben. Der erste Datenträger des ermittelten Servers wird als Betriebssystemdatenträger festgelegt, und alle Datenträger werden als „HDD Standard“ migriert. Der Betriebssystemdatenträger enthält den Bootloader und das Installationsprogramm des Betriebssystems. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replizieren von virtuellen Computern mit ausgewählten Datenträgern
Die Datenträger des ermittelten virtuellen Computers können auch selektiv repliziert werden. Verwenden Sie dazu das Cmdlet `New-AzMigrateDiskMapping`, und geben Sie es als Eingabe für den Parameter `DiskToInclude` im Cmdlet `New-AzMigrateServerReplication` an. Sie können auch das Cmdlet `New-AzMigrateDiskMapping` verwenden, um für die einzelnen zu replizierenden Datenträger jeweils andere Zieldatenträgertypen anzugeben. 

Geben Sie Werte für die folgenden Parameter des Cmdlets `New-AzMigrateDiskMapping` an:

- **DiskId**: Geben Sie den eindeutigen Bezeichner für den zu migrierenden Datenträger an. Die zu verwendende Datenträger-ID ist die UUID-Eigenschaft (Unique Identifier, eindeutiger Bezeichner) für den Datenträger, die mithilfe des Cmdlets `Get-AzMigrateServer` abgerufen wurde.  
- **IsOSDisk**: Geben Sie „true“ an, wenn es sich bei dem zu migrierenden Datenträger um den Betriebssystemdatenträger des virtuellen Computers handelt. Geben Sie andernfalls „false“ an.
- **DiskType**: Geben Sie die Art des Datenträgers an, die in Azure verwendet werden soll. 

Im folgenden Beispiel werden nur zwei Datenträger des ermittelten virtuellen Computers repliziert. Der Betriebssystemdatenträger wird angegeben, und es werden unterschiedliche Datenträgertypen für die zu replizierenden Datenträger verwendet. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Überwachen der Replikation 

Die Replikation wird wie folgt durchgeführt:

- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Während der ersten Replikation wird eine VM-Momentaufnahme erstellt. Datenträgerdaten der Momentaufnahme werden auf verwalteten Replikatdatenträgern in Azure repliziert.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.

Verfolgen Sie den Status der Replikation mithilfe des Cmdlets `Get-AzMigrateServerReplication`. 

> [!NOTE]
> Bei der ID des ermittelten virtuellen Computers und der ID des replizierenden virtuellen Computers handelt es sich um zwei verschiedene eindeutige Bezeichner. Beide Bezeichner können zum Abrufen von Details eines replizierenden Servers verwendet werden.  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Überwachen der Replikation unter Verwendung des Bezeichners des ermittelten virtuellen Computers
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Überwachen der Replikation unter Verwendung des Bezeichners des replizierenden virtuellen Computers

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

Sie können die Eigenschaften „MigrationState“ (Migrationszustand) und „MigrationStateDescription“ (Beschreibung des Migrationszustands) in der Ausgabe nachverfolgen. 
- Bei der ersten Replikation haben die Eigenschaften „MigrationState“ (Migrationszustand) und „MigrationStateDescription“ (Beschreibung des Migrationszustands) den Wert „InitialSeedingInProgress“ (Anfängliches Seeding wird ausgeführt) bzw. „Initial replication“ (Erste Replikation). 
- Während der Deltareplikation haben die Eigenschaften „MigrationState“ (Migrationszustand) und „MigrationStateDescription“ (Beschreibung des Migrationszustands) den Wert „Replicating“ (Replikation wird ausgeführt) bzw. „Ready to migrate“ (Bereit zum Migrieren).
- Nach Abschluss der Migration haben die Eigenschaften „MigrationState“ (Migrationszustand) und „MigrationStateDescription“ (Beschreibung des Migrationszustands) den Wert „Migration succeeded“ (Replikation erfolgreich) bzw. „Migrated“ (Migriert).

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Details zum Replikationsfortschritt erhalten Sie durch Ausführen des folgenden Cmdlets:

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
Sie können den Fortschritt der ersten Replikation anhand der Eigenschaft „InitialSeedingProgressPercentage“ (Fortschritt des anfängliche Seedings in Prozent) nachverfolgen.

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

Die Replikation wird wie folgt durchgeführt:

- Nachdem der Auftrag „Replikation starten“ erfolgreich abgeschlossen wurde, beginnen die Computer mit der ersten Replikation in Azure.
- Während der ersten Replikation wird eine VM-Momentaufnahme erstellt. Datenträgerdaten der Momentaufnahme werden auf verwalteten Replikatdatenträgern in Azure repliziert.
- Nach Abschluss der ersten Replikation beginnt die Deltareplikation. Inkrementelle Änderungen an lokalen Datenträgern werden regelmäßig auf den Replikatdatenträgern in Azure repliziert.

## <a name="retrieve-the-status-of-a-job"></a>Abrufen des Status eines Auftrags

Sie können den Status eines Auftrags mithilfe des Cmdlets `Get-AzMigrateJob` überwachen. 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>Aktualisieren von Eigenschaften eines replizierenden virtuellen Computers

[Azure Migrate: Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) ermöglicht das Ändern von Zieleigenschaften wie Name, Größe, Ressourcengruppe und NIC-Konfiguration für einen replizierenden virtuellen Computer. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
Folgende Eigenschaften können für einen virtuellen Computer aktualisiert werden:

- **Name des virtuellen Computers:** Geben Sie mithilfe des Parameters `TargetVMName` den Namen des zu erstellenden virtuellen Azure-Computers an.
- **Größe des virtuellen Computers:** Geben Sie mithilfe des Parameters `TargetVMSize` die Größe des virtuellen Azure-Computers an, die für den replizierenden virtuellen Computer verwendet werden soll. Wenn Sie also beispielsweise einen virtuellen Computer zu „D2_v2“ in Azure migrieren möchten, geben Sie für `TargetVMSize` den Wert „Standard_D2_v2“ an.  
- **Virtuelles Netzwerk:** Geben Sie mithilfe des Parameters `TargetNetworkId` die ID der Azure Virtual Network-Instanz ein, zu der der virtuelle Computer migriert werden soll. 
- **Ressourcengruppe:** Geben Sie mithilfe des Parameters `TargetResourceGroupId` die ID der Ressourcengruppe an, zu der der virtuelle Computer migriert werden soll.
- **Netzwerkschnittstelle:** Die NIC-Konfiguration kann mithilfe des Cmdlets `New-AzMigrateNicMapping` angegeben werden. Das Objekt wird dann als Eingabe an den Parameter `NicToUpdate` im Cmdlet `Set-AzMigrateServerReplication` übergeben. 

    - **Ändern der IP-Zuordnung:** Wenn Sie eine statische IP-Adresse für eine NIC angeben möchten, geben Sie mithilfe des Parameters `TargetNicIP` die IPv4-Adresse an, die als statische IP für den virtuellen Computer verwendet werden soll. Wenn für eine NIC dynamisch eine IP-Adresse zugewiesen werden soll, geben Sie für den Parameter `TargetNicIP` den Wert „Auto“ an.
    - Verwenden Sie für den Parameter `TargetNicSelectionType` den Wert „Primary“ (Primär), „Secondary“ (Sekundär) oder „DoNotCreate“ (Nicht erstellen), um anzugeben, ob die NIC als primäre oder sekundäre NIC fungieren oder nicht auf dem migrierten virtuellen Computer erstellt werden soll. Es kann nur eine einzelne NIC als primäre NIC für den virtuellen Computer angegeben werden. 
    - Wenn eine NIC als primäre NIC fungieren soll, müssen Sie auch die anderen NICs angeben, die als sekundäre NICs fungieren oder nicht auf dem migrierten virtuellen Computer erstellt werden sollen.  
    - Wenn Sie das Subnetz für die NIC ändern möchten, geben Sie mithilfe des Parameters `TargetNicSubnet` den Namen des Subnetzes an.

 - **Verfügbarkeitszone:** Falls Sie Verfügbarkeitszonen verwenden möchten, geben Sie für den Parameter `TargetAvailabilityZone` den Wert der Verfügbarkeitszone an.
 - **Verfügbarkeitsgruppe:** Falls Sie eine Verfügbarkeitsgruppe verwenden möchten, geben Sie für den Parameter `TargetAvailabilitySet` die ID der Verfügbarkeitsgruppe an.

Im folgenden Beispiel wird die NIC-Konfiguration wie folgt geändert: Die erste NIC wird als primäre NIC konfiguriert, und ihr wird eine statische IP-Adresse zugewiesen. Die zweite NIC wird bei der Migration verworfen, und Name und Größe des virtuellen Zielcomputers werden aktualisiert. 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

Sie können auch alle replizierenden Server in einem Azure Migrate-Projekt auflisten und dann den Bezeichner des replizierenden virtuellen Computers verwenden, um VM-Eigenschaften zu aktualisieren.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>Ausführen einer Testmigration

Wenn die Deltareplikation beginnt, können Sie zunächst eine Testmigration für die virtuellen Computer ausführen, bevor eine vollständige Migration zu Azure erfolgt. Wir empfehlen dringend, diese Testmigration mindestens einmal für jeden Computer auszuführen, bevor Sie ihn migrieren. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

- Bei einer Testmigration wird überprüft, ob die Migration wie erwartet funktioniert. Der lokale Computer wird dadurch nicht beeinträchtigt, bleibt betriebsbereit und wird weiter repliziert. 
- Mit der Testmigration wird die Migration simuliert, indem eine Azure-VM mit replizierten Daten erstellt wird (normalerweise per Migration zu einem nicht für die Produktion bestimmten VNET unter Ihrem Azure-Abonnement).
- Sie können den replizierten virtuellen Azure-Testcomputer verwenden, um die Migration zu überprüfen, App-Tests durchzuführen und Probleme zu beheben, bevor die vollständige Migration erfolgt.

Wählen Sie die Azure Virtual Network-Instanz aus, die zum Testen verwendet werden soll, indem Sie mithilfe des Parameters `TestNetworkID` die ID des virtuellen Netzwerks angeben.

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

Bereinigen Sie die Testmigration nach Abschluss des Tests mithilfe des Cmdlets `Start-AzMigrateTestMigrationCleanup`. Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>Migrieren virtueller Computer

Nachdem Sie sich vergewissert haben, dass die Testmigration wie erwartet funktioniert, können Sie den replizierenden Server mithilfe des folgenden Cmdlets migrieren: Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

Soll der Quellserver nicht ausgeschaltet werden, lassen Sie den Parameter `TurnOffSourceServer` weg.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>Fertigstellen der Migration

1. Beenden Sie nach Abschluss der Migration die Replikation für den lokalen Computer, und bereinigen Sie die Informationen zum Replikationszustand für den virtuellen Computer mithilfe des folgenden Cmdlets: Mit dem Cmdlet wird ein Auftrag zurückgegeben, der zur Überwachung des Vorgangsstatus nachverfolgt werden kann. 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. Installieren Sie den [Windows](../virtual-machines/extensions/agent-windows.md)- oder [Linux](../virtual-machines/extensions/agent-linux.md)-Agent für die Azure-VM auf den migrierten Computern.
3. Führen Sie App-Anpassungen nach der Migration durch, z.B. die Aktualisierung von Datenbankverbindungszeichenfolgen und Webserverkonfigurationen.
4. Führen Sie endgültige Anwendungs- und Migrationsakzeptanztests für die migrierte Anwendung durch, die nun in Azure ausgeführt wird.
5. Leiten Sie den Datenverkehr auf die migrierte Instanz der Azure-VM um.
6. Entfernen Sie die lokalen VMs aus Ihrem lokalen VM-Bestand.
7. Entfernen Sie die lokalen VMs aus lokalen Sicherungen.
8. Aktualisieren Sie die interne Dokumentation zum Anzeigen des neuen Speicherorts und der IP-Adresse der Azure-VMs. 

## <a name="post-migration-best-practices"></a>Bewährte Methoden nach der Migration

- Beachten Sie zur Steigerung der Resilienz Folgendes:
    - Schützen Sie Daten, indem Sie Azure-VMs mit dem Azure Backup-Dienst sichern. [Weitere Informationen](../backup/quick-backup-vm-portal.md)
    - Sorgen Sie für die kontinuierliche Ausführung und Verfügbarkeit von Workloads, indem Sie Azure-VMs mithilfe von Site Recovery in eine sekundäre Region replizieren. [Weitere Informationen](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Beachten Sie zur Steigerung der Sicherheit Folgendes:
    - Sperren und beschränken Sie den Zugriff von eingehendem Datenverkehr mit der [Just-in-Time-Verwaltung im Azure Security Center](../security-center/security-center-just-in-time.md).
    - Beschränken Sie den Netzwerkdatenverkehr mithilfe von [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md) auf Verwaltungsendpunkte.
    - Stellen Sie [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) bereit, um Datenträger und Daten vor Diebstahl und unbefugtem Zugriff zu schützen.
    - Erfahren Sie mehr über das [Sichern von IaaS-Ressourcen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), und besuchen Sie die Website [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Beachten Sie zur Überwachung und Verwaltung Folgendes:
-  Ziehen Sie die Bereitstellung von [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) in Erwägung, um den Ressourceneinsatz und die Ausgaben zu überwachen.