---
title: Azure Disk Encryption und Erweiterungssequenzierung von Azure VM-Skalierungsgruppen
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Linux-IaaS-Computer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60611266"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Verwenden von Azure Disk Encryption mit Erweiterungssequenzierung der VM-Skalierungsgruppe

Erweiterungen wie Azure Disk Encryption können einer Azure VM-Skalierungsgruppe in einer angegebenen Reihenfolge hinzugefügt werden. Verwenden Sie zu diesem Zweck die [Erweiterungssequenzierung](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Im Allgemeinen sollte die Verschlüsselung auf einem Datenträger in folgenden Fällen angewendet werden:

- Nach Erweiterungen oder benutzerdefinierten Skripts zur Vorbereitung der Datenträger oder Volumes.
- Vor Erweiterungen oder benutzerdefinierten Skripts, die auf die Daten auf den verschlüsselten Datenträgern oder Volumes zugreifen oder sie nutzen.

In beiden Fällen legt die Eigenschaft `provisionAfterExtensions` fest, welche Erweiterung später in der Sequenz hinzugefügt werden soll.

## <a name="sample-azure-templates"></a>Azure-Beispielvorlagen

Wenn Azure Disk Encryption nach einer anderen Erweiterung angewendet werden soll, fügen Sie die Erweiterung `provisionAfterExtensions` im Block der AzureDiskEncryption-Erweiterung ein. 

Hier ist ein Beispiel zur Verwendung von „CustomScriptExtension“, einem PowerShell-Skript, das einen Windows-Datenträger initialisiert und formatiert, gefolgt von „AzureDiskEncryption“:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Wenn Azure Disk Encryption vor einer anderen Erweiterung angewendet werden soll, fügen Sie die Erweiterung `provisionAfterExtensions` im Block der darauf folgenden Erweiterung ein.

Hier ist ein Beispiel zur Verwendung von „AzureDiskEncryption“, gefolgt von „VMDiagnosticsSettings“, einer Erweiterung, die Überwachungs- und Diagnosefunktionen auf einem Windows-basierten virtuellen Azure-Computer bereitstellt:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Informationen zu umfassenderen Vorlagen finden Sie unter:
* Anwenden der Azure Disk Encryption-Erweiterung nach einem benutzerdefinierten Shell-Skript zum Formatieren des Datenträgers (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Anwenden der Azure Disk Encryption-Erweiterung nach einem benutzerdefinierten PowerShell-Skript zum Initialisieren und Formatieren des Datenträgers (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Anwenden der Azure Disk Encryption-Erweiterung vor einem benutzerdefinierten PowerShell-Skript zum Initialisieren und Formatieren des Datenträgers (Windows): [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Erweiterungssequenzierung: [Bereitstellen des Sequenzierens von Erweiterungen in VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Weitere Informationen zur Eigenschaft `provisionAfterExtensions`: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
