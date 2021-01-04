---
title: Migrieren von virtuellen VMware-Computern zu Azure mit serverseitiger Verschlüsselung (Server-Side Encryption, SSE) und von Kunden verwalteten Schlüsseln (Customer-Managed Keys, CMK) mit Azure Migrate-Servermigration
description: Erfahren Sie, wie Sie virtuelle VMware-Computer mit serverseitiger Verschlüsselung (Server-Side Encryption, SSE) und von Kunden verwalteten Schlüsseln (Customer-Managed Keys, CMK) mit Azure Migrate-Servermigration zu Azure migrieren.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751086"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Migrieren von VMware-VMs zu Azure-VMS mit serverseitiger Verschlüsselung und von Kunden verwalteten Schlüsseln

In diesem Artikel wird beschrieben, wie Sie mit Azure Migrate-Servermigration (Replikation ohne Agents) VMware-VMs zu Azure-VMs mit Datenträgern migrieren, die mithilfe der serverseitigen Verschlüsselung (SSE) mit vom Kunden verwalteten Schlüsseln (CMK) verschlüsselt sind.

Die Benutzeroberfläche des Azure Migrate-Servermigrationsportals ermöglicht Ihnen das [Migrieren von VMware-VMs zu Azure (ohne Agent)](tutorial-migrate-vmware.md). Die Benutzeroberfläche des Portals bietet derzeit nicht die Möglichkeit, SSE mit CMK für ihre replizierten Datenträger in Azure zu aktivieren. Die Möglichkeit, SSE mit CMK für die replizierten Datenträger zu aktivieren, ist zurzeit nur über die REST-API verfügbar. In diesem Artikel erfahren Sie, wie Sie eine [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) erstellen und bereitstellen, um eine VMware-VM zu replizieren und die replizierten Datenträger in Azure für die Verwendung von SSE mit CMK zu konfigurieren.

In den Beispielen in diesem Artikel wird [Azure PowerShell](/powershell/azure/new-azureps-module-az) verwendet, um die Aufgaben auszuführen, die zum Erstellen und Bereitstellen der Resource Manager-Vorlage erforderlich sind.

[Erfahren Sie mehr](../virtual-machines/disk-encryption.md) über die serverseitige Verschlüsselung (SSE) mit vom Kunden verwalteten Schlüsseln (CMK) für verwaltete Datenträger.

## <a name="prerequisites"></a>Voraussetzungen

- [Lesen Sie das Tutorial](tutorial-migrate-vmware.md) zur Migration von VMware-VMS zu Azure mit Replikation ohne Agent, um die Anforderungen des Tools zu verstehen.
- [Befolgen Sie diese Anweisungen](./create-manage-projects.md), um ein Azure Migrate-Projekt zu erstellen und das Tool **Azure Migrate: Servermigration** dem Projekt hinzuzufügen.
- [Befolgen Sie diese Anweisungen](how-to-set-up-appliance-vmware.md), um die Azure Migrate-Appliance für VMware in Ihrer lokalen Umgebung einzurichten und die Ermittlung abzuschließen.

## <a name="prepare-for-replication"></a>Vorbereiten der Replikation

Sobald die VM-Ermittlung abgeschlossen ist, wird in der Zeile „Ermittelte Server“ auf der Kachel „Servermigration“ die Anzahl der von der Appliance ermittelten VMware-VMS angezeigt.

Bevor Sie mit dem Replizieren von VMs beginnen können, muss die Replikationsinfrastruktur vorbereitet werden.

1. Erstellen Sie eine Service Bus-Instanz in der Zielregion. Der Service Bus wird von der lokalen Azure Migrate-Appliance für die Kommunikation mit dem Servermigrationsdienst verwendet, um die Replikation und Migration zu koordinieren.
2. Erstellen Sie ein Speicherkonto für die Übertragung von Vorgangsprotokollen aus der Replikation.
3. Erstellen Sie ein Speicherkonto, in das die Azure Migrate-Appliance Replikationsdaten hochlädt.
4. Erstellen Sie eine Key Vault-Instanz, und konfigurieren Sie die Key Vault-Instanz, um Shared Access Signature-Token für den Blobzugriff auf die in Schritt 3 und 4 erstellten Speicherkonten zu verwalten.
5. Generieren Sie ein Shared Access Signature-Token für den Service Bus, den Sie in Schritt 1 erstellt haben, und erstellen Sie in der im vorherigen Schritt erstellten Key Vault-Instanz ein Geheimnis für das Token.
6. Erstellen Sie eine Key Vault-Zugriffsrichtlinie, um der lokalen Azure Migrate-Appliance (mit der Appliance-AAD-App) und dem Servermigrationsdienst Zugriff auf die Key Vault-Instanz zu gewähren.
7. Erstellen Sie eine Replikationsrichtlinie, und konfigurieren Sie den Servermigrationsdienst mit Details zur Replikationsinfrastruktur, die im vorherigen Schritt erstellt wurde.

