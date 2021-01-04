---
title: Ändern einer Azure-VM-Skalierungsgruppe
description: Erfahren Sie, wie Sie eine Azure-VM-Skalierungsgruppe mit den REST-APIs, Azure PowerShell und der Azure CLI ändern und aktualisieren.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 7577c8510746d1140c1f8b70081f600d992ae512
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016674"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Ändern einer VM-Skalierungsgruppe

Während des Lebenszyklus von Anwendungen müssen Sie möglicherweise Ihre VM-Skalierungsgruppe ändern oder aktualisieren. Zu diesen Aktualisierungen können das Aktualisieren der Konfiguration der Skalierungsgruppe oder das Ändern der Anwendungskonfiguration zählen. Dieser Artikel beschreibt die Vorgehensweise zum Ändern einer vorhandenen Skalierungsgruppe mit den REST-APIs, Azure PowerShell oder der Azure CLI.

## <a name="fundamental-concepts"></a>Grundlegende Konzepte

### <a name="the-scale-set-model"></a>Skalierungsgruppenmodell
Eine Skalierungsgruppe enthält ein Skalierungsgruppenmodell, das den *gewünschten* Zustand der gesamten Skalierungsgruppe erfasst. Zum Abfragen des Modells für eine Skalierungsgruppe können Sie Folgendes verwenden: 

