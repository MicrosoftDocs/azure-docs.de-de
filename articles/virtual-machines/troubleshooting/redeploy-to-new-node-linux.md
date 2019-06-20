---
title: Erneutes Bereitstellen von virtuellen Linux-Computern in Azure | Microsoft Docs
description: Erneutes Bereitstellen von virtuellen Linux-Computern zum Beheben von Problemen mit der SSH-Verbindung.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 1c1ac1a20c1c06db10c1462c95f4d924c5ec09d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116945"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Erneutes Bereitstellen eines virtuellen Linux-Computers in einem neuen Azure-Knoten
Wenn Sie Schwierigkeiten mit der Problembehandlung bei SSH oder dem Anwendungszugriff auf einen virtuellen Linux-Computer in Azure haben, lassen sich diese u.U. durch erneutes Bereitstellen des virtuellen Computers beseitigen. Wenn Sie einen virtuellen Computer erneut bereitstellen, wird er innerhalb der Azure-Infrastruktur auf einen neuen Knoten verschoben und dann wieder eingeschaltet. Dabei werden alle Ihre Konfigurationsoptionen und zugehörigen Ressourcen beibehalten. In diesem Artikel erfahren Sie, wie ein virtueller Computer mithilfe der Azure-Befehlszeilenschnittstelle oder dem Azure-Portal erneut bereitgestellt wird.

> [!NOTE]
> Nachdem Sie einen virtuellen Computer erneut bereitgestellt haben, geht der temporäre Datenträger verloren, und die der virtuellen Netzwerkschnittstelle zugeordneten dynamischen IP-Adressen werden aktualisiert. 


## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2), und melden Sie sich mithilfe von [az login](/cli/azure/reference-index) bei Ihrem Azure-Konto an.

Stellen Sie mit [az vm redeploy](/cli/azure/vm) Ihren virtuellen Computer erneut bereit. Im folgenden Beispiel wird die VM *myVM* in der Ressourcengruppe *myResourceGroup* erneut bereitgestellt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Verwenden der klassischen Azure-Befehlszeilenschnittstelle
Installieren Sie die [neueste Version der Azure-Befehlszeilenschnittstelle](../../cli-install-nodejs.md), und melden Sie sich bei Ihrem Azure-Konto an. Stellen Sie sicher, dass Sie sich im Resource Manager-Modus befinden (`azure config mode arm`).

Im folgenden Beispiel wird die VM *myVM* in der Ressourcengruppe *myResourceGroup* erneut bereitgestellt:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nächste Schritte
Falls beim Herstellen einer Verbindung mit Ihrem virtuellen Computer Probleme auftreten, finden Sie spezifische Hilfe unter [Problembehandlung bei SSH-Verbindungen](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [Ausführliche Schritte zur Problembehandlung bei SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Sie können auch die Informationen zur [Problembehandlung bei der Anwendung](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lesen, wenn Sie auf eine Anwendung, die auf Ihrem virtuellen Computer ausgeführt wird, nicht zugreifen können.


