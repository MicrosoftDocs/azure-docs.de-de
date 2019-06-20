---
title: include file
description: include file
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814718"
---
## <a name="before-you-begin"></a>Voraussetzungen

Für das Beispiel in diesem Artikel muss ein verwaltetes Image eines generalisierten virtuellen Computers vorhanden sein. Weitere Informationen finden Sie unter [Tutorial: Erstellen eines benutzerdefinierten Images eines virtuellen Azure-Computers mit der Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images). Wenn das verwaltete Image einen Datenträger für Daten enthält, darf dieser nicht größer als 1 TB sein.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. 

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren**. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Wenn Sie die CLI lieber lokal installieren und verwenden, finden Sie hierzu Informationen unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-image-gallery"></a>Erstellen eines Imagekatalogs 

Ein Imagekatalog ist die primäre Ressource, die zur Ermöglichung des Teilens von Images verwendet wird. Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname darf keine Bindestriche enthalten.   Katalognamen müssen innerhalb Ihres Abonnements eindeutig sein. 

Erstellen Sie einen Imagekatalog mit [az sig create](/cli/azure/sig#az-sig-create). Im folgenden Beispiel wird ein Katalog namens *my Gallery* in *myGalleryRG* erstellt.

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Erstellen einer Imagedefinition

Imagedefinitionen erstellen eine logische Gruppierung von Images. Sie werden verwendet, um Informationen über die Imageversionen zu verwalten, die in ihnen erstellt werden. Namen für Imagedefinition können aus Groß- und Kleinbuchstaben, Zahlen, Punkten und (Binde)Strichen bestehen. Weitere Informationen zu den Werten, die Sie für eine Imagedefinition angeben können, finden Sie unter [Imagedefinitionen](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions).

Erstellen Sie eine anfängliche Imagedefinition im Katalog mithilfe von [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>Erstellen einer Imageversion 

Erstellen Sie nach Bedarf Versionen des Images mittels [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create). Sie müssen die ID des verwalteten Images, das als Grundlage zum Erstellen der Imageversion verwendet werden soll, als Eingabe übergeben. Sie können [az image list](/cli/azure/image?view#az-image-list) verwenden, um Informationen zu Images abzurufen, die sich in einer Ressourcengruppe befinden. 

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *Hauptversion*.*Nebenversion*.*Patch*.

In diesem Beispiel wird ein Image der Version *1.0.0* verwendet, und es werden unter Verwendung bon zonenredundantem Speicher zwei Replikate in der Region *USA, Westen-Mitte*, ein Replikat in der Region *USA, Süden-Mitte* und ein Replikat in der Region *USA, Osten 2* erstellt.


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.
>
> Sie können alle Ihre Imageversionsreplikate auch in [zonenredundantem Speicher](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) speichern, indem Sie `--storage-account-type standard_zrs` hinzufügen, wenn Sie die Imageversion erstellen.
>

## <a name="share-the-gallery"></a>Teilen des Katalogs

Wir empfehlen, dass Sie mit anderen Benutzern auf Ebene des Katalogs teilen. Um die Objekt-ID Ihres Katalogs abzurufen, verwenden Sie [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Verwenden Sie die Objekt-ID als Bereich, zusammen mit einer E-Mail-Adresse und [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), um einem Benutzer Zugriff auf den geteilten Imagekatalog zu gewähren.

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


