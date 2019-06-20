---
title: 'Gewusst wie: Verwalten von Zuweisungen mit PowerShell'
description: Es wird beschrieben, wie Sie Blaupausenzuweisungen mit dem offiziellen PowerShell-Modul „Az.Blueprint“ von Azure Blueprints verwalten.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d8eacffe4b792eda5d81051f6aa65caa3292c896
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60682842"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Gewusst wie: Verwalten von Zuweisungen mit PowerShell

Eine Blaupausenzuweisung kann mit dem Azure PowerShell-Modul **Az.Blueprint** verwaltet werden. Das Modul unterstützt das Abrufen, Erstellen, Aktualisieren und Entfernen von Zuweisungen. Darüber hinaus können mit dem Modul auch Details zu vorhandenen Blaupausendefinitionen abgerufen werden. In diesem Artikel wird beschrieben, wie Sie das Modul installieren und mit der Nutzung beginnen.

## <a name="add-the-azblueprint-module"></a>Hinzufügen des Moduls „Az.Blueprint“

Das Modul muss hinzugefügt werden, um für Azure PowerShell das Verwalten von Blaupausenzuweisungen zu ermöglichen. Dieses Modul kann mit einer lokal installierten PowerShell-Instanz, mit [Azure Cloud Shell](https://shell.azure.com) oder mit dem [Azure PowerShell-Docker-Image](https://hub.docker.com/r/azuresdk/azure-powershell/) verwendet werden.

### <a name="base-requirements"></a>Basisanforderungen

Für das Azure Blueprints-Modul ist die folgende Software erforderlich:

- Azure PowerShell 1.5.0 oder höher. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 oder höher. Falls es nicht installiert oder aktuell ist, befolgen Sie [diese Anweisungen](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installieren des Moduls

Das Blueprints-Modul für PowerShell ist **Az.Blueprint**.

1. Führen Sie an einer **Administratoreingabeaufforderung** von PowerShell den folgenden Befehl aus:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Falls bereits **Az.Accounts** installiert ist, muss unter Umständen `-AllowClobber` verwendet werden, um die Installation zu erzwingen.

1. Vergewissern Sie sich, dass das Modul importiert wurde und die richtige Version (0.1.0) hat:

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Abrufen von Blaupausendefinitionen

Der erste Schritt bei der Arbeit mit einer Zuweisung ist häufig das Abrufen eines Verweises auf eine Blaupausendefinition.
Mit dem Cmdlet `Get-AzBlueprint` wird mindestens eine Blaupausendefinition abgerufen. Das Cmdlet kann Blaupausendefinitionen aus einer Verwaltungsgruppe mit `-ManagementGroupId {mgId}` oder aus einem Abonnement mit `-SubscriptionId {subId}` abrufen. Mit dem Parameter **Name** wird eine Blaupausendefinition abgerufen, aber er muss zusammen mit **ManagementGroupId** oder **SubscriptionId** verwendet werden. **Version** kann mit **Name** verwendet werden, um genauer anzugeben, welche Blaupausendefinition zurückgegeben werden soll. Anstelle von **Version** wird mit dem Switch `-LatestPublished` die zuletzt veröffentlichte Version abgerufen.

Im folgenden Beispiel wird `Get-AzBlueprint` verwendet, um alle Versionen einer Blaupausendefinition mit dem Namen „101-blueprints-definition-subscription“ aus dem spezifischen Abonnement `{subId}` abzurufen:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Die Beispielausgabe für eine Blaupausendefinition mit mehreren Versionen sieht wie folgt aus:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Die [Blaupausenparameter](../concepts/parameters.md#blueprint-parameters) in der Blaupausendefinition können erweitert werden, um weitere Informationen bereitzustellen.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Abrufen von Blaupausenzuweisungen

Wenn die Blaupausenzuweisung bereits vorhanden ist, können Sie mit dem Cmdlet `Get-AzBlueprintAssignment` einen Verweis darauf abrufen. Für das Cmdlet werden **SubscriptionId** und **Name** als optionale Parameter verwendet. Wenn **SubscriptionId** nicht angegeben ist, wird der aktuelle Abonnementkontext verwendet.

Im folgenden Beispiel wird `Get-AzBlueprintAssignment` verwendet, um eine einzelne Blaupausenzuweisung mit dem Namen „Assignment-lock-resource-groups“ aus dem Abonnement `{subId}` abzurufen:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Die Beispielausgabe für eine Blaupausenzuweisung sieht wie folgt aus:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Erstellen von Blaupausenzuweisungen

Falls die Blaupausenzuweisung noch nicht vorhanden ist, können Sie sie mit dem Cmdlet `New-AzBlueprintAssignment` erstellen. Für dieses Cmdlet werden die folgenden Parameter verwendet:

- **Name** [erforderlich]
  - Gibt den Namen der Blaupausenzuweisung an.
  - Der Name muss eindeutig sein und darf nicht bereits in **SubscriptionId** vorhanden sein.
- **Blaupause** [erforderlich]
  - Gibt die Blaupausendefinition an, die zugewiesen werden soll.
  - Verwenden Sie `Get-AzBlueprint`, um das Verweisobjekt abzurufen.
- **Standort** [erforderlich]
  - Gibt die Region für die vom System zugewiesene verwaltete Identität und das Bereitstellungsobjekt des Abonnements für die Erstellung an.
- **Abonnement** (optional)
  - Gibt das Abonnement an, unter dem die Zuweisung bereitgestellt wird.
  - Wenn der Parameter nicht angegeben wird, wird standardmäßig der aktuelle Abonnementkontext verwendet.
- **Sperre** (optional)
  - Dient zum Definieren der [Blaupausen-Ressourcensperre](../concepts/resource-locking.md) für bereitgestellte Ressourcen.
  - Unterstützte Optionen: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Wenn der Parameter nicht angegeben wird, wird standardmäßig _None_ verwendet.
- **SystemAssignedIdentity** (optional)
  - Wählen Sie diesen Parameter, um eine vom System zugewiesene verwaltete Identität für die Zuweisung zu erstellen und die Ressourcen bereitzustellen.
  - Standardoption für den Parametersatz „identity"
  - Kann nicht mit **UserAssignedIdentity** verwendet werden.
- **UserAssignedIdentity** (optional)
  - Gibt die vom Benutzer zugewiesene verwaltete Identität an, die für die Zuweisung und zum Bereitstellen der Ressourcen verwendet werden soll.
  - Teil des Parametersatzes „identity“
  - Kann nicht mit **SystemAssignedIdentity** verwendet werden.
- **Parameter** (optional)
  - Eine [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) mit Schlüssel-Wert-Paaren zum Festlegen von [dynamischen Parametern](../concepts/parameters.md#dynamic-parameters) in der Blaupausenzuweisung.
  - Der Standardwert für einen dynamischen Parameter ist **defaultValue** in der Definition.
  - Wenn ein Parameter nicht angegeben wird und nicht über einen Standardwert (**defaultValue**) verfügt, ist der Parameter nicht optional.

    > [!NOTE]
    > Für **Parameter** wird „secureStrings“ nicht unterstützt.

- **ResourceGroupParameter** (optional)
  - Eine [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) mit Ressourcengruppenartefakten.
  - Jeder Ressourcengruppenartefakt-Platzhalter verfügt über ein Schlüssel-Wert-Paar zum dynamischen Festlegen von **Name** bzw. **Location** im entsprechenden Ressourcengruppenartefakt.
  - Wenn kein Ressourcengruppenparameter angegeben wird und kein **defaultValue**-Element vorhanden ist, ist der Ressourcengruppenparameter nicht optional.

Im folgenden Beispiel wird eine neue Zuweisung von Version „1.1“ der Blaupausendefinition „my-blueprint“ erstellt, die mit `Get-AzBlueprint` abgerufen wird. Hierbei wird der Standort der verwalteten Identität und des Zuweisungsobjekts auf „westus2“ festgelegt, die Ressourcen werden mit _AllResourcesReadOnly_ gesperrt, und die Hashtabellen für **Parameter** und **ResourceGroupParameter** im Abonnement `{subId}` werden festgelegt:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Die Beispielausgabe für die Erstellung einer Blaupausenzuweisung sieht wie folgt aus:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Aktualisieren von Blaupausenzuweisungen

In einigen Fällen kann es erforderlich sein, eine bereits erstellte Blaupausenzuweisung zu aktualisieren. Diese Aktion wird mit dem Cmdlet `Set-AzBlueprintAssignment` verarbeitet. Für das Cmdlet werden die meisten Parameter wie für das Cmdlet `New-AzBlueprintAssignment` verwendet, und alle Festlegungen der Zuweisung können aktualisiert werden. Ausnahmen hiervon sind _Name_, _Blueprint_ und _SubscriptionId_. Nur die angegebenen Werte werden aktualisiert.

Informationen zum Ablauf bei der Aktualisierung einer Blaupausenzuweisung finden Sie unter [Regeln für das Aktualisieren von Zuweisungen](./update-existing-assignments.md#rules-for-updating-assignments).

- **Name** [erforderlich]
  - Gibt den Namen der zu aktualisierenden Blaupausenzuweisung an.
  - Wird verwendet, um die zu aktualisierende Zuweisung zu ermitteln (nicht zum Ändern der Zuweisung).
- **Blaupause** [erforderlich]
  - Gibt die Blaupausendefinition der Blaupausenzuweisung an.
  - Verwenden Sie `Get-AzBlueprint`, um das Verweisobjekt abzurufen.
  - Wird verwendet, um die zu aktualisierende Zuweisung zu ermitteln (nicht zum Ändern der Zuweisung).
- **Standort** (optional)
  - Gibt die Region für die vom System zugewiesene verwaltete Identität und das Bereitstellungsobjekt des Abonnements für die Erstellung an.
- **Abonnement** (optional)
  - Gibt das Abonnement an, unter dem die Zuweisung bereitgestellt wird.
  - Wenn der Parameter nicht angegeben wird, wird standardmäßig der aktuelle Abonnementkontext verwendet.
  - Wird verwendet, um die zu aktualisierende Zuweisung zu ermitteln (nicht zum Ändern der Zuweisung).
- **Sperre** (optional)
  - Dient zum Definieren der [Blaupausen-Ressourcensperre](../concepts/resource-locking.md) für bereitgestellte Ressourcen.
  - Unterstützte Optionen: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optional)
  - Wählen Sie diesen Parameter, um eine vom System zugewiesene verwaltete Identität für die Zuweisung zu erstellen und die Ressourcen bereitzustellen.
  - Standardoption für den Parametersatz „identity"
  - Kann nicht mit **UserAssignedIdentity** verwendet werden.
- **UserAssignedIdentity** (optional)
  - Gibt die vom Benutzer zugewiesene verwaltete Identität an, die für die Zuweisung und zum Bereitstellen der Ressourcen verwendet werden soll.
  - Teil des Parametersatzes „identity“
  - Kann nicht mit **SystemAssignedIdentity** verwendet werden.
- **Parameter** (optional)
  - Eine [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) mit Schlüssel-Wert-Paaren zum Festlegen von [dynamischen Parametern](../concepts/parameters.md#dynamic-parameters) in der Blaupausenzuweisung.
  - Der Standardwert für einen dynamischen Parameter ist **defaultValue** in der Definition.
  - Wenn ein Parameter nicht angegeben wird und nicht über einen Standardwert (**defaultValue**) verfügt, ist der Parameter nicht optional.

    > [!NOTE]
    > Für **Parameter** wird „secureStrings“ nicht unterstützt.

- **ResourceGroupParameter** (optional)
  - Eine [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) mit Ressourcengruppenartefakten.
  - Jeder Ressourcengruppenartefakt-Platzhalter verfügt über ein Schlüssel-Wert-Paar zum dynamischen Festlegen von **Name** bzw. **Location** im entsprechenden Ressourcengruppenartefakt.
  - Wenn kein Ressourcengruppenparameter angegeben wird und kein **defaultValue**-Element vorhanden ist, ist der Ressourcengruppenparameter nicht optional.

Im folgenden Beispiel wird die Zuweisung von Version „1.1“ der Blaupausendefinition „my-blueprint“ aktualisiert, die mit `Get-AzBlueprint` durch eine Änderung des Sperrmodus abgerufen wird:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Die Beispielausgabe für die Erstellung einer Blaupausenzuweisung sieht wie folgt aus:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Entfernen von Blaupausenzuweisungen

Wenn eine Blaupausenzuweisung entfernt werden soll, wird diese Aktion mit dem Cmdlet `Remove-AzBlueprintAssignment` verarbeitet. Für das Cmdlet werden entweder **Name** oder **InputObject** verwendet, um anzugeben, welche Blaupausenzuweisung entfernt werden soll. **SubscriptionId** ist _erforderlich_ und muss in allen Fällen angegeben werden.

Im folgenden Beispiel wird eine vorhandene Blaupausenzuweisung mit `Get-AzBlueprintAssignment` abgerufen und dann aus dem spezifischen Abonnement `{subId}` entfernt:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Umfassendes Codebeispiel

Im folgenden Beispiel sind alle Schritte zusammengefasst. Zuerst wird die Blaupausendefinition abgerufen, und anschließend wird eine Blaupausenzuweisung im spezifischen Abonnement `{subId}` entfernt:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).