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
ms.openlocfilehash: 4bdeef537556db94338ed50fcfa6e9d88431f25a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96016255"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Beschränkungen

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Sie können eine inkrementelle Momentaufnahme mit der Azure CLI erstellen. Sie benötigen dazu die aktuelle Version der Azure CLI. 

Mit dem folgenden Befehl wird sie unter Windows installiert oder Ihre vorhandene Installation auf die neueste Version aktualisiert:
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Unter Linux variiert die CLI-Installation je nach Betriebssystemversion.  Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) für Ihre jeweilige Linux-Version.

Um eine inkrementelle Momentaufnahme zu erstellen, verwenden Sie [az snapshot create](/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) mit dem Parameter `--incremental`.

Das folgende Beispiel erstellt eine inkrementelle Momentaufnahme, ersetzt `<yourDesiredSnapShotNameHere>`, `<yourResourceGroupNameHere>`, `<exampleDiskName>` und `<exampleLocation>` durch Ihre eigenen Werte und führt das Beispiel dann aus:

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

Sie können inkrementelle Momentaufnahmen auf dem gleichen Datenträger mit den Eigenschaften `SourceResourceId` und `SourceUniqueId` der Momentaufnahmen identifizieren. `SourceResourceId` ist die Azure Resource Manager-Ressourcen-ID des übergeordneten Datenträgers. `SourceUniqueId` ist der Wert, der von der `UniqueId`-Eigenschaft des Datenträgers geerbt wird. Wenn Sie einen Datenträger löschen und dann einen Datenträger mit demselben Namen erstellen, ändert sich der Wert der `UniqueId`-Eigenschaft.

Sie können `SourceResourceId` und `SourceUniqueId` verwenden, um eine Liste aller Momentaufnahmen zu erstellen, die einem bestimmten Datenträger zugeordnet sind. Im folgenden Beispiel werden alle inkrementellen Momentaufnahmen aufgelistet, die einem bestimmten Datenträger zugeordnet sind. Dafür ist jedoch ein Setup erforderlich.

In diesem Beispiel wird jq zum Abfragen der Daten verwendet. Um das Beispiel auszuführen, müssen Sie [jq installieren](https://stedolan.github.io/jq/download/).

Ersetzen Sie `<yourResourceGroupNameHere>` und `<exampleDiskName>` durch Ihren Wert. Anschließend können Sie das folgende Beispiel verwenden, um Ihre vorhandenen inkrementellen Momentaufnahmen aufzulisten, wenn jq installiert ist:

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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