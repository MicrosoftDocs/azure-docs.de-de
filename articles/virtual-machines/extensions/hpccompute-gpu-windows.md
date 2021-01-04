---
title: NVIDIA-GPU-Treibererweiterung – Azure-Windows-VMs
description: Microsoft Azure-Erweiterung für die Installation von NVIDIA-GPU-Treibern auf Compute-VMs der N-Serie unter Windows
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: 82ed1d57d72a4479005d8bfd2234c124a97096ee
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965985"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>NVIDIA-GPU-Treibererweiterung für Windows

## <a name="overview"></a>Übersicht

Diese Erweiterung installiert NVIDIA-GPU-Treiber auf Windows-VMs der N-Serie. Je nach VM-Familie installiert die Erweiterung CUDA- oder GRID-Treiber. Bei der Installation von NVIDIA Treibern mit dieser Erweiterung akzeptieren Sie die Bedingungen des [NVIDIA-Endbenutzer-Lizenzvertrags](https://go.microsoft.com/fwlink/?linkid=874330) und stimmen diesen zu. Während der Installation wird der virtuelle Computer möglicherweise neu gestartet, um die Treibereinrichtung abzuschließen.

Anweisungen zur manuellen Installation der Treiber und der aktuellen unterstützten Versionen sind [hier](../windows/n-series-driver-setup.md) verfügbar.
Es ist auch eine Erweiterung zum Installieren von NVIDIA-GPU-Treibern auf [Linux-VMs der N-Serie](hpccompute-gpu-linux.md) verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="operating-system"></a>Betriebssystem

Diese Erweiterung unterstützt die folgenden Betriebssysteme:

| Distribution | Version |
|---|---|
| Windows 10 | Core |
| Windows Server 2016 | Core |
| Windows Server 2012 R2 | Core |

### <a name="internet-connectivity"></a>Internetkonnektivität

Die Microsoft Azure-Erweiterung für NVIDIA-GPU-Treiber erfordert, dass der virtuelle Zielcomputer mit dem Internet verbunden ist und Zugriff hat.

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
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
| type | NvidiaGpuDriverWindows | Zeichenfolge |
| typeHandlerVersion | 1.3 | INT |


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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.3",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.3 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name NvidiaGpuDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.3 \
  --settings '{ \
  }'
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

Die Ausgabe der Erweiterungsausführung wird im folgenden Verzeichnis protokolliert:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\
```

### <a name="error-codes"></a>Fehlercodes

| Fehlercode | Bedeutung | Mögliche Aktion |
| :---: | --- | --- |
| 0 | Vorgang erfolgreich |
| 1 | Vorgang erfolgreich. Es ist ein Neustart erforderlich. |
| 100 | Der Vorgang wird nicht unterstützt oder konnte nicht abgeschlossen werden. | Mögliche Ursachen: PowerShell-Version nicht unterstützt, VM-Größe ist keine VM der N-Serie, Fehler beim Herunterladen von Daten. Überprüfen Sie die Protokolldateien, um die Ursache des Fehlers zu ermitteln. |
| 240, 840 | Timeout bei Vorgang. | Wiederholen Sie den Vorgang. |
| -1 | Ausnahme. | Überprüfen Sie die Protokolldateien, um die Ursache der Ausnahme zu ermitteln. |
| -5x | Vorgang aufgrund ausstehenden Neustarts unterbrochen. | Starten Sie die VM neu. Die Installation wird nach dem Neustart fortgesetzt. Eine Deinstallation sollte manuell aufgerufen werden. |


### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/community/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer für Windows](features-windows.md).

Weitere Informationen zu virtuellen Computern der N-Serie finden Sie unter [Für GPU optimierte VM-Größen](../sizes-gpu.md).
