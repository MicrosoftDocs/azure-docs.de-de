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
ms.openlocfilehash: d2a85f3947e9993e5d1853e45c6d03586a074cf6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178054"
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

Um die Beschreibung eines Katalogs zu aktualisieren, verwenden Sie [Update-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimagedefinition) das Ablaufdatum für Ihre Imagedefinition aktualisieren können.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

In diesem Beispiel wird gezeigt, wie Sie mit [Update-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/update-azgalleryimageversion) dafür sorgen können, dass diese Image-Version nicht als das *neueste* Image verwendet wird.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
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

