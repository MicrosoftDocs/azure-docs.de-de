---
title: Verwenden der Erweiterungssequenzierung mit Azure-VM-Skalierungsgruppen | Microsoft-Dokumentation
description: Informationen zum Sequenzieren von Erweiterungen, wenn Sie mehrere Erweiterungen in VM-Skalierungsgruppen bereitstellen.
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
ms.openlocfilehash: 2e5dfda16c4828b3113fc50d4cffc79fe6ff19e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60620171"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Bereitstellen des Sequenzierens von Erweiterungen in VM-Skalierungsgruppen
Azure-VM-Erweiterungen bieten Funktionen wie die Konfiguration nach der Bereitstellung sowie Verwaltung, Überwachung, Sicherheit und vieles mehr. Bei Produktionsbereitstellungen wird in der Regel eine Kombination von mehreren Erweiterungen verwendet, die so konfiguriert sind, dass die VM-Instanzen die gewünschten Ergebnisse erzielen.

Wenn Sie mehrere Erweiterungen auf einem virtuellen Computer verwenden, müssen Sie sicherstellen, dass Erweiterungen, die die gleichen Betriebssystemressourcen erfordern, nicht versuchen, diese Ressourcen gleichzeitig abzurufen. Einige Erweiterungen hängen auch davon ab, dass andere Erweiterungen die erforderlichen Konfigurationen bereitstellen, wie z.B. Umgebungseinstellungen und Geheimnisse. Ohne die richtige Reihenfolge und Sequenzierung können bei abhängigen Erweiterungsbereitstellungen Fehler auftreten.

In diesem Artikel wird erläutert, wie Sie Erweiterungen sequenzieren können, die für die VM-Instanzen in VM-Skalierungsgruppen konfiguriert werden sollen.

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie mit Folgendem vertraut sind:
-   [Erweiterungen](../virtual-machines/extensions/overview.md) für virtuelle Azure-Computer
-   [Ändern](virtual-machine-scale-sets-upgrade-scale-set.md) der VM-Skalierungsgruppen

## <a name="when-to-use-extension-sequencing"></a>Anwendungsfälle für die Erweiterungssequenzierung
Das Sequenzieren von Erweiterungen ist für Skalierungsgruppen nicht obligatorisch, und sofern nicht anders angegeben, können Erweiterungen auf einer Skalierungsgruppeninstanz in beliebiger Reihenfolge bereitgestellt werden.

Wenn Ihr Skalierungsgruppenmodell beispielsweise über zwei im Modell angegebene Erweiterungen verfügt – ExtensionA und ExtensionB – dann kann eine der folgenden Bereitstellungssequenzen auftreten:
-   ExtensionA -> ExtensionB
-   ExtensionB -> ExtensionA

Wenn Ihre Anwendung erfordert, dass ExtensionA immer vor ExtensionB bereitzustellen ist, sollten Sie die Erweiterungssequenzierung wie in diesem Artikel beschrieben verwenden. Mit der Erweiterungssequenzierung erfolgt jetzt nur eine einzige Sequenz:
-   ExtensionA -> ExtensionB

Alle Erweiterungen, die nicht in einer definierten Bereitstellungssequenz angegeben sind, können jederzeit, auch vor, nach oder während einer definierten Sequenz bereitgestellt werden. Erweiterungssequenzierung gibt nur an, dass eine spezifische Erweiterung nach einer anderen spezifischen Erweiterung bereitgestellt wird. Sie hat keine Auswirkungen auf die Bereitstellung einer anderen im Modell definierten Erweiterung.

Wenn Ihr Skalierungsgruppenmodell z.B. über drei im Modell angegebene Erweiterungen verfügt – ExtensionA, ExtensionB und ExtensionC – und für ExtensionC wird festgelegt, dass sie nach ExtensionA bereitgestellt wird, kann eine der folgenden Bereitstellungssequenzen auftreten:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Wenn Sie sicherstellen müssen, dass keine andere Erweiterung bereitgestellt wird, während die definierte Erweiterungssequenz ausgeführt wird, sollten Sie alle Erweiterungen in Ihrem Skalierungsgruppenmodell sequenzieren. Im obigen Beispiel kann für ExtensionB festgelegt werden, dass sie nach der ExtensionC bereitgestellt wird, sodass nur eine einzige Sequenz auftreten kann:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>Durchführung der Erweiterungssequenzierung
Zur Bereitstellung der Erweiterungssequenzierung müssen Sie die Erweiterungsdefinition im Skalierungsgruppenmodell derart aktualisieren, dass die Eigenschaft provisionAfterExtensions einbezogen wird, die ein Array von Erweiterungsnamen akzeptiert. Die im Eigenschaftsarraywert erwähnten Erweiterungen müssen im Skalierungsgruppenmodell vollständig definiert werden.

