---
title: 'Schnellstart: Neue Richtlinienzuweisung mit Terraform'
description: In dieser Schnellstartanleitung erstellen Sie mit Terraform und HCL-Syntax eine Richtlinienzuweisung zum Identifizieren von nicht konformen Ressourcen.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93104908"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mithilfe von Terraform

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln.
Diese Schnellstartanleitung führt Sie schrittweise durch die Erstellung einer Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden.

Am Ende dieses Prozesses können Sie erfolgreich virtuelle Computer identifizieren, die keine verwalteten Datenträger verwenden. Sie sind mit der Richtlinienzuweisung _nicht konform_.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- In Ihrer Umgebung muss mindestens Version 0.12.0 von [Terraform](https://www.terraform.io/) konfiguriert sein.
  Anweisungen finden Sie unter [Konfigurieren von Terraform mit Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Für diesen Schnellstart muss mindestens Version 2.13.0 der Azure CLI ausgeführt werden. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Erstellen der Terraform-Konfiguration, -Variablen und -Ausgabedatei

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

Konfigurieren Sie zunächst die Konfiguration, die Variable und die Ausgabedateien in Terraform. Für die Terraform-Ressourcen für Azure Policy wird der [Azure-Anbieter](https://www.terraform.io/docs/providers/azurerm/index.html) genutzt.

1. Erstellen Sie einen neuen Ordner mit dem Namen `policy-assignment`, und navigieren Sie zu diesem Verzeichnis.

1. Erstellen Sie `main.tf` mit dem folgenden Code:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Erstellen Sie `variables.tf` mit dem folgenden Code:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Ein solcher Bereich kann eine Verwaltungsgruppe oder auch nur eine einzelne Ressource sein. Ersetzen Sie `{scope}` unbedingt durch eins der folgenden Muster:

   - Abonnement: `/subscriptions/{subscriptionId}`
   - Ressourcengruppe: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Ressource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Erstellen Sie `output.tf` mit dem folgenden Code:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Initialisieren von Terraform und Erstellen eines Plans

Initialisieren Sie anschließend Terraform, um die erforderlichen Anbieter herunterzuladen, und erstellen Sie dann einen Plan.

1. Führen Sie den Befehl [terraform init](https://www.terraform.io/docs/commands/init.html) aus. Mit diesem Befehl werden die Azure-Module heruntergeladen, die zum Erstellen der Azure-Ressourcen in der Terraform-Konfiguration erforderlich sind.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Screenshot, der die Ausführung des Befehls „terraform init“ sowie das Herunterladen des AzureRM-Moduls und eine Erfolgsmeldung zeigt":::

1. Authentifizieren Sie sich mit der [Azure CLI](/cli/azure/) für Terraform. Weitere Informationen finden Sie unter [Azure-Anbieter: Authentifizieren mithilfe der Azure CLI](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Erstellen Sie den Ausführungsplan mit dem Befehl [terraform plan](https://www.terraform.io/docs/commands/plan.html) und dem Parameter **out**.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Screenshot: Ausführen des Befehls „terraform plan“ und des Parameters „out“, um die Azure-Ressource zu zeigen, die erstellt wird":::

   > [!NOTE]
   > Weitere Informationen zum Beibehalten von Ausführungsplänen und zur Sicherheit finden Sie unter [Terraform-Plan: Sicherheitswarnung](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Anwenden des Terraform-Ausführungsplans

Wenden Sie zum Schluss den Ausführungsplan an.

Führen Sie den Befehl [terraform apply](https://www.terraform.io/docs/commands/apply.html) aus, und geben Sie das bereits erstellte Element `assignment.tfplan` an.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Screenshot: Ausführen des Befehls „terraform apply“ und resultierende Ressourcenerstellung":::

Die Richtlinienzuweisung wird mit der Meldung „Apply complete! Resources: 1 added, 0 changed, 0 destroyed.“ (Anwendung abgeschlossen: Ressourcen: 1 hinzugefügt, 0 geändert, 0 zerstört) erstellt. Da Sie die Datei `outputs.tf` definiert haben, wird außerdem _assignment\_id_ zurückgegeben.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Wenn Sie die nicht konformen Ressourcen unter dieser neuen Zuweisung anzeigen möchten, verwenden Sie die von `terraform apply` zurückgegebene Zuweisungs-ID ( _assignment\_id_ ). Führen Sie damit den folgenden Befehl aus, um die Ressourcen-IDs der nicht konformen Ressourcen abzurufen, die in einer JSON-Datei ausgegeben werden:

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

Verwenden Sie zum Entfernen der erstellten Zuweisung die Azure CLI, oder kehren Sie den Terraform-Ausführungsplan mit `terraform destroy` um.

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
