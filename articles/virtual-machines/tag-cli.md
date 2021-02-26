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
ms.openlocfilehash: 32d15730557c96362602b5e324254c76637ecb55
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897441"
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

Nachdem wir unseren Ressourcen über die Azure CLI und das Portal Tags zugewiesen haben, werfen wir einen Blick auf die Nutzungsdetails, um die Tags im Abrechnungsportal anzuzeigen.

### <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Markieren Ihrer Azure-Ressourcen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md) und [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../azure-resource-manager/management/tag-resources.md).
- Unter [Grundlegendes zu Ihrer Microsoft Azure-Rechnung und „Verwenden der Azure-Abrechnungs-APIs, um programmgesteuerte Einblicke in die Nutzung Ihrer Azure-Ressourcen zu erlangen“ erfahren Sie, wie Tags Sie bei der Verwaltung Ihrer Azure-Ressourcenverwendung unterstützen können.](../cost-management-billing/understand/review-individual-bill.md)
