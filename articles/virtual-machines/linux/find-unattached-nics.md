---
title: Suchen und Löschen nicht angefügter Azure-Netzwerkadapter
description: Hier erfahren Sie, wie Sie mithilfe der Azure CLI Azure-NICs suchen und löschen, die nicht an virtuelle Computer angefügt sind.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/10/2018
ms.author: cynthn
ms.openlocfilehash: 5b28226cfe6bc51a2619c4dd63e666ddd51dad2a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016198"
---
# <a name="how-to-find-and-delete-unattached-network-interface-cards-nics-for-azure-vms"></a>Suchen und Löschen nicht angefügter NICs (Network Interface Cards, Netzwerkschnittstellenkarten) für virtuelle Azure-Computer
Wenn Sie einen virtuellen Computer (Virtual Machine, VM) in Azure löschen, werden die NICs (Network Interface Cards, Netzwerkschnittstellenkarten) standardmäßig nicht gelöscht. Wenn Sie mehrere virtuelle Computer erstellen und wieder löschen, nutzen die nicht verwendeten NICs weiterhin die internen IP-Adressleases. Wenn Sie dann weitere VM-NICs erstellen, können diese möglicherweise keine IP-Adressleases im Adressraum des Subnetzes beziehen. In diesem Artikel erfahren Sie, wie Sie nicht angefügte NICs suchen und löschen.

## <a name="find-and-delete-unattached-nics"></a>Suchen und Löschen nicht angefügter Netzwerkadapter

In der Eigenschaft *VirtualMachine* einer NIC werden die ID und die Ressourcengruppe des virtuellen Computers gespeichert, an den die NIC angefügt ist. Das folgende Skript durchläuft alle NICs in einem Abonnement und prüft, ob die Eigenschaft *virtualMachine* NULL ist. Ist die Eigenschaft NULL, ist die NIC an keinen virtuellen Computer angefügt.

Es wird dringend empfohlen, bei der ersten Skriptausführung die Variable *deleteUnattachedNics* auf *0* festzulegen, um alle nicht angefügten NICs anzuzeigen. Führen Sie das Skript nach der Prüfung der ausgegebenen Liste erneut aus, und legen Sie dabei den Wert für *deleteUnattachedNics* auf *1* fest, um alle nicht angefügten NICs zu löschen.

```azurecli
# Set deleteUnattachedNics=1 if you want to delete unattached NICs
# Set deleteUnattachedNics=0 if you want to see the Id(s) of the unattached NICs
deleteUnattachedNics=0

unattachedNicsIds=$(az network nic list --query '[?virtualMachine==`null`].[id]' -o tsv)
for id in ${unattachedNicsIds[@]}
do
   if (( $deleteUnattachedNics == 1 ))
   then

       echo "Deleting unattached NIC with Id: "$id
       az network nic delete --ids $id
       echo "Deleted unattached NIC with Id: "$id
   else
       echo $id
   fi
done
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen und Verwalten virtueller Netzwerke in Azure finden Sie unter [Verwalten von virtuellen Azure-Netzwerken und virtuellen Linux-Computern mit der Azure-Befehlszeilenschnittstelle](tutorial-virtual-network.md).
