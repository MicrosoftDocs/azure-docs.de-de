---
title: Programmgesteuertes Erstellen von Richtlinien
description: In diesem Artikel wird Schritt für Schritt das programmgesteuerte Erstellen und Verwalten von Richtlinien für Azure Policy per Azure-Befehlszeilenschnittstelle, Azure PowerShell und REST-API beschrieben.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 86c1b60608780cba4d8ae27e3c82ace458f09a26
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920014"
---
# <a name="programmatically-create-policies"></a>Programmgesteuertes Erstellen von Richtlinien

In diesem Artikel wird das programmgesteuerte Erstellen und Verwalten von Richtlinien Schritt für Schritt beschrieben. Mit Azure Policy-Definitionen werden verschiedene Regeln und Auswirkungen für Ihre Ressourcen erzwungen. Durch die Erzwingung wird sichergestellt, dass die Ressourcen stets konform mit Ihren Unternehmensstandards und Vereinbarungen zum Servicelevel bleiben.

Weitere Informationen zur Konformität finden Sie unter [Abrufen von Konformitätsdaten](get-compliance-data.md).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass die folgenden Voraussetzungen erfüllt sind:

1. Installieren Sie den [ARMClient](https://github.com/projectkudu/ARMClient), falls Sie dies noch nicht durchgeführt haben. Mit diesem Tool werden HTTP-Anforderungen an Azure Resource Manager-basierte APIs gesendet.

1. Aktualisieren Sie Ihr Azure PowerShell-Modul auf die neueste Version. Ausführliche Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps). Weitere Informationen zur aktuellen Version finden Sie unter [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Registrieren Sie den Azure-Ressourcenanbieter „Policy Insights“ über Azure PowerShell, um zu prüfen, ob Ihr Abonnement mit dem Ressourcenanbieter funktioniert. Um einen Ressourcenanbieter zu registrieren, benötigen Sie die Berechtigungen zum Ausführen des Vorgangs „Aktion registrieren“ für den Ressourcenanbieter. Dieser Vorgang ist in den Rollen „Mitwirkender“ und „Besitzer“ enthalten. Führen Sie den folgenden Befehl aus, um den Ressourcenanbieter zu registrieren:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Weitere Informationen zum Registrieren und Anzeigen von Ressourcenanbietern finden Sie unter [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md).

1. Installieren Sie die Azure CLI, falls Sie dies noch nicht getan haben. Sie finden die aktuelle Version unter [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Erstellen und Zuweisen einer Richtliniendefinition

Im ersten Schritt zur besseren Sichtbarkeit Ihrer Ressourcen werden Richtlinien für die Ressourcen erstellt und zugewiesen. Im nächsten Schritt erfahren Sie, wie Sie eine Richtlinie programmgesteuert erstellen und zuweisen. Mit der Beispielrichtlinie werden Speicherkonten überprüft, die für alle öffentlichen Netzwerke offen sind, indem PowerShell, Azure CLI und HTTP-Anforderungen verwendet werden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Erstellen und Zuweisen einer Richtliniendefinition mit PowerShell

1. Verwenden Sie den folgenden JSON-Codeausschnitt, um eine JSON-Datei mit dem Namen „AuditStorageAccounts.json“ zu erstellen.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Weitere Informationen zum Erstellen einer Richtliniendefinition finden Sie unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).

1. Führen Sie den folgenden Befehl aus, um mit der Datei „AuditStorageAccounts.json“ eine Richtliniendefinition zu erstellen.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Mit dem Befehl wird die Richtliniendefinition _Audit Storage Accounts Open to Public Networks_ erstellt.
   Weitere Informationen zu anderen Parametern, die Sie verwenden können, finden Sie unter [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   `New-AzPolicyDefinition` speichert die Richtliniendefinition standardmäßig im ausgewählten Abonnement des Sitzungskontexts bei Aufruf ohne Speicherortparameter. Um die Definition an einem anderen Speicherort zu speichern, verwenden Sie die folgenden Parameter:

   - **SubscriptionId** zum Speichern in einem anderen Abonnement. Dies erfordert einen _GUID_-Wert.
   - **ManagementGroupName** zum Speichern in einer Verwaltungsgruppe. Dies erfordert einen _Zeichenfolgenwert_.

1. Nach dem Erstellen der Richtliniendefinition können Sie eine Richtlinienzuweisung erstellen. Führen Sie dazu die folgenden Befehle aus:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Ersetzen Sie _ContosoRG_ durch den Namen Ihrer gewünschten Ressourcengruppe.

   Der Parameter **Scope** für `New-AzPolicyAssignment` kann mit einer Verwaltungsgruppe, einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource verwendet werden. Der Parameter verwendet einen vollständigen Ressourcenpfad, den die Eigenschaft **ResourceId** in `Get-AzResourceGroup` zurückgibt. Das Muster für **Scope** sieht für jeden Container wie folgt aus. Ersetzen Sie `{rName}`, `{rgName}`, `{subId}` und `{mgName}` durch Ihre(n) Ressourcennamen, Ressourcengruppennamen, Abonnement-ID bzw. Namen der Verwaltungsgruppe.
   `{rType}` wird durch den **Ressourcentyp** der Ressource ersetzt, z. B. `Microsoft.Compute/virtualMachines` für eine VM.

   - Ressource: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Ressourcengruppe: `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnement: `/subscriptions/{subId}`
   - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{mgName}`

Weitere Informationen zum Verwalten von Ressourcenrichtlinien unter Verwendung des Resource Manager-PowerShell-Moduls finden Sie unter [Az.Resources](/powershell/module/az.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Erstellen und Zuweisen einer Richtliniendefinition per ARMClient

Verwenden Sie das folgende Verfahren, um eine Richtliniendefinition zu erstellen.

1. Kopieren Sie den folgenden JSON-Codeausschnitt, um eine JSON-Datei zu erstellen. Sie rufen die Datei im nächsten Schritt auf.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Erstellen Sie die Richtliniendefinition mithilfe eines der folgenden Aufrufe:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Ersetzen Sie „{subscriptionId}“ durch die ID Ihres Abonnements oder „{managementGroupId}“ durch die ID Ihrer [Verwaltungsgruppe](../../management-groups/overview.md).

   Weitere Informationen zur Struktur der Abfrage finden Sie unter [Policy Definitions – Create Or Update](/rest/api/resources/policydefinitions/createorupdate) (Richtliniendefinitionen: Erstellen oder Aktualisieren) sowie unter [Policy Definitions – Create Or Update At Management Group](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) (Richtliniendefinitionen: Erstellen oder Aktualisieren in einer Verwaltungsgruppe).

Verwenden Sie das folgende Verfahren, um eine Richtlinienzuweisung zu erstellen und die Richtliniendefinition auf Ressourcengruppenebene zuzuweisen.

1. Kopieren Sie den folgenden JSON-Codeausschnitt, um eine Datei mit einer JSON-Richtlinienzuweisung zu erstellen. Ersetzen Sie die in &lt;&gt; gesetzten Angaben durch Ihre eigenen Werte.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Erstellen Sie die Richtlinienzuweisung, indem Sie den folgenden Aufruf verwenden:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Ersetzen Sie die in &lt;&gt; gesetzten Angaben durch Ihre eigenen Werte.

   Weitere Informationen zur Durchführung von HTTP-Aufrufen für die REST-API finden Sie unter [Azure-REST-API-Ressourcen](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Erstellen und Zuweisen einer Richtliniendefinition mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie das folgende Verfahren, um eine Richtliniendefinition zu erstellen:

1. Kopieren Sie den folgenden JSON-Codeausschnitt, um eine Datei mit einer JSON-Richtlinienzuweisung zu erstellen.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Weitere Informationen zum Erstellen einer Richtliniendefinition finden Sie unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).

1. Führen Sie den folgenden Befehl aus, um eine Richtliniendefinition zu erstellen:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Mit dem Befehl wird die Richtliniendefinition _Audit Storage Accounts Open to Public Networks_ erstellt.
   Weitere Informationen zu anderen Parametern, die Sie verwenden können, finden Sie unter [az policy definition create](/cli/azure/policy/definition#az_policy_definition_create).

   `az policy definition creation` speichert die Richtliniendefinition standardmäßig im ausgewählten Abonnement des Sitzungskontexts bei Aufruf ohne Speicherortparameter. Um die Definition an einem anderen Speicherort zu speichern, verwenden Sie die folgenden Parameter:

   - **subscription** zum Speichern in einem anderen Abonnement. Dieser Parameter erfordert einen _GUID-Wert_ für die Abonnement-ID oder einen _Zeichenfolgenwert_ für den Namen des Abonnements.
   - **management-group** zum Speichern in einer Verwaltungsgruppe. Dies erfordert einen _Zeichenfolgenwert_.

1. Verwenden Sie den folgenden Befehl, um eine Richtlinienzuweisung zu erstellen. Ersetzen Sie die in &lt;&gt; gesetzten Angaben durch Ihre eigenen Werte.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   Der Parameter **scope** für `az policy assignment create` kann mit einer Verwaltungsgruppe, einem Abonnement, einer Ressourcengruppe oder einer einzelnen Ressource verwendet werden. Der Parameter verwendet einen vollständigen Ressourcenpfad. Das Muster für **scope** für die einzelnen Container ist unten aufgeführt. Ersetzen Sie `{rName}`, `{rgName}`, `{subId}` und `{mgName}` durch Ihre(n) Ressourcennamen, Ressourcengruppennamen, Abonnement-ID bzw. Namen der Verwaltungsgruppe. `{rType}` wird durch den **Ressourcentyp** der Ressource ersetzt, z. B. `Microsoft.Compute/virtualMachines` für eine VM.

   - Ressource: `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Ressourcengruppe: `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Abonnement: `/subscriptions/{subID}`
   - Verwaltungsgruppe: `/providers/Microsoft.Management/managementGroups/{mgName}`

Sie können die ID der Azure Policy-Definition abrufen, indem Sie PowerShell mit dem folgenden Befehl verwenden:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Die ID für die Richtliniendefinition, die Sie erstellt haben, sollte in etwa wie im folgenden Beispiel aussehen:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Weitere Informationen zum Verwalten von Ressourcenrichtlinien mit der Azure-Befehlszeilenschnittstelle finden Sie unter [Azure CLI-Ressourcenrichtlinien](/cli/azure/policy).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den in diesem Artikel verwendeten Befehlen und Abfragen finden Sie in den folgenden Artikeln.

- [Azure-REST-API-Ressourcen](/rest/api/resources/)
- [Azure PowerShell-Module](/powershell/module/az.resources/#policies)
- [Befehle für Azure CLI-Richtlinien](/cli/azure/policy)
- [Azure-Ressourcenanbieter „Policy Insights“ – REST-API-Referenz](/rest/api/policy-insights)
- [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md)