- REST-API mit [compute/virtualmachinescalesets/get](/rest/api/compute/virtualmachinescalesets/get) wie folgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Azure PowerShell mit [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- Azure CLI mit [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Sie können auch [resources.azure.com](https://resources.azure.com) oder die sprachspezifischen [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

Die genaue Darstellung der Ausgabe hängt von den für den Befehl angegebenen Optionen ab. Das folgende Beispiel zeigt eine verkürzte Beispielausgabe von der Azure CLI:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Diese Eigenschaften gelten für die gesamte Skalierungsgruppe.


### <a name="the-scale-set-instance-view"></a>Instanzenansicht für die Skalierungsgruppe
Eine Skalierungsgruppe verfügt außerdem über eine Skalierungsgruppen-Instanzenansicht, die den aktuellen *Laufzeitstatus* der gesamten Skalierungsgruppe erfasst. Zum Abfragen der Instanzenansicht für eine Skalierungsgruppe können Sie Folgendes verwenden:

- REST-API mit [compute/virtualmachinescalesets/getinstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) wie folgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell mit [Get-AzVmss](/powershell/module/az.compute/get-azvmss):

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- Azure CLI mit [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Sie können auch [resources.azure.com](https://resources.azure.com) oder die sprachspezifischen [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

Die genaue Darstellung der Ausgabe hängt von den für den Befehl angegebenen Optionen ab. Das folgende Beispiel zeigt eine verkürzte Beispielausgabe von der Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Diese Eigenschaften bieten eine Übersicht über den aktuellen Laufzeitstatus der virtuellen Computer in der Skalierungsgruppe, etwa den Status der auf die Skalierungsgruppe angewendeten Erweiterungen.


### <a name="the-scale-set-vm-model-view"></a>VM-Modellansicht der Skalierungsgruppe
Nicht nur die Skalierungsgruppe verfügt über eine Modellansicht, sondern auch jede in der Skalierungsgruppe enthaltene VM-Instanz. Zum Abfragen der Modellansicht für eine bestimmte VM-Instanz in einer Skalierungsgruppe können Sie Folgendes verwenden:

- REST-API mit [compute/virtualmachinescalesetvms/get](/rest/api/compute/virtualmachinescalesetvms/get) wie folgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- Azure PowerShell mit [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI mit [az vmss show](/cli/azure/vmss):

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Sie können auch [resources.azure.com](https://resources.azure.com) oder die [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

Die genaue Darstellung der Ausgabe hängt von den für den Befehl angegebenen Optionen ab. Das folgende Beispiel zeigt eine verkürzte Beispielausgabe von der Azure CLI:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Diese Eigenschaften beschreiben die Konfiguration einer VM-Instanz in einer Skalierungsgruppe, nicht die Konfiguration der Skalierungsgruppe als Ganzes. Beispielsweise weist das Skalierungsgruppenmodell die Eigenschaft `overprovision` auf, das Modell für eine VM-Instanz in einer Skalierungsgruppe besitzt diese Eigenschaft jedoch nicht. Dieser Unterschied ist darauf zurückzuführen, dass die Überbereitstellung eine Eigenschaft für die Skalierungsgruppe als Ganzes und nicht für einzelne VM-Instanzen in der Skalierungsgruppe ist. (Weitere Informationen zur Überbereitstellung finden Sie unter [Überlegungen zum Entwurf von Skalierungsgruppen](virtual-machine-scale-sets-design-overview.md#overprovisioning).)


### <a name="the-scale-set-vm-instance-view"></a>VM-Instanzenansicht für die Skalierungsgruppe
Nicht nur die Skalierungsgruppe verfügt über eine Instanzenansicht, sondern auch jede in der Skalierungsgruppe enthaltene VM-Instanz. Zum Abfragen der Instanzenansicht für eine bestimmte VM-Instanz in einer Skalierungsgruppe können Sie Folgendes verwenden:

- REST-API mit [compute/virtualmachinescalesetvms/getinstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) wie folgt:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- Azure PowerShell mit [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm):

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- Azure CLI mit [az vmss get-instance-view](/cli/azure/vmss):

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Sie können auch [resources.azure.com](https://resources.azure.com) oder die [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

Die genaue Darstellung der Ausgabe hängt von den für den Befehl angegebenen Optionen ab. Das folgende Beispiel zeigt eine verkürzte Beispielausgabe von der Azure CLI:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Diese Eigenschaften beschreiben den aktuellen Laufzeitstatus einer VM-Instanz in einer Skalierungsgruppe, einschließlich aller auf die Skalierungsgruppe angewandten Erweiterungen.


## <a name="how-to-update-global-scale-set-properties"></a>Aktualisieren von globalen Skalierungsgruppeneigenschaften
Wenn Sie eine globale Skalierungsgruppeneigenschaft aktualisieren möchten, müssen Sie die Eigenschaft im Skalierungsgruppenmodell aktualisieren. Für die Aktualisierung können Sie Folgendes verwenden:

- REST-API mit [compute/virtualmachinescalesets/createorupdate](/rest/api/compute/virtualmachinescalesets/createorupdate) wie folgt:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Sie können mit den Eigenschaften aus der REST-API eine Azure Resource Manager-Vorlage bereitstellen, um globale Skalierungsgruppeneigenschaften zu aktualisieren.

- Azure PowerShell mit [Update-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- Azure CLI mit [az vmss update](/cli/azure/vmss):
    - So ändern Sie eine Eigenschaft

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - So fügen Sie einer Listeneigenschaft in einer Skalierungsgruppe ein Objekt hinzu 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - So entfernen Sie ein Objekt aus einer Listeneigenschaft in einer Skalierungsgruppe 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Falls Sie die Skalierungsgruppe zuvor mit dem Befehl `az vmss create` bereitgestellt haben, können Sie den Befehl `az vmss create` erneut ausführen, um die Skalierungsgruppe zu aktualisieren. Stellen Sie sicher, dass alle Eigenschaften im Befehl `az vmss create` mit Ausnahme der zu ändernden den vorherigen Eigenschaften entsprechen.

- Sie können auch [resources.azure.com](https://resources.azure.com) oder die [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

Nach der Aktualisierung des Skalierungsgruppenmodells gilt die neue Konfiguration für alle in der Skalierungsgruppe erstellten neuen virtuellen Computer. Die Modelle für die vorhandenen virtuellen Computer in der Skalierungsgruppe müssen aber dennoch auf das aktuelle Skalierungsgruppenmodell aktualisiert werden. Die Modelle der einzelnen virtuellen Computer enthalten eine boolesche Eigenschaften namens `latestModelApplied`, die angibt, ob ein virtueller Computer auf das aktuelle Skalierungsgruppenmodell aktualisiert wurde. (`true` bedeutet, dass der virtuelle Computer auf das aktuelle Modell aktualisiert wurde.)


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Aktualisieren von virtuellen Computer auf das aktuelle Skalierungsgruppenmodell
Für Skalierungsgruppen gilt eine „Upgraderichtlinie“, die festlegt, wie virtuelle Computer auf das aktuelle Skalierungsgruppenmodell aktualisiert werden. Die drei Modi für die Upgraderichtlinie lauten wie folgt:

- **Automatisch:** In diesem Modus gibt die Skalierungsgruppe keine Garantie in Bezug auf die Reihenfolge, in der virtuelle Computer heruntergefahren werden. Die Skalierungsgruppe fährt unter Umständen alle virtuellen Computer gleichzeitig herunter. 
- **Parallel:** In diesem Modus führt die Skalierungsgruppe das Update in Batches mit optionaler Pause zwischen den Batches aus.
- **Manuell:** In diesem Modus geschieht beim Aktualisieren des Skalierungsgruppenmodells nichts mit den vorhandenen virtuellen Computern.
 
Um vorhandene virtuelle Computer zu aktualisieren, müssen Sie für jeden virtuellen Computer ein manuelles Upgrade ausführen. Sie können das manuelle Upgrade über folgende Komponenten ausführen:

- REST-API mit [compute/virtualmachinescalesets/updateinstances](/rest/api/compute/virtualmachinescalesets/updateinstances) wie folgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- Azure PowerShell mit [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance):
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- Azure CLI mit [az vmss update-instances](/cli/azure/vmss):

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Sie können auch die sprachspezifischen [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.

>[!NOTE]
> Für Service Fabric-Cluster kann nur der Modus *Automatisch* verwendet werden, das Update wird jedoch anders gehandhabt. Weitere Informationen finden Sie unter [Service Fabric-Anwendungsupgrades](../service-fabric/service-fabric-application-upgrade.md).

Eine der Änderungen, die an globalen Skalierungsgruppeneigenschaften vorgenommen werden, entspricht nicht der Upgraderichtlinie. Änderungen am Betriebssystem- und Datenträgerprofil von Skalierungsgruppen (z. B. Administratorbenutzername und -kennwort) können nur in API-Versionen ab *2017-12-01* geändert werden. Diese Änderungen gelten nur für virtuelle Computer, die nach der Änderung im Skalierungsgruppenmodell erstellt wurden. Um vorhandene virtuelle Computer zu aktualisieren, müssen Sie ein Reimaging aller vorhandenen virtuellen Computer durchführen. Für dieses Reimaging können Sie Folgendes verwenden:

- REST-API mit [compute/virtualmachinescalesets/reimage](/rest/api/compute/virtualmachinescalesets/reimage) wie folgt:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- Azure PowerShell mit [Set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm):

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- Azure CLI mit [az vmss reimage](/cli/azure/vmss):

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Sie können auch die sprachspezifischen [Azure-SDKs](https://azure.microsoft.com/downloads/) verwenden.


## <a name="properties-with-restrictions-on-modification"></a>Eigenschaften mit Einschränkungen bei der Änderung

### <a name="create-time-properties"></a>Eigenschaften zur Erstellungszeit
Einige Eigenschaften können nur beim Erstellen der Skalierungsgruppe festgelegt werden. Zu diesen Eigenschaften zählen folgende:

- Verfügbarkeitszonen
- Imagereferenzherausgeber
- Imagereferenzangebot
- Speicherkontotyp des verwalteten Betriebssystemdatenträgers

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Eigenschaften, die nur auf Grundlage des aktuellen Werts geändert werden können
Einige Eigenschaften können mit Ausnahmen je nach aktuellem Wert geändert werden. Zu diesen Eigenschaften zählen folgende:

- **singlePlacementGroup:** Wenn für singlePlacementGroup die Option „true“ festgelegt ist, kann diese in „false“ geändert werden. Wenn für „singlePlacementGroup“ jedoch „false“ festgelegt ist, kann die Einstellung **nicht** in „true“ geändert werden.
- **Subnetz:** Das Subnetz einer Skalierungsgruppe kann geändert werden, solange sich das ursprüngliche und das neue Subnetz im selben virtuellen Netzwerk befinden.

### <a name="properties-that-require-deallocation-to-change"></a>Eigenschaften, für die zur Änderung die Aufhebung der Zuordnung erforderlich ist
Einige Eigenschaften können nur in bestimmte Werte geändert werden, wenn die Zuordnung der virtuellen Computer in der Skalierungsgruppe aufgehoben wird. Zu diesen Eigenschaften zählen folgende:

- **SKU-Name:** Wird die neue VM-SKU auf der Hardware, auf der sich die Skalierungsgruppe derzeit befindet, nicht unterstützt, müssen Sie die Zuordnung der virtuellen Computer in der Skalierungsgruppe aufheben, bevor Sie den SKU-Namen ändern. Weitere Informationen finden Sie unter [Ändern der Größe einer Azure-VM](../virtual-machines/windows/resize-vm.md).


## <a name="vm-specific-updates"></a>VM-spezifische Updates
Bestimmte Änderungen können statt auf die globalen Skalierungsgruppeneigenschaften auf spezifische virtuelle Computer angewendet werden. Derzeit wird als VM-spezifische Aktualisierung nur das Anfügen von Datenträgern an virtuelle Computer in der Skalierungsgruppe bzw. das Trennen dieser Datenträger unterstützt. Dieses Feature befindet sich in der Vorschauphase. Weitere Informationen finden Sie in der [Vorschaudokumentation](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Szenarien

### <a name="application-updates"></a>Anwendungsupdates
Wenn eine Anwendung über Erweiterungen in einer Skalierungsgruppe bereitgestellt wird, löst eine Aktualisierung der Erweiterungskonfiguration eine Aktualisierung der Anwendung gemäß der Upgraderichtlinie aus. Wenn Sie beispielsweise über eine neue Version eines Skripts für die Ausführung in einer benutzerdefinierten Skripterweiterung verfügen, können Sie die *fileUris*-Eigenschaft aktualisieren, sodass sie auf das neue Skript verweist. In einigen Fällen möchten Sie jedoch eine Aktualisierung erzwingen, auch wenn die Erweiterungskonfiguration unverändert bleibt, z.B. wenn Sie das Skript aktualisiert haben, ohne den URI des Skripts zu ändern. In diesen Fällen können Sie *forceUpdateTag* ändern, um eine Aktualisierung zu erzwingen. Diese Eigenschaft wird von der Azure-Plattform nicht interpretiert. Eine Änderung des Werts hat keine Auswirkung auf die Ausführung der Erweiterung. Durch eine Änderung wird lediglich eine erneute Ausführung der Erweiterung erzwungen. Weitere Informationen zu *forceUpdateTag* finden Sie in der [REST-API-Dokumentation für Erweiterungen](/rest/api/compute/virtualmachineextensions/createorupdate). Beachten Sie, dass *forceUpdateTag* mit allen Erweiterungen verwendet werden kann, nicht nur mit der benutzerdefinierten Skripterweiterung.

Anwendungen werden häufig auch über ein benutzerdefiniertes Image bereitgestellt. Dieses Szenario wird im folgenden Abschnitt behandelt.

### <a name="os-updates"></a>Betriebssystemupdates
Bei Verwendung von Azure-Plattformimages können Sie das Image durch Ändern von *imageReference* aktualisieren. (Weitere Informationen finden Sie in der [REST-API-Dokumentation](/rest/api/compute/virtualmachinescalesets/createorupdate).)

>[!NOTE]
> Bei Plattformimages wird als Imagereferenzversion häufig „aktuelle Version“ angegeben. Beim Erstellen, Aufskalieren und Durchführen eines Reimagings werden die virtuellen Computer mit der neuesten verfügbaren Version erstellt. Das bedeutet jedoch **nicht**, dass das Betriebssystemimage im Lauf der Zeit automatisch aktualisiert wird, wenn neue Imageversionen veröffentlicht werden. Es befindet sich derzeit ein separates Feature in der Vorschau, das automatische Betriebssystemupgrades bereitstellt. Weitere Informationen finden Sie in der [Dokumentation zu automatischen Betriebssystemupgrades](virtual-machine-scale-sets-automatic-upgrade.md).

Bei Verwendung von benutzerdefinierten Images können Sie das Image durch Aktualisieren der *imageReference*-ID aktualisieren. (Weitere Informationen finden Sie in der [REST-API-Dokumentation](/rest/api/compute/virtualmachinescalesets/createorupdate).)

## <a name="examples"></a>Beispiele

### <a name="update-the-os-image-for-your-scale-set"></a>Aktualisieren des Betriebssystemimages für Ihre Skalierungsgruppe
Möglicherweise verfügen Sie über eine Skalierungsgruppe mit einer alten Version von Ubuntu LTS 16.04. Sie möchten auf eine neuere Version von Ubuntu LTS 16.04 aktualisieren, z.B. Version *16.04.201801090*. Die Eigenschaft für die Imagereferenzversion ist nicht Teil einer Liste, daher können Sie diese Eigenschaften direkt mit einem der folgenden Befehle ändern:

- Azure PowerShell mit [Update-AzVmss](/powershell/module/az.compute/update-azvmss) wie folgt:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- Azure CLI mit [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternativ können Sie auch das Image ändern, das Ihre Skalierungsgruppe verwendet. So lässt sich beispielsweise ein benutzerdefiniertes Image aktualisieren oder ändern, das Ihre Skalierungsgruppe verwendet. Aktualisieren Sie dafür die Eigenschaft der Imagereferenz-ID. Die Eigenschaft der Imagereferenz-ID gehört zu keiner Liste, sodass Sie sie direkt mit einem der folgenden Befehle ändern können:

- Azure PowerShell mit [Update-AzVmss](/powershell/module/az.compute/update-azvmss) wie folgt:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- Azure CLI mit [az vmss update](/cli/azure/vmss):

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Aktualisieren des Lastenausgleichs für Ihre Skalierungsgruppe
Angenommen, Sie besitzen eine Skalierungsgruppe mit Azure Load Balancer und möchten Azure Load Balancer durch Azure Application Gateway ersetzen. Die Eigenschaften des Lastenausgleichsmoduls und von Application Gateway für eine Skalierungsgruppe sind Teil einer Liste, daher können Sie die Befehle zum Entfernen und Hinzufügen von Listenelementen verwenden, statt die Eigenschaften direkt zu ändern:

- Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLI:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Bei diesen Befehlen wird davon ausgegangen, dass für die Skalierungsgruppe nur eine IP-Konfiguration und ein Lastenausgleichsmodul festgelegt sind. Sind mehrere vorhanden, müssen Sie möglicherweise einen anderen Listenindex als *0* verwenden.


## <a name="next-steps"></a>Nächste Schritte
Sie können mit der [Azure CLI](virtual-machine-scale-sets-manage-cli.md) oder [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md) auch allgemeine Verwaltungsaufgaben für Skalierungsgruppen ausführen.
