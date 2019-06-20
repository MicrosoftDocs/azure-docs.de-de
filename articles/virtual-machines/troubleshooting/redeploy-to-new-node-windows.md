---
title: Erneutes Bereitstellen von virtuellen Windows-Computern in Azure | Microsoft Docs
description: Erneutes Bereitstellen von virtuellen Windows-Computern in Azure zum Beheben von Problemen mit der Remotedesktopverbindung.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b71ca93ac3e3e6c77c5f87b4859a2e3e0e1040d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62103981"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Erneutes Bereitstellen von virtuellen Windows-Computern in einem neuen Azure-Knoten
Wenn Sie Schwierigkeiten mit der Problembehandlung bei der Remotedesktopverbindung (RDP) oder dem Anwendungszugriff auf einen Windows-basierten virtuellen Azure-Computer haben, lassen sich diese u. U. durch das erneute Bereitstellen des virtuellen Computers beseitigen. Wenn Sie eine VM erneut bereitstellen, wird die VM von Azure heruntergefahren. Anschließen wird sie innerhalb der Azure-Infrastruktur auf einen neuen Knoten verschoben und dann wieder eingeschaltet. Dabei bleiben alle Ihre Konfigurationsoptionen und zugehörigen Ressourcen erhalten. In diesem Artikel erfahren Sie, wie ein virtueller Computer mithilfe von Azure PowerShell oder dem Azure-Portal erneut bereitgestellt wird.

> [!NOTE]
> Nachdem Sie einen virtuellen Computer erneut bereitgestellt haben, geht der temporäre Datenträger verloren, und die der virtuellen Netzwerkschnittstelle zugeordneten dynamischen IP-Adressen werden aktualisiert. 


## <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell
Stellen Sie sicher, dass Sie das aktuelle Azure PowerShell 1.x auf Ihrem virtuellen Computer installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/overview).

Das folgende Beispiel stellt den virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` bereit:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nächste Schritte
Falls beim Herstellen einer Verbindung mit Ihrem virtuellen Computer Probleme auftreten, finden Sie spezifische Hilfe unter [Problembehandlung bei RDP-Verbindungen](troubleshoot-rdp-connection.md) oder [Ausführliche Schritte zur Problembehandlung bei RDP](detailed-troubleshoot-rdp.md). Sie können auch die Informationen zur [Problembehandlung bei der Anwendung](../windows/troubleshoot-app-connection.md) lesen, wenn Sie auf eine Anwendung, die auf Ihrem virtuellen Computer ausgeführt wird, nicht zugreifen können.

