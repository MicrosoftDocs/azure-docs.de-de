---
title: Erstellen eines Images von einer VM mithilfe der Azure-Befehlszeilenschnittstelle
description: Informationen zum Erstellen eines Images einer VM in Shared Image Gallery in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: ca354ee662ebad0cd514d4822794b056ee6f9850
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805376"
---
# <a name="create-an-image-version-from-a-vm-in-azure-using-the-azure-cli"></a>Erstellen einer Imageversion von einer VM in Azure mithilfe der Azure CLI

Wenn Sie über eine VM verfügen, die Sie verwenden möchten, um mehrere identische VMs zu erstellen, können Sie mit der Azure CLI ein Image dieser VM in Shared Image Gallery erstellen. Ebenso können Sie auch [Azure PowerShell](image-version-vm-powershell.md) verwenden, um ein VM-Image zu erstellen.

Bei der Verwendung von Shared Image Gallery werden **Imageversionen** verwendet, um VMs zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wenn Sie eine Imageversion zum Erstellen eines virtuellen Computers verwenden, wird die Imageversion verwendet, um Datenträger für die neue VM zu erstellen. Imageversionen können mehrmals verwendet werden.


## <a name="before-you-begin"></a>Voraussetzungen

Um diesen Artikel nachzuvollziehen, benötigen Sie ein vorhandenen Shared Image Gallery. 

Außerdem muss eine VM in Azure in der gleichen Region wie Ihr Katalog vorhanden sein. 

Wenn ein Datenträger an die VM angefügt ist, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels die Ressourcennamen, wo dies erforderlich ist.

## <a name="get-information-about-the-vm"></a>Abrufen von Informationen zur VM

Eine Liste der verfügbaren VMs kann mithilfe von [az vm list](/cli/azure/vm#az-vm-list) angezeigt werden. 

```azurecli-interactive
az vm list --output table
```

Wenn Sie den VM-Namen kennen und wissen, in welcher Ressourcengruppe sich die VM befindet, rufen Sie die ID der VM mithilfe von [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) ab. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```


## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. 

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Stellen Sie sicher, dass die Imagedefinition den richtigen Typ aufweist. Wenn Sie die VM generalisiert haben (über Sysprep für Windows oder waagent -deprovision für Linux) sollten Sie unter Verwendung von `--os-state generalized` eine generalisierte Imagedefinition erstellen. Wenn Sie die VM verwenden möchten, ohne vorhandene Benutzerkonten zu entfernen, erstellen Sie mithilfe von `--os-state specialized` eine spezialisierte Imagedefinition.

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./shared-image-galleries.md#image-definitions).

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) eine Imagedefinition im Katalog.

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [spezialisiertes](./shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage vorgesehen. Verwenden Sie `--os-type Windows`, um eine Definition für Images zu erstellen, die ein Windows-Betriebssystem verwenden. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) eine Imageversion der VM.  

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel wird ein Image der Version *1.0.0* verwendet, und es werden unter Verwendung bon zonenredundantem Speicher zwei Replikate in der Region *USA, Westen-Mitte*, ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* erstellt. Die Replikationsregionen müssen die Region beinhalten, in der sich der virtuelle Quellcomputer befindet.

Ersetzen Sie den Wert `--managed-image` in diesem Beispiel durch die ID Ihrer VM aus dem vorherigen Schritt.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Fügen Sie beim Erstellen der Imageversion entweder `--storage-account-type  premium_lrs` hinzu, um Ihr Image in Premium-Speicher zu speichern, oder `--storage-account-type  standard_zrs`, um Ihr Image in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) zu speichern.
>

## <a name="next-steps"></a>Nächste Schritte

Erstellen einer VM aus dem [generalisierten Image](vm-generalized-image-version-cli.md) mithilfe der Azure CLI.

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).