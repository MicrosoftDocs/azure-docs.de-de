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
ms.openlocfilehash: 8d0f9866864ca4b02ca6238be2ac44537a586c2d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178103"
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

Aktualisieren Sie die Beschreibung eines Katalogs mit ([az sig update](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Aktualisieren Sie die Beschreibung einer Imagedefinition mit [az sig image-definition update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Aktualisieren Sie eine Imageversion zum Hinzufügen einer Region als Replikationsziel mit [az sig image-version update](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-update). Diese Änderung wird einige Zeit in Anspruch nehmen, da das Image in eine neue Region repliziert wird.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

## <a name="delete-resources"></a>Löschen von Ressourcen

Ressourcen müssen in umgekehrter Reihenfolge gelöscht werden, beginnend mit der Imageversion. Nachdem alle Imageversionen gelöscht sind, können Sie die Imagedefinition löschen. Nachdem alle Imagedefinitionen gelöscht sind, können Sie den Katalog löschen. 

Löschen Sie eine Imageversion mit [az sig image-version delete](https://docs.microsoft.com/cli/azure/sig/image-version?view=azure-cli-latest#az-sig-image-version-delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Löschen Sie eine Imagedefinition mit [az sig image-definition delete](https://docs.microsoft.com/cli/azure/sig/image-definition?view=azure-cli-latest#az-sig-image-definition-delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Löschen Sie einen Imagekatalog mit [az sig delete](https://docs.microsoft.com/cli/azure/sig?view=azure-cli-latest#az-sig-delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```
