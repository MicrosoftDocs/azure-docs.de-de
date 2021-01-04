---
title: Klonen eines verwalteten Images in eine Imageversion mit der Azure-Befehlszeilenschnittstelle
description: Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle ein verwaltetes Image in eine Imageversion in einer Shared Image Gallery klonen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0e53bebf9cdb8c0fc084d04550c7444c1c01be50
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352863"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klonen eines verwalteten Images in eine Imageversion mit der Azure-Befehlszeilenschnittstelle
Wenn Sie über ein vorhandenes verwaltetes Image verfügen, das Sie in eine Shared Image Gallery klonen möchten, können Sie direkt aus dem verwalteten Image ein Image in der Shared Image Gallery erstellen. Nachdem Sie das neue Image getestet haben, können Sie das verwaltete Quellimage löschen. Sie können auch mithilfe von [PowerShell](image-version-managed-image-powershell.md) von einem verwalteten Image zu einem Katalog mit freigegebenen Images migrieren.

Images in Imagekatalogen weisen zwei Komponenten auf, die in diesem Beispiel erstellt werden:
- Eine **Imagedefinition** enthält Informationen zum Image und zu den Anforderungen für dessen Verwendung. Dies umfasst Angaben dazu, ob es sich um ein Windows- oder Linux-Image handelt, ob es spezialisiert oder generalisiert ist sowie welche Mindest- und Höchstanforderungen für den Arbeitsspeicher gelten. Außerdem sind Versionshinweise enthalten. Es ist eine Definition eines Imagetyps. 
- Bei der Verwendung eines Katalogs mit freigegebenen Images werden **Imageversionen** verwendet, um VMs zu erstellen. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Bei der Erstellung einer VM werden anhand der Imageversionen neue Datenträger für die VM erstellt. Imageversionen können mehrmals verwendet werden.


## <a name="before-you-begin"></a>Voraussetzungen

Um diesen Artikel abzuschließen, benötigen Sie einen vorhandenen [Katalog mit freigegebenen Images](shared-images-cli.md). 

Für das Beispiel in diesem Artikel muss ein verwaltetes Image eines generalisierten virtuellen Computers vorhanden sein. Weitere Informationen finden Sie unter [Erfassen eines verwalteten Images](./linux/capture-image.md). Wenn das verwaltete Image einen Datenträger für Daten enthält, darf dieser nicht größer als 1 TB sein.

Ersetzen Sie beim Durcharbeiten dieses Artikels bei Bedarf den Namen der Ressourcengruppe und des virtuellen Computers.



## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Da verwaltete Images immer generalisierte Images sind, erstellen Sie eine Imagedefinition mit `--os-state generalized` für ein generalisiertes Image.

Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. 

Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](./linux/shared-image-galleries.md#image-definitions).

Erstellen Sie mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) eine Imagedefinition im Katalog.

In diesem Beispiel heißt die Imagedefinition *myImageDefinition* und ist für ein [generalisiertes](./linux/shared-image-galleries.md#generalized-and-specialized-images) Linux-Betriebssystemimage vorgesehen. Verwenden Sie `--os-type Windows`, um eine Definition für Images zu erstellen, die ein Windows-Betriebssystem verwenden. 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>Erstellen der Imageversion

Erstellen Sie Versionen mithilfe von [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Sie müssen die ID des verwalteten Images, das als Grundlage zum Erstellen der Imageversion verwendet werden soll, als Eingabe übergeben. Sie können mit [az image list](/cli/azure/image?view#az-image-list) die IDs für Ihre Images abrufen. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel ist die Version des Images *1.0.0*, und es wird ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* mithilfe von zonenredundantem Speicher erstellt. Bei der Auswahl der Zielregionen für die Replikation ist zu beachten, dass Sie auch die *Quell* region als Ziel für die Replikation angeben müssen.

Übergeben Sie die ID des verwalteten Images im Parameter `--managed-image`.


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Sie können alle Ihre Imageversionsreplikate auch in [zonenredundantem Speicher](../storage/common/storage-redundancy.md) speichern, indem Sie `--storage-account-type standard_zrs` hinzufügen, wenn Sie die Imageversion erstellen.
>

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine VM aus einer [generalisierten Imageversion](vm-generalized-image-version-cli.md).

Weitere Informationen zur Bereitstellung von Erwerbsplaninformationen finden Sie unter [Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images](marketplace-images.md).
