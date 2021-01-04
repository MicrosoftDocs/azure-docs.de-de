---
title: Verwenden der Azure Policy, um die Installation der VM-Erweiterung einzuschränken (Windows)
description: Verwenden Sie Azure Policy, um Erweiterungsbereitstellungen einzuschränken.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 558b23b4a89fd161b9e97265f6e1ed82624e8254
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955598"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Verwenden von Azure Policy, um die Installation von Erweiterungen auf virtuellen Windows-Computern einzuschränken

Wenn Sie die Verwendung oder Installation bestimmter Erweiterungen auf Ihren Windows-VMs verhindern möchten, können Sie mit PowerShell eine Azure Policy-Definition zum Einschränken von Erweiterungen für VMs innerhalb einer Ressourcengruppe erstellen. 

Dieses Tutorial verwendet Azure PowerShell innerhalb der Cloud Shell, die ständig auf die neueste Version aktualisiert wird. 

 

## <a name="create-a-rules-file"></a>Erstellen einer Regeldatei

Um einzuschränken, welche Erweiterungen installiert werden können, benötigen Sie eine [Regel](../../governance/policy/concepts/definition-structure.md#policy-rule), um die Logik zum Identifizieren der Erweiterung bereitzustellen.

Dieses Beispiel zeigt Ihnen, wie Sie Erweiterungen ablehnen, die von „Microsoft.Compute“ veröffentlicht wurden, indem Sie eine Regeldatei in Azure Cloud Shell erstellen, aber wenn Sie lokal in PowerShell arbeiten, können Sie auch eine lokale Datei erstellen und den Pfad ($home/clouddrive) durch den Pfad zu der lokalen Datei auf Ihrem Computer ersetzen.

Geben Sie in einer [Cloud Shell](https://shell.azure.com/powershell) ein:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in die Datei ein.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wenn Sie fertig sind, drücken die **STRG + O** und dann die **EINGABETASTE** zum Speichern der Datei. Drücken Sie **STRG + X**, um die Datei zu schließen und zu beenden.

## <a name="create-a-parameters-file"></a>Erstellen einer Parameterdatei

Sie benötigen auch eine [Parameterdatei](../../governance/policy/concepts/definition-structure.md#parameters), die für Sie eine Struktur für die Übergabe einer Liste der zu blockierenden Erweiterungen erstellt. 

Dieses Beispiel zeigt Ihnen, wie Sie eine Parameterdatei für VMs in Cloud Shell erstellen, aber wenn Sie lokal in PowerShell arbeiten, können Sie auch eine lokale Datei erstellen und den Pfad ($home/clouddrive) durch den Pfad zu der lokalen Datei auf Ihrem Computer ersetzen.

Geben Sie in [Cloud Shell](https://shell.azure.com/powershell) ein:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in die Datei ein.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

Wenn Sie fertig sind, drücken die **STRG + O** und dann die **EINGABETASTE** zum Speichern der Datei. Drücken Sie **STRG + X**, um die Datei zu schließen und zu beenden.

## <a name="create-the-policy"></a>Erstellen der Richtlinie

Eine Richtliniendefinition ist ein Objekt, mit dem die Konfiguration gespeichert wird, die Sie verwenden möchten. Die Richtliniendefinition verwendet die Regeln und Parameterdateien zum Definieren der Richtlinie. Erstellen Sie eine Richtliniendefinition mithilfe des Cmdlets [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

 Die Richtlinienregeln und Parameter sind die Dateien, die Sie erstellt und als JSON-Dateien in Ihrer Cloud Shell gespeichert haben.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Zuweisen der Richtlinie

In diesem Beispiel wird die Richtlinie mit [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment) einer Ressourcengruppe zugewiesen. Alle in der Ressourcengruppe **myResourceGroup** erstellten virtuellen Computer werden nicht in der Lage sein, die VM Access Agent- oder Custom Script-Erweiterungen zu installieren. 

Rufen Sie mit dem Cmdlet [Get-AzSubscription | Format-Table](/powershell/module/az.accounts/get-azsubscription) Ihre Abonnement-ID ab, die Sie anstelle der im Beispiel angegebenen ID verwenden.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testen der Richtlinie

Versuchen Sie, die VM Access-Erweiterung zu verwenden, um die Richtlinie zu testen. Bei Folgendem sollte ein Fehler mit der Meldung „Set-AzVMAccessExtension: Die Ressource ‚myVMAccess‘ wurde durch eine Richtlinie abgelehnt“ auftreten.

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

Im Portal sollte bei der Änderung des Kennworts ein Fehler auftreten mit der „Fehler bei der Vorlagenbereitstellung aufgrund einer Richtlinienverletzung.“- Meldung.

## <a name="remove-the-assignment"></a>Entfernen der Zuweisung

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Entfernen der Richtlinie

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).