Die Replikationsinfrastruktur muss in der Azure-Zielregion für die Migration und im Azure-Zielabonnement erstellt werden, zu dem die VMs migriert werden.

Die Benutzeroberfläche des Servermigrationsportals vereinfacht die Vorbereitung der Replikationsinfrastruktur, indem dies automatisch für Sie ausgeführt wird, wenn Sie einen virtuellen Computer zum ersten Mal in einem Projekt replizieren. In diesem Artikel gehen wir davon aus, dass Sie bereits eine oder mehrere VMs mithilfe der Benutzeroberfläche des Portals repliziert haben, und dass die Replikationsinfrastruktur bereits erstellt wurde. Wir sehen uns an, wie Details der vorhandenen Replikationsinfrastruktur ermittelt werden, und wie diese Details als Eingaben für die Resource Manager-Vorlage dienen, die zum Einrichten der Replikation mit CMK verwendet wird.

### <a name="identifying-replication-infrastructure-components"></a>Identifizieren der Komponenten der Replikationsinfrastruktur

1. Wechseln Sie im Azure-Portal zur Ressourcengruppenseite, und wählen Sie die Ressourcengruppe aus, in der das Azure Migrate-Projekt erstellt wurde.
2. Wählen Sie im Menü auf der linken Seite **Bereitstellungen** aus, und suchen Sie nach einem Bereitstellungsnamen, der mit der Zeichenfolge *„Microsoft.MigrateV2.VMwareV2EnableMigrate“* beginnt. Sie sehen eine Liste mit Resource Manager-Vorlagen, die in der Benutzeroberfläche des Portals erstellt wurden, um die Replikation für VMs in diesem Projekt einzurichten. Wir laden eine solche Vorlage herunter und verwenden sie als Basis zum Vorbereiten der Vorlage für die Replikation mit CMK.
3. Um die Vorlage herunterzuladen, wählen Sie eine beliebige Bereitstellung aus, die dem Zeichenfolgenmuster im vorherigen Schritt entspricht. Wählen Sie im linken Menü **Vorlage** aus, und klicken Sie im oberen Menü auf **Herunterladen**. Speichern Sie die Datei „template.json“ lokal. Sie bearbeiten diese Vorlagendatei im letzten Schritt.

## <a name="create-a-disk-encryption-set"></a>Erstellen eines Datenträgerverschlüsselungssatzes

Ein Datenträgerverschlüsselungssatz-Objekt ordnet verwaltete Datenträger einer Key Vault-Instanz zu, die den für SSE zu verwendenden CMK enthält. Wenn Sie VMs mit CMK replizieren möchten, erstellen Sie einen Datenträgerverschlüsselungssatz, und übergeben Sie ihn als Eingabe an den Replikationsvorgang.

Folgen Sie [diesem](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) Beispiel, um mit Azure PowerShell eine Datenträgerverschlüsselung zu erstellen. Stellen Sie sicher, dass der Datenträgerverschlüsselungssatz in dem Zielabonnement erstellt wird, zu dem die VMs migriert werden, und in der Azure-Zielregion für die Migration.

Der Datenträgerverschlüsselungssatz kann für die Verschlüsselung verwalteter Datenträger mit einem kundenseitig verwalteten Schlüssel oder für die doppelte Verschlüsselung sowohl mit einem kundenseitig verwalteten Schlüssel als auch mit einem Plattformschlüssel konfiguriert werden. Wenn Sie die Option zur doppelten Verschlüsselung ruhender Daten verwenden möchten, konfigurieren Sie die Datenträgerverschlüsselung gemäß [dieser](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md) Beschreibung.

Im unten gezeigten Beispiel wurde der Datenträgerverschlüsselungssatz für die Verwendung eines kundenseitig verwalteten Schlüssels konfiguriert.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Abrufen von Details der zu migrierenden VMware-VM

In diesem Schritt verwenden Sie Azure PowerShell, um die Details des virtuellen Computers abzurufen, der migriert werden muss. Diese Details werden verwendet, um die Resource Manager-Vorlage für die Replikation zu erstellen. Insbesondere sind die folgenden beiden Eigenschaften interessant:

