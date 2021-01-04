---
title: Markieren eines virtuellen Azure-Computers mit der CLI
description: Erfahren Sie etwas über das Markieren eines virtuellen Computers mithilfe der Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 48f906bf0025bda03df226f32db1a0d6afdb9cee
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594883"
---
# <a name="how-to-tag-a-vm-using-the-cli"></a>Markieren einer VM mithilfe der CLI

In diesem Artikel wird das Markieren einer VM mit der Azure CLI beschrieben. Tags sind benutzerdefinierte Schlüssel-Wert-Paare, die direkt auf einer Ressource oder einer Ressourcengruppe platziert werden können. Azure unterstützt derzeit bis zu 50 Tags pro Ressource und Ressourcengruppe. Tags können zum Zeitpunkt der Erstellung auf einer Ressource platziert werden oder zu einer vorhandenen Ressource hinzugefügt werden. Sie können eine VM auch mit [Azure PowerShell](tag-powershell.md) markieren.


Sie können alle Eigenschaften für einen bestimmten virtuellen Computer einschließlich der Tags mit `az vm show` anzeigen.

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM --query tags
```

Zum Hinzufügen eines neuen VM-Tags über die Azure-Befehlszeilenschnittstelle können Sie den `azure vm update` -Befehl zusammen mit dem Tag-Parameter `--set`verwenden:

```azurecli-interactive
az vm update \
    --resource-group myResourceGroup \
    --name myVM \
    --set tags.myNewTagName1=myNewTagValue1 tags.myNewTagName2=myNewTagValue2
```

Um Tags zu entfernen, können Sie den `--remove`-Parameter im `azure vm update`-Befehl verwenden.

```azurecli-interactive
az vm update \
   --resource-group myResourceGroup \
   --name myVM \
   --remove tags.myNewTagName1
```

Nun, da wir unseren Ressourcen über die Azure-Befehlszeilenschnittstelle und das Portal Tags zugewiesen haben, werfen wir einen Blick auf die Nutzungsdetails, um die Tags im Abrechnungsportal anzuzeigen.


**Nächste Schritte**

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Informationen dazu, wie Tags Ihnen helfen können, die Verwendung Ihrer Azure-Ressourcen zu verwalten, finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md) und [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](../cost-management-billing/manage/usage-rate-card-overview.md).
