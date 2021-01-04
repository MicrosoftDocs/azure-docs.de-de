---
title: InfiniBand-Treibererweiterung – Azure-Linux-VMs
description: Microsoft Azure-Erweiterung für die Installation von InfiniBand-Treibern auf Compute-VMs der H- und N-Serie unter Linux.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 2a5aa8983e6cbb0745e05ce275edeadeccb60736
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966036"
---
# <a name="infiniband-driver-extension-for-linux"></a>InfiniBand-Treibererweiterung für Linux

Diese Erweiterung installiert InfiniBand-OFED-Treiber auf VMs der [H-Serie](../sizes-hpc.md) und [N-Serie](../sizes-gpu.md), auf denen InfiniBand und SR-IOV (r-Größen) aktiviert ist und Linux ausgeführt wird. Je nach VM-Familie installiert die Erweiterung die entsprechenden Treiber für die Connect-X-NIC.

Anweisungen zur manuellen Installation der OFED-Treiber sind [hier](../workloads/hpc/enable-infiniband.md#manual-installation) verfügbar.

Außerdem ist eine Erweiterung zur Installation der InfiniBand-Treiber für [Windows-VMs](hpc-compute-infiniband-windows.md) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Diese Erweiterung unterstützt die folgenden Betriebssystem-Distributionen, abhängig von der Treiberunterstützung für bestimmte BS-Versionen.

| Distribution | Version |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS, 20.04 LTS |
| Linux: CentOS | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |
| Linux: Red Hat Enterprise Linux | 7.4, 7.5, 7.6, 7.7, 8.1, 8,2 |

### <a name="internet-connectivity"></a>Internetkonnektivität

Die Microsoft Azure-Erweiterung für InfiniBand-Treiber erfordert, dass der virtuelle Zielcomputer mit dem Internet verbunden ist und Zugriff hat.

## <a name="extension-schema"></a>Erweiterungsschema

Der folgende JSON-Code zeigt das Schema für die Erweiterung.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Eigenschaften

| Name | Wert/Beispiel | Datentyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | Zeichenfolge |
| type | InfiniBandDriverLinux | Zeichenfolge |
| typeHandlerVersion | 1.1 | INT |



## <a name="deployment"></a>Bereitstellung


### <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage 

Azure-VM-Erweiterungen können mithilfe von Azure Resource Manager-Vorlagen bereitgestellt werden. Vorlagen sind ideal, wenn Sie virtuelle Computer bereitstellen, die nach der Bereitstellung konfiguriert werden müssen.

Die JSON-Konfiguration für eine VM-Erweiterung kann innerhalb der VM-Ressource geschachtelt oder im Stamm bzw. auf der obersten Ebene einer Resource Manager-JSON-Vorlage platziert werden. Die Platzierung der JSON-Konfiguration wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen). 

Im folgenden Beispiel wird davon ausgegangen, dass die Erweiterung in der VM-Ressource geschachtelt ist. Beim Schachteln der Ressource für die Erweiterung wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Hinzufügen einer Erweiterung zu einer VM-Skalierungsgruppe

Durch folgenden Befehl wird die neueste Version (1.1) der Erweiterung „InfiniBandDriverLinux“ auf allen RDMA-fähigen VMs in einer vorhandenen VM-Skalierungsgruppe mit dem Namen *myVMSS* installiert, die in der Ressourcengruppe mit dem Namen *myResourceGroup* bereitgestellt wurde:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können über das Azure-Portal und mithilfe von Azure PowerShell und der Azure-Befehlszeilenschnittstelle abgerufen werden. Führen Sie den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Die Ausgabe der Erweiterungsausführung wird in der folgenden Datei protokolliert. In dieser Datei finden Sie Informationen zum Nachverfolgen des Status von Installationen sowie zur Problembehandlung.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Exitcodes

Die folgende Tabelle beschreibt die Bedeutung und empfohlene Aktionen basierend auf Exitcodes des Installationsprozesses für die Erweiterung.

| Exitcode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 0 | Vorgang erfolgreich |
| 1 | Falsche Verwendung der Erweiterung | Protokoll zur Ausführungsausgabe überprüfen |
| 10 | Linux Integration Services für Hyper-V und Azure nicht verfügbar oder nicht installiert | Ispci-Ausgabe überprüfen |
| 11 | Mellanox InfiniBand not found on this VM size (Mellanox InfiniBand konnte auf dieser VM-Größe nicht gefunden werden) | Verwenden Sie eine [VM-Größe und ein Betriebssystem](../sizes-hpc.md), die unterstützt werden. |
| 12 | Imageangebot nicht unterstützt |
| 13 | VM-Größe nicht unterstützt | Use an InfiniBand-enabled ('r' size) [H-series](../sizes-hpc.md) and [N-series](../sizes-gpu.md)N-series VM to deploy (Verwenden Sie für die Bereitstellung eine VM der „H-Serie“ und „N-Serie“ mit InfiniBand-Aktivierung (r-Größe)). |
| 14 | Vorgang nicht erfolgreich | Protokoll zur Ausführungsausgabe überprüfen |


### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ können Sie auf der [Website des Azure-Supports](https://azure.microsoft.com/support/options/) einen Supportfall erstellen. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu VMs mit InfiniBand-Aktivierung (r-Größen) finden Sie unter [H-Serie](../sizes-hpc.md) und [N-Serie](../sizes-gpu.md).

> [!div class="nextstepaction"]
> [Informationen zu Erweiterungen und Features für virtuelle Computer für Linux](features-linux.md)