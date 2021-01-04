---
title: 'Schnellstart: Neue Richtlinienzuweisung mit Python'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von Python eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: aa67b3a2bae2beb62daa6e64871411ee760f802b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832195"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mithilfe von Python

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln. Im Rahmen dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden. Im Anschluss identifizieren Sie virtuelle Computer, die _nicht konform_ sind.

Die Python-Bibliothek dient zum Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In dieser Anleitung erfahren Sie, wie Sie mithilfe der Python-Bibliothek eine Richtlinienzuweisung erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>Hinzufügen der Policy-Bibliothek

Damit Python mit Azure Policy verwendet werden kann, muss die Bibliothek hinzugefügt werden. Diese Bibliothek funktioniert überall dort, wo Python verwendet werden kann, dies schließt [Bash unter Windows 10](/windows/wsl/install-win10) oder eine lokale Installation ein.

1. Überprüfen Sie, ob die aktuelle Python-Version (mindestens **3.8**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Python.org](https://www.python.org/downloads/) herunter.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.5.1**) installiert ist. Falls sie noch nicht installiert ist, lesen Sie den Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI ist erforderlich, um Python für die Verwendung der **CLI-basierten Authentifizierung** in den folgenden Beispielen zu aktivieren. Informationen zu anderen Optionen finden Sie unter [Authentifizieren mit Azure-Verwaltungsbibliotheken für Python](/azure/developer/python/azure-sdk-authenticate).

1. Führen Sie die Authentifizierung über Azure CLI aus.

   ```azurecli
   az login
   ```

1. Installieren Sie in der Python-Umgebung Ihrer Wahl die erforderlichen Bibliotheken für Azure Resource Graph:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Wenn Python für alle Benutzer installiert ist, muss dieser Befehl in einer Konsole mit erhöhten Rechten ausgeführt werden.

1. Überprüfen Sie, ob die Bibliotheken installiert wurden. `azure-mgmt-policyinsights` sollte mindestens **0.5.0** sein, `azure-mgmt-resource` sollte mindestens **9.0.0** sein, und `azure-cli-core` sollte mindestens **2.5.0** sein.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung und weisen die Definition **Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden** (`06a78e20-9358-41c9-923c-fb736d382a4d`) zu. Mit dieser Richtliniendefinition werden Ressourcen identifiziert, die die in der Richtliniendefinition festgelegten Bedingungen nicht erfüllen.

Führen Sie den folgenden Code aus, um eine neue Richtlinienzuweisung zu erstellen:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

In diesen Befehlen werden folgende Informationen verwendet:

Zuweisungsdetails:
- **display_name**: Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall _Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger_.
- **policy_definition_id**: Der Pfad der Richtliniendefinition, auf dessen Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition _Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden_. In diesem Beispiel ist die Richtliniendefinition integriert, und der Pfad enthält keine Verwaltungsgruppen- oder Abonnementinformationen.
- **scope**: Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Ein solcher Bereich kann eine Verwaltungsgruppe oder auch nur eine einzelne Ressource sein. Ersetzen Sie `{scope}` unbedingt durch eins der folgenden Muster:
  - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Ressourcengruppe: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Ressource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description**: Eine ausführlichere Erläuterung des Richtlinienzwecks oder des Grunds, warum sie diesem Bereich zugewiesen ist

Zuweisungserstellung:

- Bereich: Dieser Bereich bestimmt, wo die Richtlinienzuweisung gespeichert wird. Der in den Zuweisungsdetails festgelegte Bereich muss in diesem Bereich liegen.
- Name: Der tatsächliche Name der Zuweisung. In diesem Beispiel wurde _audit-vm-manageddisks_ verwendet.
- Richtlinienzuweisung: Das Python-Objekt **PolicyAssignment**, das im vorherigen Schritt erstellt wurde

Sie können nun nicht konforme Ressourcen identifizieren, um den Konformitätszustand Ihrer Umgebung nachzuvollziehen.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Verwenden Sie folgende Informationen, um Ressourcen zu identifizieren, die mit der erstellten Richtlinienzuweisung nicht konform sind. Führen Sie den folgenden Code aus:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Ersetzen Sie `{subscriptionId}` durch das Abonnement, für das Sie die Konformitätsergebnisse für diese Richtlinienzuweisung anzeigen möchten. Eine Liste anderer Bereiche und Möglichkeiten zum Zusammenfassen der Daten finden Sie im Artikel zu [Richtlinienstatusmethoden](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods).

Ihre Ergebnisse sollten in etwa wie im folgenden Beispiel aussehen:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

Die Ergebnisse entsprechen dem, was im Azure-Portal auf der Registerkarte **Ressourcenkonformität** einer Richtlinienzuweisung angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Ersetzen Sie `{scope}` durch den gleichen Bereich, den Sie auch für die Erstellung der Richtlinienzuweisung verwendet haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtliniendefinitionen, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)
