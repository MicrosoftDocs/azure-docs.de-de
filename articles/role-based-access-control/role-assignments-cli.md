---
title: Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle – Azure RBAC
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten mit der Azure CLI und der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) erteilen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 10060e0dc6595a4d59f3968fa324ca40c91a7a39
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033798"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] In diesem Artikel wird das Zuweisen von Rollen mithilfe der Azure-Befehlszeilenschnittstelle beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Rollenzuweisungen hinzufügen oder entfernen zu können:

- `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z.B. [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) oder [Besitzer](built-in-roles.md#owner)
- [Bash in der Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure-Befehlszeilenschnittstelle](/cli/azure)

## <a name="steps-to-add-a-role-assignment"></a>Schritte zum Hinzufügen einer Rollenzuweisung

In Azure RBAC fügen Sie zum Gewähren des Zugriffs eine Rollenzuweisung hinzu. Eine Rollenzuweisung besteht aus drei Elementen: Sicherheitsprinzipal, Rollendefinition und Bereich. Führen Sie die folgenden Schritte aus, um eine Rollenzuweisung hinzuzufügen.

### <a name="step-1-determine-who-needs-access"></a>Schritt 1: Ermitteln, wer Zugriff benötigt

Sie können eine Rolle einem Benutzer, einer Gruppe, einem Dienstprinzipal oder einer verwalteten Identität zuweisen. Sie müssen eventuell die eindeutige ID des Objekts angeben, um Rollenzuweisungen hinzufügen zu können. Die ID weist dieses Format auf: `11111111-1111-1111-1111-111111111111`. Sie können die ID über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle (Azure CLI) abrufen.

**Benutzer**

Bei einem Azure AD-Benutzer rufen Sie den Benutzerprinzipalnamen (z. B. *patlong\@contoso.com*) oder die Benutzerobjekt-ID ab. Zum Abrufen der Objekt-ID können Sie [az ad user show](/cli/azure/ad/user#az_ad_user_show) verwenden.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Gruppieren**

Bei einer Azure AD-Gruppe benötigen Sie die Gruppenobjekt-ID. Zum Abrufen der Objekt-ID können Sie [az ad group show](/cli/azure/ad/group#az_ad_group_show) oder [az ad group list](/cli/azure/ad/group#az_ad_group_list) verwenden.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Dienstprinzipal**

Bei einem Azure AD-Dienstprinzipal (einer von einer Anwendung verwendeten Identität) benötigen Sie die Objekt-ID des Dienstprinzipals. Zum Abrufen der Objekt-ID können Sie [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) verwenden. Verwenden Sie für einen Dienstprinzipal die Objekt-ID und **nicht** die Anwendungs-ID.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Verwaltete Identität**

Bei einer systemseitig oder benutzerseitig zugewiesenen verwalteten Identität benötigen Sie die Objekt-ID. Zum Abrufen der Objekt-ID können Sie [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) verwenden.

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Sie können auch [az identity list](/cli/azure/identity#az_identity_list) verwenden, um nur benutzerseitig zugewiesene verwaltete Identitäten aufzulisten.

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>Schritt 2: Suchen der gewünschten Rolle

Berechtigungen sind in Rollen zusammengefasst. Sie können aus einer Liste von verschiedenen [in Azure integrierten Rollen](built-in-roles.md) auswählen oder eigene benutzerdefinierte Rollen verwenden. Es hat sich bewährt, den Zugriff mit den minimal erforderlichen Berechtigungen zu gewähren. Daher sollten Sie keine allgemeine Rolle zuweisen.

Zum Auflisten der Rollen und Abrufen der eindeutigen Rollen-ID können Sie [az role definition list](/cli/azure/role/definition#az_role_definition_list) verwenden.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Im Folgenden wird gezeigt, wie Sie die Details einer bestimmten Rolle auflisten.

```azurecli
az role definition list --name "{roleName}"
```

Weitere Informationen finden Sie unter [Auflisten von Azure-Rollendefinitionen](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>Schritt 3: Ermitteln des erforderlichen Bereichs

Azure bietet vier Bereichsebenen: Ressource, [Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups), Abonnement und [Verwaltungsgruppe](../governance/management-groups/overview.md). Es hat sich bewährt, den Zugriff mit den minimal erforderlichen Berechtigungen zu gewähren. Daher sollten Sie Rollen nicht für einen allgemeinen Bereich zuweisen. Weitere Informationen zum Begriff „Bereich“ finden Sie unter [Grundlegendes zum Begriff „Bereich“](scope-overview.md).

**Ressourcenumfang**

Für den Ressourcenbereich benötigen Sie die Ressourcen-ID der Ressource. Die Ressourcen-ID finden Sie unter den Eigenschaften der Ressource im Azure-Portal. Ressourcen-IDs weisen folgendes Format auf:

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Ressourcengruppenbereich**

Für den Bereich der Ressourcengruppe benötigen Sie den Namen der Ressourcengruppe. Sie finden den Namen im Azure-Portal auf der Seite **Ressourcengruppen** oder mithilfe von [az group list](/cli/azure/group#az_group_list).

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Abonnementbereich** 

Für den Abonnementbereich benötigen Sie die Abonnement-ID. Sie finden die Abonnement-ID im Azure-Portal auf der Seite **Abonnements** oder durch Ausführen von [az account list](/cli/azure/account#az_account_list).

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Verwaltungsgruppenbereich** 

Für den Verwaltungsgruppenbereich benötigen Sie den Namen der Verwaltungsgruppe. Sie finden den Namen im Azure-Portal auf der Seite **Verwaltungsgruppen** oder durch Ausführen von [az account management-group list](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>Schritt 4: Rollenzuweisung hinzufügen

Verwenden Sie zum Hinzufügen einer Rollenzuweisung den Befehl [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Abhängig vom Bereich weist der Befehl in der Regel eines der folgenden Formate auf.

**Ressourcenumfang**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Ressourcengruppenbereich**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Abonnementbereich** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Verwaltungsgruppenbereich** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Im Folgenden finden Sie eine Beispielausgabe, wenn Sie einem Benutzer die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) in einem Ressourcengruppenbereich zuweisen.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>Beispiele für das Hinzufügen von Rollenzuweisungen

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Hinzufügen einer Rollenzuweisung für alle Blobcontainer in einem Speicherkonto-Ressourcenbereich

Weist einem Dienstprinzipal mit der Objekt-ID *55555555-5555-5555-5555-555555555555* die Rolle [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor) im Ressourcenbereich für das Speicherkonto *storage12345* zu.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Hinzufügen einer Rollenzuweisung für einen bestimmten Blobcontainer-Ressourcenbereich

Weist einem Dienstprinzipal mit der Objekt-ID *55555555-5555-5555-5555-555555555555* die Rolle [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor) im Ressourcenbereich für den Blobcontainer *blob-container-01* zu.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Hinzufügen einer Rollenzuweisung für eine Gruppe im Ressourcenbereich eines bestimmten virtuellen Netzwerks

Weist der Gruppe *Ann Mack Team* mit der ID „22222222-2222-2222-2222-222222222222“ die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) im Ressourcenbereich für das virtuelle Netzwerk *pharma-sales-project-network* zu.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer in einem Ressourcengruppenbereich

Weist dem Benutzer *patlong\@contoso.com* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) im Ressourcengruppenbereich *pharma-sales* zu.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer mithilfe der eindeutigen Rollen-ID in einem Ressourcengruppenbereich

In bestimmten Fällen kann sich ein Rollenname ändern, z. B.:

- Sie verwenden eine eigene benutzerdefinierte Rolle und beschließen, den Namen zu ändern.
- Sie verwenden eine als Vorschauversion verfügbare Rolle, deren Name den Zusatz **(Vorschauversion)** enthält. Beim Veröffentlichen wird die Rolle umbenannt.

Auch wenn eine Rolle umbenannt wird, ändert sich die Rollen-ID nicht. Wenn Sie Ihre Rollenzuweisungen mithilfe von Skripts oder mittels Automatisierung erstellen, wird empfohlen, die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, ist auf diese Weise die Wahrscheinlichkeit größer, dass Ihre Skripts funktionieren.

Im folgenden Beispiel wird dem Benutzer *patlong\@contoso.com* im Ressourcengruppenkontext *pharma-sales* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) zugewiesen.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für alle Blobcontainer in einem Ressourcengruppenbereich

Weist einem Dienstprinzipal mit der Objekt-ID *55555555-5555-5555-5555-555555555555* die Rolle [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor) im Ressourcengruppenbereich *Example-Storage-rg* zu.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternativ kann die vollqualifizierte Ressourcengruppe auch mithilfe des Parameters `--scope` angegeben werden:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für eine Anwendung in einem Ressourcengruppenbereich

Weist einer Anwendung mit der Objekt-ID „44444444-4444-4444-4444-444444444444“ des Dienstprinzipals die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) im Ressourcengruppenbereich *pharma-sales* zu.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen neuen Dienstprinzipal in einem Ressourcengruppenbereich

Wenn Sie einen neuen Dienstprinzipal erstellen und sofort versuchen, diesem eine Rolle zuzuweisen, kann die Rollenzuweisung in einigen Fällen fehlschlagen. Wenn Sie z.B. ein Skript verwenden, um eine neue verwaltete Identität zu erstellen, und dann versuchen, dem Dienstprinzipal eine Rolle zuzuweisen, kann die Rollenzuweisung fehlschlagen. Der Grund für diesen Fehler ist wahrscheinlich eine Replikationsverzögerung. Der Dienstprinzipal wird in einer Region erstellt, die Rollenzuweisung kann aber in einer anderen Region stattfinden, in die der Dienstprinzipal noch nicht repliziert wurde. Um dieses Szenario zu beheben, sollten Sie beim Erstellen der Rollenzuweisung den Prinzipaltyp angeben.

Verwenden Sie [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) zum Hinzufügen einer Rollenzuweisung, geben Sie einen Wert für `--assignee-object-id` an, und legen Sie `--assignee-principal-type` dann auf `ServicePrincipal` fest.

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

Im folgenden Beispiel wird der verwalteten Identität *msi-test* im Ressourcengruppenkontext *pharma-sales* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) zugewiesen:

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer im Abonnementbereich

Weist dem Benutzer *annm\@example.com* die Rolle [Leser](built-in-roles.md#reader) im Abonnementbereich zu.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>Hinzufügen einer Rollenzuweisung für eine Gruppe im Abonnementbereich

Weist der Gruppe *Ann Mack Team* mit der ID „22222222-2222-2222-2222-222222222222“ die Rolle [Leser](built-in-roles.md#reader) im Abonnementbereich zu.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>Hinzufügen einer Rollenzuweisung für alle Blobcontainer im Abonnementbereich

Weist dem Benutzer *alain\@example.com* die Rolle [Leser von Speicherblobdaten](built-in-roles.md#storage-blob-data-reader) im Abonnementbereich zu.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer in einem Verwaltungsgruppenbereich

Weist dem Benutzer *alain\@example.com* die Rolle [Abrechnungsleser](built-in-roles.md#billing-reader) im Verwaltungsgruppenbereich zu.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung

In Azure RBAC entfernen Sie mit [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete) eine Rollenzuweisung und heben damit den Zugriff auf.

Im folgenden Beispiel wird die Zuweisung der Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) von Benutzer *patlong\@contoso.com* für die Ressourcengruppe *pharma-sales* entfernt:

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Entfernt die Rolle [Leser](built-in-roles.md#reader) von der Gruppe *Ann Mack Team* mit der ID „22222222-2222-2222-2222-222222222222“ im Abonnementbereich.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Entfernt die Rolle [Abrechnungsleser](built-in-roles.md#billing-reader) vom Benutzer *alain\@example.com* im Verwaltungsgruppenbereich.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Nächste Schritte

- [Auflisten von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](role-assignments-list-cli.md)
- [Verwalten von Azure-Ressourcen und -Ressourcengruppen mithilfe der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/management/manage-resources-cli.md)
