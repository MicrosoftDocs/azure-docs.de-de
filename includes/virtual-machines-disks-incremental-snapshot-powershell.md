---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 895a81df858e51a266cd87c96a161695a4bf2cc1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992880"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>Unterstützte Regionen
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Sie können Azure PowerShell verwenden, um eine inkrementelle Momentaufnahme zu erstellen. Sie benötigen die aktuelle Version von Azure PowerShell. Mit dem folgenden Befehl wird sie installiert oder Ihre vorhandene Installation auf die neueste Version aktualisiert:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Melden Sie sich nach der Installation bei der PowerShell-Sitzung mit `Connect-AzAccount` an.

Um eine inkrementelle Momentaufnahme mit Azure PowerShell zu erstellen, legen Sie die Konfiguration mit [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) mit dem Parameter `-Incremental` fest und übergeben diese dann als Variable über den Parameter `-Snapshot` an [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0).

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Sie können inkrementelle Momentaufnahmen auf dem gleichen Datenträger mit den Eigenschaften `SourceResourceId` und `SourceUniqueId` der Momentaufnahmen identifizieren. `SourceResourceId` ist die Azure Resource Manager-Ressourcen-ID des übergeordneten Datenträgers. `SourceUniqueId` ist der Wert, der von der `UniqueId`-Eigenschaft des Datenträgers geerbt wird. Wenn Sie einen Datenträger löschen und dann einen Datenträger mit demselben Namen erstellen, ändert sich der Wert der `UniqueId`-Eigenschaft.

Sie können `SourceResourceId` und `SourceUniqueId` verwenden, um eine Liste aller Momentaufnahmen zu erstellen, die einem bestimmten Datenträger zugeordnet sind. Ersetzen Sie `<yourResourceGroupNameHere>` durch Ihren Wert. Anschließend können Sie das folgende Beispiel verwenden, um Ihre vorhandenen inkrementellen Momentaufnahmen aufzulisten:

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager-Vorlage

Sie können auch Azure Resource Manager-Vorlagen verwenden, um eine inkrementelle Momentaufnahme zu erstellen. Sie müssen sicherstellen, dass die apiVersion auf **2019-03-01** festgelegt ist. Die inkrementelle Eigenschaft muss ebenfalls auf TRUE festgelegt sein. Der folgende Codeausschnitt ist ein Beispiel dafür, wie Sie eine inkrementelle Momentaufnahme mit Resource Manager-Vorlagen erstellen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Beispielcode mit .NET, der die differenzielle Funktionalität von inkrementellen Momentaufnahmen veranschaulicht, finden Sie unter [Kopieren von Azure Managed Disks-Sicherungen in eine andere Region mit der differenziellen Funktionalität von inkrementellen Momentaufnahmen](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).