### <a name="template-deployment"></a>Bereitstellung mit Vorlagen
Das folgende Beispiel definiert eine Vorlage, in der die Skalierungsgruppe über drei Erweiterungen verfügt – ExtensionA, ExtensionB und ExtensionC – sodass Erweiterungen in dieser Reihenfolge bereitgestellt werden:
-   ExtensionA -> ExtensionB -> ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Da die Eigenschaft „provisionAfterExtensions“ ein Array von Erweiterungsnamen akzeptiert, kann das obige Beispiel derart geändert werden, dass ExtensionC nach ExtensionA und ExtensionB bereitgestellt wird, aber die Reihenfolge von ExtensionA und ExtensionB gleichgültig ist. Die folgende Vorlage kann zum Umsetzen dieses Szenarios verwendet werden:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST-API
Im folgenden Beispiel wird eine neue Erweiterung mit dem Namen ExtensionC einem Skalierungsgruppenmodell hinzugefügt. ExtensionC ist von ExtensionA und ExtensionB abhängig, die bereits im Skalierungsgruppenmodell definiert wurden.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Wenn ExtensionC zuvor im Skalierungsgruppenmodell definiert wurde, und Sie jetzt ihre Abhängigkeiten hinzufügen möchten, können Sie einen `PATCH` ausführen, um die bereits bereitgestellten Eigenschaften der Erweiterung zu bearbeiten.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
Änderungen an vorhandenen Skalierungsgruppeninstanzen werden auf das nächste [Aktualisieren von virtuellen Computern auf das aktuelle Skalierungsgruppenmodell](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) angewendet.

### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)-Cmdlet, um die Application Health-Erweiterung der Skalierungsgruppenmodell-Definition hinzuzufügen. Erweiterungssequenzierung setzt die Verwendung von Azure PowerShell 1.2.0 oder höher voraus.

Im folgenden Beispiel wird die [Application Health-Erweiterung](virtual-machine-scale-sets-health-extension.md) dem `extensionProfile` im Skalierungsgruppenmodell einer Windows-basierten Skalierungsgruppe hinzugefügt. Die Application Health-Erweiterung wird nach der Bereitstellung der [CustomScript-Erweiterung für Windows](../virtual-machines/extensions/custom-script-windows.md) bereitgestellt, die bereits in der Skalierungsgruppe definiert ist.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az vmss extension set](/cli/azure/vmss/extension#az-vmss-extension-set), um die Application Health-Erweiterung der Skalierungsgruppen-Modelldefinition hinzuzufügen. Erweiterungssequenzierung setzt die Verwendung von Azure CLI 2.0.55 oder höher voraus.

Im folgenden Beispiel wird die [Application Health-Erweiterung](virtual-machine-scale-sets-health-extension.md) dem Skalierungsgruppenmodell einer Windows-basierten Skalierungsgruppe hinzugefügt. Die Application Health-Erweiterung wird nach der Bereitstellung der [CustomScript-Erweiterung für Windows](../virtual-machines/extensions/custom-script-windows.md) bereitgestellt, die bereits in der Skalierungsgruppe definiert ist.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Problembehandlung

### <a name="not-able-to-add-extension-with-dependencies"></a>Sie können keine Erweiterung mit Abhängigkeiten hinzufügen?
1. Stellen Sie sicher, dass die in provisionAfterExtensions angegebenen Erweiterungen im Skalierungsgruppenmodell definiert sind.
2. Stellen Sie sicher, dass keine zirkulären Abhängigkeiten eingeführt werden. Beispielsweise ist die folgende Sequenz nicht zulässig: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Stellen Sie sicher, dass alle Erweiterungen, von denen Abhängigkeiten bestehen sollen, über eine Eigenschaft „settings“ unter „properties“ verfügen. Wenn z.B. ExtensionB nach ExtensionA bereitgestellt werden muss, dann muss unter „properties“ für ExtensionA das Feld „settings“ vorhanden sein. Sie können eine leere „settings“-Eigenschaft angeben, wenn die Erweiterung keine erforderlichen Einstellungen voraussetzt.

### <a name="not-able-to-remove-extensions"></a>Sie können keine Erweiterungen entfernen?
Stellen Sie sicher, dass die Erweiterungen, die entfernt werden, nicht unter provisionAfterExtensions für andere Erweiterungen aufgeführt werden.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich über das [Bereitstellen Ihrer Anwendung](virtual-machine-scale-sets-deploy-app.md) in VM-Skalierungsgruppen.
