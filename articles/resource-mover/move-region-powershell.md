---
title: Verschieben von Ressourcen zwischen Regionen mithilfe von PowerShell in Azure Resource Mover
description: Hier erfahren Sie, wie Sie Ressourcen zwischen Regionen mithilfe von PowerShell in Azure Resource Mover verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 0aca0e49d72025686cf44d434fa7a43ae0c86e0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461113"
---
# <a name="move-resources-across-regions-in-powershell"></a>Verschieben von Ressourcen zwischen Regionen mithilfe von PowerShell

Hier erfahren Sie, wie Sie Azure-Ressourcen in eine andere Region mithilfe von PowerShell in Azure Resource Mover verschieben. 

> [!NOTE]
> Azure Resource Mover ist derzeit als Vorschauversion verfügbar.



## <a name="before-you-start"></a>Vorbereitung

- Ihr Azure-Abonnement erfordert Zugriffsrechte auf Resource Mover. Zudem müssen Sie über Berechtigungen als [Besitzer](../role-based-access-control/built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) für das Abonnement verfügen.
- Resource Mover verfolgt keine Änderungen und Upgrades, also nehmen Sie alle erforderlichen Änderungen an den Ressourcen vor, bevor Sie sie verschieben.
- Wenn Sie Ressourcen mit PowerShell verschieben, können Sie derzeit keine Einstellungen für die Zielregion bearbeiten. Ändern Sie diese Einstellungen direkt über das Portal.
- Wenn Sie einer zu verschiebenden Sammlung Ressourcen hinzufügen, um für das Verschieben in eine andere Region vorzubereiten, werden die Metadaten zur Verschiebung in einer Ressourcengruppe gespeichert, die zu diesem Zweck erstellt wird. Diese Ressourcengruppe kann sich derzeit in den Regionen „USA, Osten 2“ oder „Europa, Norden“ befinden. Azure-Ressourcen können zwischen beliebigen öffentlichen Regionen mithilfe von Metadaten, die in einer dieser Regionen vorhanden sind, verschoben werden.

## <a name="sample-values"></a>Beispielwerte

Wir verwenden diese Werte in unseren Skriptbeispielen:

**Einstellung** | **Wert** 
--- | ---
Abonnement-ID | subscription-id
Speicherort des Resource Mover-Diensts | USA (Ost) 2
Metadatenressourcengruppe | RegionMoveRG-centralus-westcentralus
Speicherort der Metadatenressourcengruppe | USA (Ost) 2
Sammlungsnamen verschieben | MoveCollection-centralus-westcentralus
Quellregion |  centralus
Quellressourcengruppe | PSDemoRM
Zielregion | westcentralus
Zielressourcengruppe | PSDemoRMtgt
Zu verschiebende VM | PSDemoVM

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich bei Ihrem Azure-Abonnement an.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Erstellen der Ressourcengruppe für Metadaten

Erstellen Sie eine Ressourcengruppe, die die Metadaten und Konfigurationsinformationen der zu verschiebenden Sammlung enthält.

```azurepowershell-interactive
# Create the resource group for metadata
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Erwartete Ausgabe**:

![Ausgabetext nach der Erstellung einer Ressourcengruppe](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

Registrieren Sie den Ressourcenanbieter „Microsoft.Migrate“, damit die Ressource „MoveCollection“ erstellt werden kann.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Erstellen der zu verschiebenden Sammlung

Das MoveCollection-Objekt speichert Metadaten und Konfigurationsinformationen zu den Ressourcen, die Sie verschieben möchten.

- Damit das MoveCollection-Objekt auf das Abonnement zugreifen kann, in dem sich der Azure Resource Mover-Dienst befindet, ist eine [systemseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identity (MSI) bezeichnet) erforderlich, für die eine Vertrauensstellung mit dem Abonnement besteht.
- Der Identität wird die Rolle „Mitwirkender“ oder „Benutzerzugriffsadministrator“ für das Quellabonnement zugewiesen.
- Um der Identität eine Rolle zuzuweisen, benötigen Sie eine Rolle im Abonnement (z. B. „Besitzer“ oder „Benutzerzugriffsadministrator“) mit diesen Berechtigungen:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus" -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Erwartete Ausgabe**:

![Ausgabetext nach der Erstellung einer zu verschiebenden Sammlung](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Zuweisen einer verwalteten Identität 

Legen Sie die Abonnement-ID fest, rufen Sie den Identitätsprinzipal des Objekts „MoveCollection“ ab, und weisen Sie ihm die Azure-Rollen zu.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -Name "MoveCollection-centralus-westcentralus"

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
```

