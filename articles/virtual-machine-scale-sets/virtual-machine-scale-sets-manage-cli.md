---
title: Verwalten von VM-Skalierungsgruppen mit der Azure CLI | Microsoft-Dokumentation
description: Gängige Azure CLI-Befehle für die Verwaltung von VM-Skalierungsgruppen (etwa zum Starten und Beenden einer Instanz oder zum Ändern der Kapazität der Skalierungsgruppe).
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: b49182ebdcc93c4a51a55f27c3e0bf7a45307b7f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60618080"
---
# <a name="manage-a-virtual-machine-scale-set-with-the-azure-cli"></a>Verwalten einer VM-Skalierungsgruppe mit der Azure CLI
Während des Lebenszyklus einer Skalierungsgruppe müssen unter Umständen verschiedene Verwaltungsaufgaben durchgeführt werden. Darüber hinaus empfiehlt es sich, Skripts zum Automatisieren von verschiedenen Aufgaben im Lebenszyklus zu erstellen. In diesem Artikel werden einige der gängigen Azure CLI-Befehle behandelt, mit denen Sie diese Aufgaben durchführen können.

Für diese Verwaltungsaufgaben benötigen Sie die aktuelle Azure CLI. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Bei Bedarf können Sie [eine VM-Skalierungsgruppe mit der Azure CLI erstellen](quick-create-cli.md).


## <a name="view-information-about-a-scale-set"></a>Anzeigen von Informationen zu einer Skalierungsgruppe
Verwenden Sie [az vmss show](/cli/azure/vmss), um die allgemeinen Informationen zu einer Skalierungsgruppe anzuzeigen. Im folgenden Beispiel werden Informationen zur Skalierungsgruppe namens *myScaleSet* in der Ressourcengruppe *myResourceGroup* abgerufen. Geben Sie Ihre eigenen Namen wie folgt ein:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
```


## <a name="view-vms-in-a-scale-set"></a>Anzeigen von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [az vmss list-instances](/cli/azure/vmss), um eine Liste mit den VM-Instanzen in einer Skalierungsgruppe anzuzeigen. Im folgenden Beispiel werden alle VM-Instanzen der Skalierungsgruppe *myScaleSet* in der Ressourcengruppe *myResourceGroup* aufgeführt. Geben Sie für diese Namen Ihre eigenen Werte an:

```azurecli
az vmss list-instances \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --output table
```

Wenn Sie zusätzliche Informationen zu einer bestimmten VM-Instanz anzeigen möchten, fügen Sie [az vmss get-instance-view](/cli/azure/vmss) den Parameter `--instance-id` hinzu, und geben Sie die gewünschte Instanz an. Im folgenden Beispiel werden Informationen zur VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* angezeigt. Geben Sie Ihre eigenen Namen wie folgt ein:

```azurecli
az vmss get-instance-view \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --instance-id 0
```


## <a name="list-connection-information-for-vms"></a>Auflisten von Verbindungsinformationen für virtuelle Computer
Wenn Sie eine Verbindung mit den virtuellen Computern in einer Skalierungsgruppe herstellen möchten, stellen Sie eine SSH- oder RDP-Verbindung mit einer zugewiesenen öffentlichen IP-Adresse und Portnummer her. Standardmäßig werden dem Azure-Lastenausgleich, der den Datenverkehr von Remoteverbindungen an die einzelnen virtuellen Computer weiterleitet, Regeln für die Netzwerkadressübersetzung (Network Address Translation, NAT) hinzugefügt. Verwenden Sie [az vmss list-instance-connection-info](/cli/azure/vmss), um die Adresse und die Ports für die Verbindungsherstellung mit VM-Instanzen in einer Skalierungsgruppe aufzulisten. Im folgenden Beispiel werden Verbindungsinformationen für VM-Instanzen in der Skalierungsgruppe namens *myScaleSet* und in der Ressourcengruppe *myResourceGroup* aufgeführt. Geben Sie für diese Namen Ihre eigenen Werte an:

```azurecli
az vmss list-instance-connection-info \
    --resource-group myResourceGroup \
    --name myScaleSet
```


## <a name="change-the-capacity-of-a-scale-set"></a>Ändern der Kapazität einer Skalierungsgruppe
Die obigen Befehle dienen zum Anzeigen von Informationen zu Ihrer Skalierungsgruppe und zu den VM-Instanzen. Wenn Sie die Anzahl von Instanzen in der Skalierungsgruppe erhöhen oder verringern möchten, können Sie die Kapazität ändern. Die Skalierungsgruppe erstellt oder entfernt die erforderliche Anzahl von virtuellen Computern und konfiguriert die virtuellen Computer anschließend für den Empfang von Anwendungsdatenverkehr.

Verwenden Sie [az vmss show](/cli/azure/vmss), und führen Sie eine Abfrage nach *sku.capacity* durch, um die Anzahl der zurzeit in einer Skalierungsgruppe vorhandenen Instanzen anzuzeigen:

```azurecli
az vmss show \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Sie können dann die Anzahl der virtuellen Computer in der Skalierungsgruppe mit [az vmss scale](/cli/azure/vmss) manuell erhöhen oder verringern. Im folgenden Beispiel wird die Anzahl der virtuellen Computer in der Skalierungsgruppe auf *5* festgelegt:

