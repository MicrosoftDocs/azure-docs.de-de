---
title: Erstellen von VM-Images (Vorschauversion)
description: Informationen zur Verwendung von Azure PowerShell zum Erstellen eines Images einer Azure-VM in Shared Image Gallery
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3a7ca8236307bbf8a419d2988e1a6dc1e4c40597
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964863"
---
# <a name="preview-create-an-image-from-a-vm"></a>Vorschau: Erstellen von VM-Images

Wenn Sie über eine VM verfügen, die Sie verwenden möchten, um mehrere identische VMs zu erstellen, können Sie mithilfe von Azure PowerShell ein Image dieser VM in Shared Image Gallery erstellen. Ebenso können Sie auch die [Azure CLI](image-version-vm-cli.md) verwenden, um ein VM-Image zu erstellen.

Mithilfe von Azure PowerShell können Sie sowohl für [spezialisierte als auch für generalisierte](./windows/shared-image-galleries.md#generalized-and-specialized-images) VMs ein Image erstellen. 

Images in Imagekatalogen verfügen über zwei Komponenten, die in diesem Beispiel erstellt werden:
- Eine **Imagedefinition** enthält Informationen zum Image und zu den Anforderungen für dessen Verwendung. Dies umfasst Angaben dazu, ob es sich um ein Windows- oder Linux-Image handelt, ob es spezialisiert oder generalisiert ist sowie welche Mindest- und Höchstanforderungen für den Arbeitsspeicher gelten. Außerdem sind Versionshinweise enthalten. Es ist eine Definition eines Imagetyps. 
- Bei der Verwendung eines Katalogs mit freigegebenen Images werden **Imageversionen** verwendet, um VMs zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Bei der Erstellung einer VM werden anhand der Imageversionen neue Datenträger für die VM erstellt. Imageversionen können mehrmals verwendet werden.


## <a name="before-you-begin"></a>Voraussetzungen

Sie benötigen eine Shared Image Gallery-Instanz sowie eine VM in Azure, die als Quelle verwendet werden kann. 

Wenn ein Datenträger an die VM angefügt ist, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie, wenn nötig, im Laufe der Bearbeitung dieses Artikels die Ressourcennamen.


## <a name="get-the-gallery"></a>Abrufen des Katalogs

Sie können alle Kataloge und Imagedefinitionen nach Namen auflisten. Die Ergebnisse liegen im Format `gallery\image definition\image version` vor.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Wenn Sie die richtigen Katalog- und Imagedefinitionen gefunden haben, erstellen Sie Variablen für diese, die Sie später verwenden können. In diesem Beispiel heißt der Katalog in der Ressourcengruppe *myResourceGroup* *myGallery*.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Abrufen der VM

Mit [Get-AzVM](/powershell/module/az.compute/get-azvm) können Sie eine Liste der VMs abrufen, die in einer Ressourcengruppe verfügbar sind. Wenn Sie den Namen und die Ressourcengruppe der VM kennen, können Sie `Get-AzVM` verwenden, um das VM-Objekt abzurufen und für die spätere Verwendung in einer Variable zu speichern. Dieses Beispiel ruft die VM *sourceVM* aus der Ressourcengruppe „myResourceGroup“ ab und weist es der Variablen *$sourceVm* zu. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Es empfiehlt sich, den virtuellen Computer vor dem Erstellen eines Images mit [Stop-AzVM](/powershell/module/az.compute/stop-azvm) zu beenden oder die Zuordnung aufzuheben.

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition 

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden zum Verwalten von Informationen zum Image verwendet. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Stellen Sie beim Erstellen der Imagedefinition sicher, dass diese alle zutreffenden Informationen beinhaltet. Wenn Sie die VM generalisiert haben (über Sysprep für Windows oder waagent-deprovision für Linux) sollten Sie unter Verwendung von `-OsState generalized` eine Imagedefinition erstellen. Wenn Sie die VM nicht generalisiert haben, erstellen Sie unter Verwendung von `-OsState specialized` eine Imagedefinition.

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./windows/shared-image-galleries.md#image-definitions).

Erstellen Sie die Imagedefinition mit [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für eine spezialisierte VM gedacht, die unter Windows ausgeführt wird. Verwenden Sie `-OsType Linux`, um eine Definition für Images unter Linux zu erstellen. 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Erstellen einer Imageversion

Erstellen Sie mithilfe von [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) eine Imageversion. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel lautet die Imageversion *1.0.0*. Sie wird in den Rechenzentren *USA, Westen-Mitte* und *USA, Süden-Mitte* repliziert. Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quell* region als Ziel für die Replikation angeben müssen.

Verwenden Sie `$vm.Id.ToString()` für die `-SourceImageId`, um eine Imageversion der VM zu erstellen.

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Es dauert eine Weile, das Image für alle Zielregionen zu replizieren. Daher haben wir zum Verfolgen des Fortschritts einen Auftrag erstellt. Um den Fortschritt des Auftrags anzuzeigen, geben Sie `$job.State` ein.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `-StorageAccountType Premium_LRS` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `-StorageAccountType Standard_ZRS`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.
>

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie überprüft haben, ob die neue Imageversion ordnungsgemäß funktioniert, können Sie eine VM erstellen. Erstellen Sie eine VM aus einer [spezialisierten](vm-specialized-image-version-powershell.md) oder einer [generalisierten](vm-generalized-image-version-powershell.md) Imageversion.

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