- Die Computerressourcen-ID für die ermittelten virtuellen Computer.
- Die Liste der Datenträger für den virtuellen Computer und die zugehörigen Datenträger-IDs.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Kopieren Sie den Wert der SiteId-Zeichenfolge, die der Azure Migrate-Appliance entspricht, über die der virtuelle Computer erkannt wird. Im oben gezeigten Beispiel ist die SiteId *„/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite“* .

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Kopieren Sie Resource-ID, Name und UUID-Werte des Datenträgers für den zu migrierenden Computer.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Erstellen einer Resource Manager-Vorlage für die Replikation

- Öffnen Sie die Resource Manager-Vorlagendatei, die Sie in dem Schritt **Identifizieren der Komponenten der Replikationsinfrastruktur** in einen Editor Ihrer Wahl heruntergeladen haben.
- Entfernen Sie alle Ressourcendefinitionen mit Ausnahme der Ressourcen vom Typ *„Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems“* aus der Vorlage.
- Wenn mehrere Ressourcendefinitionen des obigen Typs vorhanden sind, entfernen Sie alle bis auf eine. Entfernen Sie alle **dependsOn**-Eigenschaftsdefinitionen aus der Ressourcendefinition.
- Am Ende dieses Schritts sollten Sie über eine Datei verfügen, die wie im folgenden Beispiel aussieht und denselben Satz von Eigenschaften hat.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Bearbeiten Sie die Eigenschaft **name** in der Ressourcendefinition. Ersetzen Sie die Zeichenfolge, die dem letzten „/“ in der Eigenschaft „name“ folgt, durch den Wert von *$machine.Name* (aus dem vorherigen Schritt).
- Ändern Sie den Wert der **properties.providerSpecificDetails.vmwareMachineId**-Eigenschaft mit dem Wert von *$machine.ResourceId*(aus dem vorherigen Schritt).
- Legen Sie als Werte für **targetResourceGroupId**, **targetNetworkId** und **targetSubnetName** die Zielressourcengruppen-ID, die Ressourcen-ID des virtuellen Zielnetzwerks und den Zielsubnetznamen fest.
- Legen Sie für **licenseType** den Wert „Windows Server“ fest, um den Azure-Hybridvorteil für diesen virtuellen Computer anzuwenden. Wenn diese VM nicht für den Azure-Hybridvorteil berechtigt ist, legen Sie den Wert **licenseType** auf NoLicenseType fest.
- Ändern Sie den Wert der Eigenschaft **targetVmName** in den gewünschten Azure-VM-Namen für den migrierten virtuellen Computer.
- Fügen Sie optional eine Eigenschaft mit dem Namen **targetVmSize** unterhalb der Eigenschaft **targetVmName** hinzu. Ändern Sie den Wert der Eigenschaft **targetVmSize** in die gewünschte Azure-VM-Größe für den migrierten virtuellen Computer.
- Die **disksToInclude**-Eigenschaft ist eine Liste von Datenträgereingaben für die Replikation mit jedem Listenelement, das einen lokalen Datenträger darstellt. Erstellen Sie so viele Listenelemente, wie Datenträger auf dem lokalen virtuellen Computer vorhanden sind. Ersetzen Sie die **diskId**-Eigenschaft im Listenelement durch die UUID der Datenträger, die im vorherigen Schritt identifiziert wurden. Legen Sie für den Betriebssystemdatenträger der VM den Wert **isOSDisk** auf „true“ und für alle anderen Datenträger auf „false“ fest. Lassen Sie die Eigenschaften **logStorageAccountId** und **logStorageAccountSasSecretName** unverändert. Legen Sie den **diskType**-Wert auf den verwalteten Azure-Datenträgertyp (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) fest, der für den Datenträger verwendet werden soll. Fügen Sie für die Datenträger, die mit CMK verschlüsselt werden müssen, eine Eigenschaft mit dem Namen **diskEncryptionSetId** hinzu, und legen Sie den Wert auf die Ressourcen-ID des im Schritt *Erstellen eines Datenträgerverschlüsselungssatzes* erstellten Datenträgerverschlüsselungssatzes ( **$des.Id**) fest.
- Speichern Sie die bearbeitete Vorlagendatei. Im obigen Beispiel sieht die bearbeitete Vorlagendatei wie folgt aus:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Einrichten der Replikation

Sie können jetzt die bearbeitete Resource Manager-Vorlage in der Projektressourcengruppe bereitstellen, um die Replikation für den virtuellen Computer einzurichten. Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Nächste Schritte

[Überwachen des Status der Replikation](tutorial-migrate-vmware.md#track-and-monitor) über die Portalbenutzeroberfläche und Durchführen von Testmigrationen und Migration.