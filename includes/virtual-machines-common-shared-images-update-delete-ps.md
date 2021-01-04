---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 32978257c3e209dc78a29c6e8ae0d1c4ae016a5b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553890"
---
## <a name="update-resources"></a>Aktualisieren von Ressourcen

Es gibt Einschränkungen, welche Elemente aktualisiert werden können. Die folgenden Elemente können aktualisiert werden: 

Katalog mit geteilten Images:
- BESCHREIBUNG

Imagedefinition:
- Empfohlene vCPUs
- Empfohlener Arbeitsspeicher
- BESCHREIBUNG
- Datum für Ende des Lebenszyklus

Imageversion:
- Anzahl regionaler Replikate
- Zielregionen
- Ausschluss aus neuester Version
- Datum für Ende des Lebenszyklus

Wenn Sie Replikatregionen hinzufügen möchten, dürfen Sie das verwaltete Quellimage nicht löschen. Das verwaltete Quellimage ist erforderlich, um die Imageversion in weiteren Regionen zu replizieren. 

Um die Beschreibung eines Katalogs zu aktualisieren, verwenden Sie [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) das Ablaufdatum für Ihre Imagedefinition aktualisieren können.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) dafür sorgen können, dass diese Image-Version nicht als das *neueste* Image verwendet wird.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

Dieses Beispiel zeigt, wie [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) verwendet wird, um diese Imageversion in die Berücksichtigung als *neuestes* Image einzubeziehen.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Ressourcen löschen möchten, müssen Sie mit dem letzten Element in den geschachtelten Ressourcen beginnen: die Imageversion. Sobald alle Versionen gelöscht wurden, können Sie die Imagedefinition löschen. Sie können den Katalog erst löschen, nachdem alle in ihm befindlichen Ressourcen gelöscht wurden.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```