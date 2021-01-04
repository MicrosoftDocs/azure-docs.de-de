---
title: Azure Monitor Dependency-VM-Erweiterung für Windows
description: Stellen Sie den Azure Monitor Dependency-Agent auf einem virtuellen Windows-Computer bereit, indem Sie eine VM-Erweiterung verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82edc70befb7fce95869b238d26c9154ec999c7b
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966835"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure Monitor Dependency-VM-Erweiterung für Windows

Das Zuordnungsfeature in Azure Monitor für VMs erhält seine Daten vom Microsoft Dependency-Agent. Die Azure VM Dependency-Agent-VM-Erweiterung für Windows wird von Microsoft veröffentlicht und unterstützt. Die Erweiterung installiert den Dependency-Agent auf virtuellen Azure-Computern. Dieses Dokument enthält ausführliche Informationen zu den unterstützten Plattformen, Konfigurationen und Bereitstellungsoptionen für die Azure Dependency-Agent-VM-Erweiterung für Windows.

## <a name="operating-system"></a>Betriebssystem

Die Azure VM Dependency-Agent-Erweiterung für Windows kann für die unterstützten Betriebssysteme ausgeführt werden, die im Abschnitt [Unterstützte Betriebssysteme](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) des Bereitstellungsartikels zu Azure Monitor für VMs aufgelistet sind.

## <a name="extension-schema"></a>Erweiterungsschema

Das folgende JSON zeigt das Schema für die Azure VM Dependency-Agent-Erweiterung auf einem virtuellen Azure Windows-Computer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Eigenschaftswerte

| Name | Beispiel/Wert |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Bereitstellung von Vorlagen

Sie können die Azure-VM-Erweiterungen mithilfe von Azure Resource Manager-Vorlagen bereitstellen. Sie können das im vorherigen Abschnitt erläuterte JSON-Schema in einer Azure Resource Manager-Vorlage verwenden, um die Azure VM Dependency-Agent-Erweiterung im Rahmen einer Azure Resource Manager-Bereitstellung auszuführen.

Der JSON-Code für eine VM-Erweiterung kann innerhalb der VM-Ressource geschachtelt werden. Alternativ können Sie den Code auf der Stamm- bzw. obersten Ebene einer Resource Manager-JSON-Vorlage platzieren. Die Platzierung des JSON-Codes wirkt sich auf den Wert von Name und Typ der Ressource aus. Weitere Informationen finden Sie unter [Set name and type for child resources](../../azure-resource-manager/templates/child-resource-name-type.md) (Festlegen von Name und Typ für untergeordnete Ressourcen).

Im folgenden Beispiel wird davon ausgegangen, dass die Dependency-Agent-Erweiterung in der VM-Ressource geschachtelt ist. Wenn Sie die Erweiterungsressource schachteln, wird der JSON-Code im `"resources": []`-Objekt des virtuellen Computers platziert.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Wenn Sie den JSON-Code für die Erweiterung auf der Stammebene der Vorlage platzieren, enthält der Ressourcenname einen Verweis auf den übergeordneten virtuellen Computer. Der Typ spiegelt die geschachtelte Konfiguration wider.

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-Bereitstellung

Sie können den Befehl `Set-AzVMExtension` verwenden, um die Dependency-Agent-VM-Erweiterung auf einem vorhandenen virtuellen Computer bereitzustellen. Bevor Sie den Befehl ausführen, müssen die öffentliche und die private Konfiguration in einer PowerShell-Hashtabelle gespeichert werden.

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Daten zum Status von Erweiterungsbereitstellungen können aus dem Azure-Portal und über das Azure PowerShell-Modul abgerufen werden. Führen Sie im Azure PowerShell-Modul den folgenden Befehl aus, um den Bereitstellungsstatus von Erweiterungen für einen bestimmten virtuellen Computer anzuzeigen:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Die Ausgabe der Erweiterungsausführung wird in Dateien im folgenden Verzeichnis protokolliert:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Sie können auch einen Azure-Supportfall erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten** aus. Informationen darüber, wie Azure-Support genutzt werden kann, finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/).
