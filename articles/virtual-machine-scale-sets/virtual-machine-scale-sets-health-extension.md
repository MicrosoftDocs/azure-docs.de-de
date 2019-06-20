---
title: Verwenden der Application Health-Erweiterung mit Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit der Application Health-Erweiterung die Integrität Ihrer Anwendungen überwachen, die in VM-Skalierungsgruppen bereitgestellt werden.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: manayar
ms.openlocfilehash: d1cff1011e190e5fbb2874657cbdfbdc68bde0c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60619823"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Verwenden der Application Health-Erweiterung mit VM-Skalierungsgruppen
Überwachung der Integrität Ihrer Anwendung ist ein wichtiges Signal für das Verwalten und Aktualisieren Ihrer Bereitstellung. Azure-VM-Skalierungsgruppen bieten Unterstützung für [parallele Upgrades](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) einschließlich [automatischer Betriebssystemimage-Upgrades](virtual-machine-scale-sets-automatic-upgrade.md), die von der Überwachung der Integrität der einzelnen Instanzen beim Upgrade Ihrer Bereitstellung abhängig sind.

In diesem Artikel erfahren Sie, wie Sie mit der Application Health-Erweiterung die Integrität Ihrer Anwendungen überwachen, die in VM-Skalierungsgruppen bereitgestellt werden.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie mit Folgendem vertraut sind:
-   [Erweiterungen](../virtual-machines/extensions/overview.md) für virtuelle Azure-Computer
-   [Ändern](virtual-machine-scale-sets-upgrade-scale-set.md) der VM-Skalierungsgruppen

## <a name="when-to-use-the-application-health-extension"></a>Anwendungsbereich der Application Health-Erweiterung
Die Application Health-Erweiterung wird in einer VM-Skalierungsgruppeninstanz bereitgestellt und berichtet von der Skalierungsgruppeninstanz aus über die VM-Integrität. Sie können die Erweiterung konfigurieren, um einen Anwendungsendpunkt zu prüfen und den Status der Anwendung in dieser Instanz zu aktualisieren. Dieser Instanzstatus wird von Azure geprüft, um festzustellen, ob eine Instanz für Upgradevorgänge geeignet ist.

Da die Erweiterung von einem virtuellen Computer aus über die Integrität berichtet, kann die Erweiterung in Situationen verwendet werden, in denen externe Tests wie z.B. Anwendungsintegritätstests (die benutzerdefinierte Azure Load Balancer-[Tests](../load-balancer/load-balancer-custom-probe-overview.md) verwenden) nicht genutzt werden können.

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Application Health-Erweiterung. Die Erweiterung erfordert mindestens eine „Tcp“- oder „http“-Anforderung mit einem zugeordneten Port bzw. Anforderungspfad.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Eigenschaftswerte

| NAME | Wert/Beispiel | Datentyp
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | Zeichenfolge |
| type | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | Zeichenfolge |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Einstellungen

| NAME | Wert/Beispiel | Datentyp
| ---- | ---- | ----
| protocol | `http` oder `tcp` | Zeichenfolge |
| port | Optional, wenn das Protokoll `http` ist, obligatorisch, wenn das Protokoll `tcp` ist. | int |
| requestPath | Obligatorisch, wenn das Protokoll `http` ist, nicht zulässig, wenn das Protokoll `tcp` ist. | Zeichenfolge |

## <a name="deploy-the-application-health-extension"></a>Bereitstellen der Application Health-Erweiterung
Es gibt mehrere Möglichkeiten, die Application Health-Erweiterung für Ihre Skalierungsgruppen bereitzustellen, wie in den folgenden Beispielen beschrieben.

### <a name="rest-api"></a>REST-API

Im folgenden Beispiel wird die Application Health-Erweiterung (mit dem Namen „myHealthExtension“) dem „extensionProfile“ im Skalierungsgruppenmodell einer Windows-basierten Skalierungsgruppe hinzugefügt.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
Verwenden Sie `PATCH`, um eine bereits bereitgestellte Erweiterung zu bearbeiten.

### <a name="azure-powershell"></a>Azure PowerShell

Verwenden Sie das [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)-Cmdlet, um die Application Health-Erweiterung der Skalierungsgruppen-Modelldefinition hinzuzufügen.

Im folgenden Beispiel wird die Application Health-Erweiterung in dem Skalierungsgruppenmodell einer Windows-basierten Skalierungsgruppe hinzugefügt. Im Beispiel wird das neue Az PowerShell-Modul verwendet.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Verwenden Sie [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set), um die Application Health-Erweiterung der Skalierungsgruppen-Modelldefinition hinzuzufügen.

Im folgenden Beispiel wird die Application Health-Erweiterung dem Skalierungsgruppenmodell einer Windows-basierten Skalierungsgruppe hinzugefügt.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Problembehandlung
Die Ausgabe der Erweiterungsausführung wird in Dateien in folgenden Verzeichnissen protokolliert:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

Die Protokolle erfassen auch in regelmäßigen Abständen den Integritätsstatus der Anwendung.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über das [Bereitstellen Ihrer Anwendung](virtual-machine-scale-sets-deploy-app.md) in VM-Skalierungsgruppen.
