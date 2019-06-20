---
title: Erstellen einer Richtlinie für nicht konforme Ressourcen mit der Azure-Befehlszeilenschnittstelle
description: Erstellen Sie mithilfe von Azure CLI eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 76f457b7d577fb2a08bbcc386328ce4d8e59f902
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979615"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit Azure CLI

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden.

Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung *nicht konform*.

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung wird die Azure-Befehlszeilenschnittstelle verwendet, um eine Richtlinienzuweisung zu erstellen und nicht konforme Ressourcen in Ihrer Azure-Umgebung zu identifizieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Für diese Schnellstartanleitung ist es erforderlich, Version 2.0.4 oder höher der Azure CLI auszuführen, um die CLI lokal zu installieren und zu verwenden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Voraussetzungen

Registrieren Sie den Ressourcenanbieter Azure Policy Insights mithilfe der Azure-Befehlszeilenschnittstelle. Durch die Registrierung des Ressourcenanbieters wird sichergestellt, dass das Abonnement mit ihm verwendet werden kann. Um einen Ressourcenanbieter zu registrieren, benötigen Sie die Berechtigung zum Registrieren von Ressourcenanbietern. Dieser Vorgang ist in den Rollen „Mitwirkender“ und „Besitzer“ enthalten. Führen Sie den folgenden Befehl aus, um den Ressourcenanbieter zu registrieren:

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

Weitere Informationen zum Registrieren und Anzeigen von Ressourcenanbietern finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/resource-manager-supported-services.md).

Installieren Sie den [ARMClient](https://github.com/projectkudu/ARMClient), falls Sie dies noch nicht durchgeführt haben. Mit diesem Tool werden HTTP-Anforderungen an Azure Resource Manager-basierte APIs gesendet.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

Führen Sie den folgenden Befehl aus, um eine Richtlinienzuweisung zu erstellen:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

In dem Befehl werden folgende Informationen verwendet:

- **Name:** Der tatsächliche Name der Zuweisung. In diesem Beispiel wurde *audit-vm-manageddisks* verwendet.
- **DisplayName:** Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall *Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger*.
- **Richtlinie:** Die Richtliniendefinitions-ID, auf deren Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition *Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden*. Führen Sie den folgenden Befehl aus, um die Richtliniendefinitions-ID abzurufen: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Bereich:** Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Er kann von einem Abonnement bis zu Ressourcengruppen reichen. Ersetzen Sie &lt;scope&gt; durch den Namen Ihrer Ressourcengruppe.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Führen Sie zum Anzeigen der Ressourcen, die unter dieser Zuordnung nicht konform sind, die folgenden Befehle aus, um die Richtlinienzuweisungs-ID abzurufen:

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

Weitere Informationen zu Richtlinienzuweisungs-IDs finden Sie unter [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment).

Führen Sie als Nächstes den folgenden Befehl aus, um die Ressourcen-IDs der nicht konformen Ressourcen abzurufen, die in einer JSON-Datei ausgegeben werden:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Ihre Ergebnisse sollten in etwa wie im folgenden Beispiel aussehen:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Die Ergebnisse sind vergleichbar mit dem, was üblicherweise in der Azure-Portalansicht unter **Nicht konforme Ressourcen** zu sehen ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen:

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)