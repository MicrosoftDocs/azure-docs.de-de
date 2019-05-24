---
title: Verwenden von VM-Images zum Erstellen von Skalierungsgruppen in Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Azure CLI freigegebene VM-Images erstellen können, mit denen Sie VM-Skalierungsgruppen in Azure bereitstellen können.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: c002e7c107c4dcbcd7eeff9579fae6893483392e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "66156166"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Erstellen und Verwenden von freigegebenen Images für VM-Skalierungsgruppen mit der Azure CLI 2.0

Wenn Sie eine Skalierungsgruppe erstellen, geben Sie ein Image an, das beim Bereitstellen der VM-Instanzen verwendet wird. Der [Katalog mit freigegebenen Images](shared-image-galleries.md) vereinfacht das Freigeben benutzerdefinierter Images in Ihrer Organisation erheblich. Benutzerdefinierte Images sind wie Marketplace-Images, Sie erstellen sie jedoch selbst. Benutzerdefinierte Images können zum Starten von Konfigurationen verwendet werden, z.B. zum Vorabladen von Anwendungen, Anwendungskonfigurationen und anderen Betriebssystemkonfigurationen. Der Katalog mit geteilten Images gestattet es Ihnen, Ihre benutzerdefinierten VM-Images mit anderen Personen in Ihrer Organisation, innerhalb einer oder zwischen Regionen, innerhalb eines AAD-Mandanten zu teilen. Wählen Sie aus, welche Images Sie teilen möchten, in welchen Regionen Sie sie verfügbar machen möchten, und mit wem Sie sie teilen möchten. Sie können mehrere Kataloge erstellen, damit Sie geteilte Images logisch gruppieren können. Der Katalog ist eine Ressource der obersten Ebene, die vollständige rollenbasierte Zugriffssteuerung (RBAC) bereitstellt. Images bieten Versionsverwaltung, und Sie können sich entschließen, jede Imageversion in eine andere Gruppe von Azure-Regionen zu replizieren. Der Katalog funktioniert nur mit verwalteten Images. 

>[!NOTE]
> In diesem Artikel erfahren Sie, wie Sie ein allgemeines verwaltetes Image verwenden. Auf der Grundlage eines spezialisierten VM-Images kann keine Skalierungsgruppe erstellt werden.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Erstellen einer Skalierungsgruppe aus dem benutzerdefinierten VM-Image
Erstellen Sie eine Skalierungsgruppe mit [`az vmss create`](/cli/azure/vmss#az-vmss-create). Geben Sie anstelle eines Plattformimages, z.B. *UbuntuLTS* oder *CentOS*, den Namen Ihres benutzerdefinierten VM-Images an. Im folgenden Beispiel wird eine Skalierungsgruppe mit dem Namen *myScaleSet* erstellt, für die das benutzerdefinierte Image mit dem Namen *myImage* aus dem vorherigen Schritt verwendet wird:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [az group delete](/cli/azure/group), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `--no-wait` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist. Der Parameter `--yes` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nächste Schritte

Sie können auch mithilfe von Vorlagen eine Ressource im Katalog für freigegebene Images erstellen. Es stehen mehrere Azure-Schnellstartvorlagen zur Verfügung: 

- [Erstellen eines Katalogs mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Erstellen einer Imagedefinition in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Erstellen einer Imageversion in einem Katalog mit freigegebenen Images](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Erstellen eines virtuellen Computers aus einer Imageversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Sollten Probleme auftreten, finden Sie unter [Problembehandlung für Kataloge mit freigegebenen Images](troubleshooting-shared-images.md) mögliche Problembehandlungen.