## <a name="add-resources-to-the-move-collection"></a>Hinzufügen von Ressourcen zur Sammlung für die Verschiebung

Rufen Sie die ID der Quellressource ab, die Sie verschieben möchten. Anschließend fügen Sie sie zur Sammlung für die Verschiebung hinzu.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Abrufen der Ressourcen-ID](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Erwartete Ausgabe**
![Ausgabetext nach Hinzufügen der Ressource](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten

Überprüfen Sie die von Ihnen hinzugefügten Ressourcen, und lösen Sie alle Abhängigkeiten von anderen Ressourcen auf.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Ausgabe, wenn Abhängigkeiten vorhanden sind**

Wenn die Ressource in der Sammlung für die Verschiebung Abhängigkeiten von anderen Ressourcen aufweist, wird eine Fehlermeldung ausgegeben.

![Ausgabetext nach dem Überprüfen von Abhängigkeiten](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Abrufen von Abhängigkeiten

Wenn die Ressource, die Sie verschieben möchten, Abhängigkeiten von anderen Ressourcen aufweist, können Sie Informationen über die fehlenden Abhängigkeiten abrufen.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Abrufen von Abhängigkeiten](./media/move-region-powershell/missing-dependencies.png)

In diesem Beispiel werden zwei Abhängigkeiten als fehlend identifiziert:

- Quellressourcengruppe: PSDemoRM
- NIC auf VM: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Hinzufügen von Abhängigkeiten zur Sammlung


Identifizieren Sie mit den abgerufenen Ressourcen-IDs die Namen der fehlenden Ressourcen, und fügen Sie sie der Sammlung für die Verschiebung hinzu.

### <a name="add-the-nic"></a>Hinzufügen der Netzwerkkarte

Rufen Sie den NIC-Namen und -Typen ab, und fügen Sie ihn der Sammlung hinzu.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Abrufen der NIC](./media/move-region-powershell/output-retrieve-nic.png)

Fügen Sie nun die NIC zur Sammlung zur Verschiebung hinzu. In diesem Beispiel wird der Ziel-NIC der gleiche Name gegeben. Behalten Sie die Einstellungen und Groß-/Kleinschreibung bei.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Hinzufügen der NIC zur Sammlung](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Hinzufügen der Quellressourcengruppe

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Hinzufügen der Ressourcengruppe zur Sammlung](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Erneutes Überprüfen von Abhängigkeiten

Überprüfen Sie noch mal, ob Abhängigkeiten fehlen.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Ausgabe**

Es gibt weitere fehlende Abhängigkeiten.

![Ausgabetext nach dem erneuten Überprüfen von Abhängigkeiten](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Abrufen zusätzlicher Abhängigkeiten

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Erwartete Ausgabe**


![Ausgabetext nach dem Abrufen zusätzlicher Abhängigkeiten](./media/move-region-powershell/additional-missing-dependencies.png)

In diesem Beispiel werden drei weitere Abhängigkeiten als fehlend identifiziert:

- Öffentliche IP-Adresse: psdemovm-ip
- Virtuelles Azure-Netzwerk: psdemorm-vnet
- Netzwerksicherheitsgruppe (NSG): psdemovm-nsg

## <a name="add-additional-dependencies"></a>Hinzufügen zusätzlicher Abhängigkeiten

1. [ Befolgen Sie die Anweisungen](#add-dependencies-to-collection), um diese Ressourcen der Sammlung für die Verschiebung hinzuzufügen.
2. Nachdem Sie Ressourcen hinzugefügt haben, überprüfen Sie die Abhängigkeiten noch mal, bis alle hinzugefügt sind.


## <a name="prepare-and-move-the-source-resource-group"></a>Vorbereiten und Verschieben der Quellressourcengruppe

In der Quellregion bereiten Sie Ressourcen vor, bevor Sie diese verschieben. Die Art der Vorbereitung hängt von den Ressourcen ab, die Sie verschieben. Beispielsweise kann für die Vorbereitung von zustandslosen Ressource eine Azure Resource Manager-Vorlage (ARM) genutzt und exportiert werden. Für zustandsbehaftete Ressourcen kann die Replikation gestartet werden. 

Bevor Sie die Quellressourcen vorbereiten können, müssen Sie die Quellressourcengruppe vorbereiten und verschieben.

### <a name="prepare"></a>Vorbereiten

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Erwartete Ausgabe**

![Ausgabetext nach der Vorbereitung der Quellressourcengruppe](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Initiieren der Verschiebung

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Initiieren der Verschiebung der Quellressourcengruppe](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Erwartete Ausgabe**

![Ausgabetext nach dem Committen der Quellressourcengruppe](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Vorbereiten der Ressourcen

Nachdem die Quellressourcengruppe in die Zielregion verschoben wurde, rufen Sie eine Liste der Ressourcen in der Sammlung für die Verschiebung ab und bereiten die Verschiebung vor.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “<subscription-id>“ -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Erwartete Ausgabe**

![Ausgabetext nach dem Abrufen von Ressourcen in der Sammlung](./media/move-region-powershell/collection-resources.png)

Bereiten Sie jetzt die Ressourcen vor.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Alternativ dazu können Sie Ressourcen mit der Ressourcen-ID anstelle des Namens vorbereiten und verschieben:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Erwartete Ausgabe**

![Ausgabetext nach der Vorbereitung der Ressourcen in der Sammlung](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Initiieren der Ressourcenverschiebung

Nachdem Sie die Ressourcen vorbereitet haben, initiieren Sie die Verschiebung.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Erwartete Ausgabe**

![Ausgabetext nach dem Initiieren der Ressourcenverschiebung](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Verwerfen oder Committen der Verschiebung

Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie einen Commit für die Verschiebung durchführen oder sie verwerfen möchten. 

- **Verwerfen**: Sie können eine Verschiebung verwerfen, wenn Sie Tests durchführen und die Quellressource letztendlich nicht verschoben werden soll. Beim Verwerfen der Verschiebung wird die Ressource in den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) zurückgesetzt. Sie können den Verschiebevorgang dann bei Bedarf noch mal initiieren.
- **Commit**: Beim Commit wird die Verschiebung in die Zielregion abgeschlossen. Nach dem Commit hat eine Quellressource den Status *Delete source pending* (Löschen der Quelle ausstehend), und Sie können entscheiden, ob sie gelöscht werden soll.

### <a name="discard"></a>Verwerfen

So verwerfen Sie die Verschiebung

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Erwartete Ausgabe**

![Ausgabetext nach dem Verwerfen der Verschiebung](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

So committen Sie die Verschiebung

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Erwartete Ausgabe**

![Ausgabetext nach dem Commit der Verschiebung](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Löschen der Quellressourcen

Nach dem Commit der Verschiebung und der Überprüfung, ob die Ressourcen in der Zielregion erwartungsgemäß funktionieren, können Sie die Quellressourcen im [Azure-Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) [mithilfe von PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) oder der [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) löschen.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie](remove-move-resources.md), wie Sie Ressourcen aus einer Sammlung für die Verschiebung entfernen.
