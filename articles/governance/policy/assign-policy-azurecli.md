---
title: 'Schnellstart: Neue Richtlinienzuweisung mit der Azure CLI'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe der Azure CLI eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9955f911f9a92d7b353a8f3d022af7884b5a6aae
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090137"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit Azure CLI

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden.

Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung _nicht konform_.

Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung wird die Azure-Befehlszeilenschnittstelle verwendet, um eine Richtlinienzuweisung zu erstellen und nicht konforme Ressourcen in Ihrer Azure-Umgebung zu identifizieren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Für diesen Schnellstart muss mindestens Version 2.0.76 der Azure CLI ausgeführt werden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

- Registrieren Sie den Ressourcenanbieter Azure Policy Insights mithilfe der Azure-Befehlszeilenschnittstelle. Durch die Registrierung des Ressourcenanbieters wird sichergestellt, dass das Abonnement mit ihm verwendet werden kann. Um einen Ressourcenanbieter zu registrieren, benötigen Sie die Berechtigung zum Registrieren von Ressourcenanbietern. Dieser Vorgang ist in den Rollen „Mitwirkender“ und „Besitzer“ enthalten. Führen Sie den folgenden Befehl aus, um den Ressourcenanbieter zu registrieren:

  ```azurecli-interactive
  az provider register --namespace 'Microsoft.PolicyInsights'
  ```

  Weitere Informationen zum Registrieren und Anzeigen von Ressourcenanbietern finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).

- Installieren Sie den [ARMClient](https://github.com/projectkudu/ARMClient), falls Sie dies noch nicht durchgeführt haben. Mit diesem Tool werden HTTP-Anforderungen an Azure Resource Manager-basierte APIs gesendet.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

Führen Sie den folgenden Befehl aus, um eine Richtlinienzuweisung zu erstellen:

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

In dem Befehl werden folgende Informationen verwendet:

- **Name:** Der tatsächliche Name der Zuweisung. In diesem Beispiel wurde _audit-vm-manageddisks_ verwendet.
- **DisplayName:** Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_.
- **Richtlinie:** Die Richtliniendefinitions-ID, auf deren Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_. Führen Sie den folgenden Befehl aus, um die Richtliniendefinitions-ID abzurufen: `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **Bereich:** Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Er kann von einem Abonnement bis zu Ressourcengruppen reichen. Ersetzen Sie &lt;scope&gt; durch den Namen Ihrer Ressourcengruppe.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Führen Sie zum Anzeigen der Ressourcen, die unter dieser Zuordnung nicht konform sind, die folgenden Befehle aus, um die Richtlinienzuweisungs-ID abzurufen:

```azurecli-interactive
az policy assignment list --query "[?displayName=='Audit VMs without managed disks Assignment'].id"
```

Weitere Informationen zu Richtlinienzuweisungs-IDs finden Sie unter [az policy assignment](/cli/azure/policy/assignment).

Führen Sie als Nächstes den folgenden Befehl aus, um die Ressourcen-IDs der nicht konformen Ressourcen abzurufen, die in einer JSON-Datei ausgegeben werden:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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
