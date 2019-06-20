---
title: Verschieben einer Linux-VM in Azure | Microsoft-Dokumentation
description: Verschieben Sie einen virtuellen Linux-Computer im Resource Manager-Bereitstellungsmodell in ein anderes Azure-Abonnement oder eine andere Ressourcengruppe.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 09/12/2018
ms.author: cynthn
ms.openlocfilehash: d2d3f36c9b4ee0557f9e060bec762877a94ea637
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473932"
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Verschieben einer Linux-VM in ein anderes Abonnement oder eine andere Ressourcengruppe
In diesem Artikel erfahren Sie, wie Sie einen virtuellen Linux-Computer (Virtual Machine, VM) zwischen Ressourcengruppen oder Abonnements verschieben. Das Verschieben einer VM zwischen Abonnements kann nützlich sein, wenn Sie eine VM in einem persönlichen Abonnement erstellt haben und sie nun in das Abonnement Ihres Unternehmens verschieben möchten.

> [!IMPORTANT]
>Zum aktuellen Zeitpunkt können Azure Managed Disks nicht verschoben werden. 
>
>Im Rahmen der Verschiebung werden neue Ressourcen-IDs erstellt. Nach dem Verschieben des virtuellen Computers müssen Sie Ihre Tools und Skripts aktualisieren, damit die neuen Ressourcen-IDs verwendet werden. 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>Verwenden der Azure-Befehlszeilenschnittstelle zum Verschieben einer VM


Bevor Sie Ihren virtuellen Computer mithilfe der Azure-Befehlszeilenschnittstelle verschieben, müssen Sie sich vergewissern, dass sich das Quellabonnement und das Zielabonnement im gleichen Mandanten befinden. Um zu überprüfen, ob beide Abonnements die gleiche Mandanten-ID aufweisen, verwenden Sie [az account show](/cli/azure/account).

```azurecli-interactive
az account show --subscription mySourceSubscription --query tenantId
az account show --subscription myDestinationSubscription --query tenantId
```
Wenn die Mandanten-IDs für das Quell- und das Zielabonnement nicht gleich sind, müssen Sie sich an den [Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) wenden, um die Ressourcen in einen neuen Mandanten zu verschieben.

Um eine VM erfolgreich verschieben zu können, müssen Sie die VM und alle unterstützenden Ressourcen verschieben. Verwenden Sie den Befehl [azure resource list](/cli/azure/resource), um alle Ressourcen einer Ressourcengruppe und die dazugehörigen IDs aufzulisten. Es ist hilfreich, die Ausgabe dieses Befehls per Pipe-Vorgang an eine Datei zu übergeben, damit Sie die IDs später kopieren und in Befehle einfügen können.

```azurecli-interactive
az resource list --resource-group "mySourceResourceGroup" --query "[].{Id:id}" --output table
```

Verwenden Sie [az resource move](/cli/azure/resource), um einen virtuellen Computer und die dazugehörigen Ressourcen in eine andere Ressourcengruppe zu verschieben. Im folgenden Beispiel wird gezeigt, wie Sie eine VM und die wichtigsten Ressourcen dafür verschieben. Verwenden Sie den Parameter **-ids**, und übergeben Sie eine kommagetrennte Liste (ohne Leerstellen) mit IDs für die zu verschiebenden Ressourcen.

```azurecli-interactive
vm=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
nic=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
nsg=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG
pip=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIPAddress
vnet=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Network/virtualNetworks/myVNet
diag=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mydiagnosticstorageaccount
storage=/subscriptions/mySourceSubscriptionID/resourceGroups/mySourceResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccountname    

az resource move \
    --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag \
    --destination-group "myDestinationResourceGroup"
```

Wenn Sie den virtuellen Computer und seine Ressourcen in ein anderes Abonnement verschieben möchten, fügen Sie den Parameter **--destination-subscriptionId** hinzu, um das Zielabonnement anzugeben.

Wenn Sie aufgefordert werden, zu bestätigen, dass die angegebene Ressource verschoben werden soll, geben Sie **J** ein.

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie können viele verschiedene Arten von Ressourcen zwischen Ressourcengruppen und Abonnements verschieben. Weitere Informationen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../../resource-group-move-resources.md).    

