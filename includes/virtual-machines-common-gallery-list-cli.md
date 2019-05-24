---
title: Includedatei
description: Includedatei
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1e78109472668c0f9a73af6430253a0d709979af
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156161"
---
## <a name="using-rbac-to-share-images"></a>Verwenden von RBAC zum Teilen von Abbildern

Sie können Images zwischen Abonnements mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) teilen. Jeder Benutzer, der Leseberechtigungen für eine Imageversion besitzt, auch über Abonnements hinweg, kann mithilfe der Imageversion einen virtuellen Computer bereitstellen.

Weitere Informationen zum Teilen von Ressourcen mittels RBAC finden Sie unter [Verwalten des Zugriffs mithilfe von RBAC und der Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Auflisten von Informationen

Den Speicherort, Status und weitere Informationen zu den verfügbaren Abbildkatalogen können Sit mit [az sig list](/cli/azure/sig#az-sig-list) abrufen.

```azurecli-interactive 
az sig list -o table
```

Die Abbilddefinitionen in einem Katalog, einschließlich der Informationen zum Betriebssystemtyp und -status können Sie mit [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) auflisten.

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Die Versionen geteilter Abbilder in einem Katalog können Sie mit [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) auflisten.

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myImageDefinition -o table
```

Die ID einer Abbildversion können Sie mit [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show) abrufen.

```azurecli-interactive
az sig image-version show \
   -g myGalleryRG \
   -r myGallery \
   -i myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```