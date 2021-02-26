---
title: 'Schnellstart: Neue Richtlinienzuweisung mit der REST-API'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe der REST-API eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: 438d8004cd50e6e2ef7586c51adc63257f37978b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219976"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit der REST-API

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden.

Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung _nicht konform_.

Die REST-API wird zum Erstellen und Verwalten von Azure-Ressourcen verwendet. In dieser Anleitung wird die REST-API verwendet, um eine Richtlinienzuweisung zu erstellen und nicht konforme Ressourcen in Ihrer Azure-Umgebung zu identifizieren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Installieren Sie [ARMClient](https://github.com/projectkudu/ARMClient), falls Sie dies noch nicht getan haben. Mit diesem Tool werden HTTP-Anforderungen an Azure Resource Manager-basierte REST-APIs gesendet. Sie können auch die Funktion „Jetzt ausprobieren“ in der REST-Dokumentation oder Tools wie [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) von PowerShell oder [Postman](https://www.postman.com) verwenden.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

Führen Sie den folgenden Befehl aus, um eine Richtlinienzuweisung zu erstellen:

   - REST-API-URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - Anforderungstext

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
         "nonComplianceMessages": [
             {
                 "message": "Virtual machines should use a managed disk"
             }
         ]
       }
     }
     ```

Der Endpunkt und der Anforderungstext oben verwenden die folgenden Informationen:

REST-API-URI:
- **Bereich:** Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Ein solcher Bereich kann eine Verwaltungsgruppe oder auch nur eine einzelne Ressource sein. Ersetzen Sie `{scope}` unbedingt durch eins der folgenden Muster:
  - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Ressourcengruppe: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Ressource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **Name:** Der tatsächliche Name der Zuweisung. In diesem Beispiel wurde _audit-vm-manageddisks_ verwendet.

Anforderungstext:
- **DisplayName:** Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_.
- **Description**: Eine ausführlichere Erläuterung des Richtlinienzwecks oder des Grunds, warum sie diesem Bereich zugewiesen ist
- **policyDefinitionId**: Die Richtliniendefinitions-ID, auf deren Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_.
- **nonComplianceMessages**: Dient zum Festlegen der Meldung für den Fall, dass für eine Ressource der Zugriff verweigert wird, weil sie nicht konform ist bzw. als nicht konform bewertet wird. Weitere Informationen finden Sie unter [Azure Policy-Zuweisungsstruktur: Meldungen bei Complianceverstößen](./concepts/assignment-structure.md#non-compliance-messages).

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Wenn Sie die nicht konformen Ressourcen unter dieser neuen Zuweisung anzeigen möchten, führen Sie den folgenden Befehl aus, um die Ressourcen-IDs der nicht konformen Ressourcen abzurufen, die in einer JSON-Datei ausgegeben werden:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
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
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

Die Ergebnisse sind vergleichbar mit dem, was üblicherweise in der Azure-Portalansicht unter **Nicht konforme Ressourcen** zu sehen ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen:

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

Ersetzen Sie `{scope}` durch den Bereich, den Sie bei der ersten Erstellung der Richtlinienzuweisung verwendet haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
