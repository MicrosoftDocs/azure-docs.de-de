---
title: Angeben einer Ressourcengruppe für virtuelle Computer in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Ressourcengruppe für virtuelle Computer in einem Lab in Azure DevTest Labs angeben.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476082"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Angeben einer Ressourcengruppe für virtuelle Labcomputer in Azure DevTest Labs

Als Labbesitzer können Sie Ihre Lab-VMs so konfigurieren, dass sie in einer bestimmten Ressourcengruppe erstellt werden. Dieses Feature ist in folgenden Szenarien hilfreich:

- Es werden weniger Ressourcengruppen von Labs in Ihrem Abonnement erstellt.
- Die Labs werden in einem festen Satz von Ressourcengruppen ausgeführt, die Sie konfigurieren.
- Einschränkungen und Genehmigungen, die zum Erstellen von Ressourcengruppen in Ihrem Azure-Abonnement erforderlich sind, werden umgangen.
- Alle Lab-Ressourcen werden in einer einzelnen Ressourcengruppe konsolidiert, damit Sie diese Ressourcen leichter nachverfolgen und [Richtlinien](../governance/policy/overview.md) zur Verwaltung auf Ressourcengruppenebene anwenden können.

Mit dieser Funktion können Sie mithilfe eines Skripts eine neue oder vorhandene Ressourcengruppe in Ihrem Azure-Abonnement für all Ihre virtuellen Lab-Computer angeben. Azure DevTest Labs unterstützt diese Funktion derzeit über eine API.

> [!NOTE]
> Es gelten alle Abonnementeinschränkungen, wenn Sie Labs in DevTest Labs erstellen. Stellen Sie sich ein Lab so vor wie jede andere Ressource in Ihrem Abonnement. Im Fall von Ressourcengruppen beträgt die Einschränkung [980 Ressourcengruppen pro Abonnement](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). 

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals
Führen Sie die folgenden Schritte aus, um eine Ressourcengruppe für alle im Lab erstellten virtuellen Computer anzugeben. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Navigationsmenü die Option **Alle Dienste** aus. 
3. Wählen Sie in der Liste **DevTest Labs** aus.
4. Wählen Sie in der Liste mit den Labs Ihr **Lab** aus.  
5. Wählen Sie im Abschnitt **Einstellungen** die Option **Konfiguration und Richtlinien** aus. 
6. Wählen Sie im linken Menü **Lab-Einstellungen** aus. 
7. Wählen Sie **Alle virtuellen Computer in einer Ressourcengruppe** aus. 
8. Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus, oder wählen Sie die Option **Neu erstellen** aus, geben Sie einen **Namen** für die Ressourcengruppe ein, und wählen Sie **OK** aus. 

    ![Auswählen der Ressourcengruppe für alle im Lab erstellten virtuellen Computer](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Verwenden von PowerShell 
Das folgende Beispiel beschreibt, wie alle virtuellen Lab-Computer mithilfe eines PowerShell-Skripts in einer neuen Ressourcengruppe erstellt werden.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Rufen Sie das Skript mit dem folgenden Befehl auf. ResourceGroup.ps1 ist die Datei mit dem vorherigen Skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Verwenden einer Azure Resource Manager-Vorlage
Wenn Sie das Lab mit einer Azure Resource Manager-Vorlage erstellen, verwenden Sie die Eigenschaft **vmCreationResourceGroupId** im Abschnitt mit den Lab-Eigenschaften der Vorlage, wie im folgenden Beispiel gezeigt:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>API zum Konfigurieren einer Ressourcengruppe für virtuelle Lab-Computer
Als Lab-Besitzer haben Sie bei Verwendung dieser API die folgenden Optionen:

- Wahlen Sie die **Ressourcengruppe des Labs** für alle virtuellen Computer aus.
- Wählen Sie eine andere **vorhandene Ressourcengruppe** als die Ressourcengruppe des Labs für alle virtuellen Computer aus.
- Geben Sie den Namen einer **neuen Ressourcengruppe** für alle virtuellen Computer ein.
- Sie können das bestehende Verhalten übernehmen, bei dem für jeden virtuellen Computer im Lab eine Ressourcengruppe erstellt wird.
 
Diese Einstellung gilt für neue virtuelle Computer, die im Lab erstellt werden. Die älteren virtuellen Computer in Ihrem Lab, die in eigenen Ressourcengruppen erstellt wurden, sind davon nicht betroffen. In Ihrem Lab erstellte Umgebungen bleiben weiterhin in ihren eigenen Ressourcengruppen.

Verwenden dieser API:
- Verwenden Sie die API-Version **2018_10_15_preview**.
- Wenn Sie eine neue Ressourcengruppe angeben, stellen Sie sicher, dass Sie über **Schreibberechtigungen für Ressourcengruppen** in Ihrem Abonnement verfügen. Ohne Schreibberechtigungen schlägt das Erstellen neuer virtueller Computer in der angegebenen Ressourcengruppe fehl.
- Übergeben Sie bei Verwendung der API die **vollständige Ressourcengruppen-ID**. Beispiel: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Stellen Sie sicher, dass sich die Ressourcengruppe im selben Abonnement wie das Lab befindet. 


## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln: 

- [Festlegen von Richtlinien für ein Lab](devtest-lab-get-started-with-lab-policies.md)
- [Häufig gestellte Fragen](devtest-lab-faq.md)