```azurecli
az vmss scale \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --new-capacity 5
```

Die Aktualisierung der Skalierungsgruppenkapazität dauert ein paar Minuten. Wenn Sie die Kapazität einer Skalierungsgruppe verringern, werden die virtuellen Computer mit den höchsten Instanz-IDs zuerst entfernt.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Beenden und Starten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [az vmss stop](/cli/azure/vmss#az-vmss-stop), um virtuelle Computer in einer Skalierungsgruppe zu beenden. Mit dem Parameter `--instance-ids` können Sie die zu beendenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe beendet. Wenn Sie mehrere virtuelle Computer beenden möchten, trennen Sie die einzelnen Instanz-IDs jeweils durch ein Leerzeichen.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* beendet. Geben Sie Ihre eigenen Werte wie folgt an:

```azurecli
az vmss stop --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```

Beendete virtuelle Computer bleiben weiter zugeordnet, und es fallen weiterhin Compute-Gebühren für sie an. Wenn Sie stattdessen die Zuordnung der virtuellen Computer aufheben möchten, sodass nur noch Speichergebühren anfallen, verwenden Sie [az vmss deallocate](/cli/azure/vmss). Wenn Sie die Zuordnung mehrerer virtueller Computer aufheben möchten, trennen Sie die einzelnen Instanz-IDs jeweils durch ein Leerzeichen. Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* beendet und ihre Zuordnung aufgehoben. Geben Sie Ihre eigenen Werte wie folgt an:

```azurecli
az vmss deallocate --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


### <a name="start-vms-in-a-scale-set"></a>Starten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [az vmss start](/cli/azure/vmss), um virtuelle Computer in einer Skalierungsgruppe zu starten. Mit dem Parameter `--instance-ids` können Sie die zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe gestartet. Wenn Sie mehrere virtuelle Computer starten möchten, trennen Sie die einzelnen Instanz-IDs jeweils durch ein Leerzeichen.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* gestartet. Geben Sie Ihre eigenen Werte wie folgt an:

```azurecli
az vmss start --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="restart-vms-in-a-scale-set"></a>Neustarten von virtuellen Computern in einer Skalierungsgruppe
Verwenden Sie [az vmss restart](/cli/azure/vmss), um virtuelle Computer in einer Skalierungsgruppe neu zu starten. Mit dem Parameter `--instance-ids` können Sie die neu zu startenden virtuellen Computer angeben. Wenn Sie keine Instanz-ID angeben, werden alle virtuellen Computer in der Skalierungsgruppe neu gestartet. Wenn Sie mehrere virtuelle Computer neu starten möchten, trennen Sie die einzelnen Instanz-IDs jeweils durch ein Leerzeichen.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* neu gestartet. Geben Sie Ihre eigenen Werte wie folgt an:

```azurecli
az vmss restart --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="remove-vms-from-a-scale-set"></a>Entfernen von virtuellen Computern aus einer Skalierungsgruppe
Verwenden Sie [az vmss delete-instances](/cli/azure/vmss), um virtuelle Computer in einer Skalierungsgruppe zu entfernen. Mit dem Parameter `--instance-ids` können Sie die zu entfernenden virtuellen Computer angeben. Wenn Sie für die Instanz-ID ein Sternchen (*) angeben, werden alle virtuellen Computer in der Skalierungsgruppe entfernt. Wenn Sie mehrere virtuelle Computer entfernen möchten, trennen Sie die einzelnen Instanz-IDs jeweils durch ein Leerzeichen.

Im folgenden Beispiel wird die VM-Instanz *0* in der Skalierungsgruppe namens *myScaleSet* und der Ressourcengruppe *myResourceGroup* entfernt. Geben Sie Ihre eigenen Werte wie folgt an:

```azurecli
az vmss delete-instances --resource-group myResourceGroup --name myScaleSet --instance-ids 0
```


## <a name="next-steps"></a>Nächste Schritte
Weitere gängige Aufgaben für Skalierungsgruppen sind beispielsweise das [Bereitstellen einer Anwendung](virtual-machine-scale-sets-deploy-app.md) und das [Upgraden von VM-Instanzen](virtual-machine-scale-sets-upgrade-scale-set.md). Außerdem können Sie mit der Azure-Befehlszeilenschnittstelle [Regeln für die automatische Skalierung konfigurieren](virtual-machine-scale-sets-autoscale-overview.md).
