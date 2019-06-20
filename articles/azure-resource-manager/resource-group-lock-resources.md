---
title: Sperren von Azure-Ressourcen zum Verhindern von Änderungen | Microsoft-Dokumentation
description: Verhindern Sie, dass Benutzer kritische Azure-Ressourcen aktualisieren oder löschen, indem Sie eine Sperre für alle Benutzer und Rollen anwenden.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: a6c7983d22eed4a4232fbb2db490c1743684a04c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813389"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Sperren von Ressourcen, um unerwartete Änderungen zu verhindern 

Als Administrator möchten Sie möglicherweise ein Abonnement, eine Ressourcengruppe oder eine Ressource sperren, um zu verhindern, dass andere Benutzer in Ihrer Organisation versehentlich wichtige Ressourcen löschen oder ändern. Sie können die Sperrebene auf **CanNotDelete** oder **ReadOnly** festlegen. Im Portal heißen die Sperren **Löschen** und **Schreibgeschützt**.

* **CanNotDelete** bedeutet, dass autorisierte Benutzer weiterhin eine Ressource lesen und ändern, aber nicht löschen können. 
* **ReadOnly** bedeutet, dass autorisierte Benutzer eine Ressource zwar lesen, aber nicht löschen oder aktualisieren können. Mit dieser Sperre erzielen Sie einen ähnlichen Effekt wie durch die Beschränkung sämtlicher autorisierter Benutzer auf die Berechtigungen der **Leserolle**. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Anwenden von Sperren

Wenn Sie eine Sperre in einem übergeordneten Bereich anwenden, erben alle Ressourcen in diesem Bereich die entsprechende Sperre. Auch Ressourcen, die Sie später hinzufügen, erben die Sperre aus dem übergeordneten Element. Die restriktivste Sperre in der Vererbung hat Vorrang.

Im Gegensatz zur rollenbasierten Zugriffssteuerung verwenden Sie Verwaltungssperren, um eine Einschränkung für alle Benutzer und Rollen zu aktivieren. Informationen zum Festlegen von Benutzer- und Rollenberechtigungen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/role-assignments-portal.md).

Resource Manager-Sperren gelten nur für Vorgänge auf der Verwaltungsebene (also für Vorgänge, die an `https://management.azure.com` gesendet werden). Die Ausführung ressourceneigener Funktionen wird durch die Sperren nicht eingeschränkt. Die Ressourcenänderungen sind eingeschränkt, die Ressourcenvorgänge jedoch nicht. Beispielsweise verhindert eine ReadOnly-Sperre für eine SQL-Datenbank das Löschen oder Ändern der Datenbank. Sie verhindert jedoch nicht das Erstellen, Aktualisieren oder Löschen von Daten in der Datenbank. Datentransaktionen sind zulässig, da diese Vorgänge nicht an `https://management.azure.com` gesendet werden.

Das Aktivieren von **ReadOnly** kann zu unerwarteten Ergebnissen führen, da einige Vorgänge, die die Ressource nicht zu ändern scheinen, tatsächlich Aktionen erfordern, die von der Sperre blockiert werden. Die Sperre **ReadOnly** kann auf die Ressource oder auf die Ressourcengruppe angewendet werden, die die Ressource enthält. Einige gängige Beispiele für die Vorgänge, die durch eine **ReadOnly**-Sperre blockiert werden, sind:

* Eine **ReadOnly**-Sperre für ein Speicherkonto hindert alle Benutzer am Auflisten der Schlüssel. Der Vorgang zum Auflisten von Schlüsseln wird über eine POST-Anforderung behandelt, da die zurückgegebenen Schlüssel für Schreibvorgänge zur Verfügung stehen.

* Das Festlegen einer **ReadOnly**-Sperre für eine App Service-Ressource verhindert, dass der Server-Explorer von Visual Studio Dateien für die Ressource anzeigen kann, da für diese Interaktion Schreibzugriff erforderlich ist.

* Eine **ReadOnly**-Sperre für eine Ressourcengruppe, die einen virtuellen Computer enthält, hindert alle Benutzer am Starten bzw. Neustarten des virtuellen Computers. Diese Vorgänge erfordern eine POST-Anforderung.

## <a name="who-can-create-or-delete-locks"></a>Voraussetzungen für das Erstellen oder Löschen von Sperren
Zum Erstellen oder Löschen von Verwaltungssperren benötigen Sie Zugriff auf `Microsoft.Authorization/*`- oder `Microsoft.Authorization/locks/*`-Aktionen. Unter den integrierten Rollen können nur **Besitzer** und **Benutzerzugriffsadministrator** diese Aktionen ausführen.

## <a name="managed-applications-and-locks"></a>Verwaltete Anwendungen und Sperren

Einige Azure-Dienste wie Azure Databricks verwenden für die Implementierung des Diensts [verwaltete Anwendungen](../managed-applications/overview.md). In diesem Fall erstellt der Dienst zwei Ressourcengruppen. Eine Ressourcengruppe enthält eine Übersicht über den Dienst und ist nicht gesperrt. Die andere Ressourcengruppe enthält die Infrastruktur für den Dienst und ist gesperrt.

Wenn Sie versuchen, die Infrastrukturressourcengruppe zu löschen, wird eine Fehlermeldung ausgegeben, dass die Ressourcengruppe gesperrt ist. Wenn Sie versuchen, die Sperre für die Infrastrukturressourcengruppe aufzuheben, wird eine Fehlermeldung ausgegeben, dass die Sperre nicht gelöscht werden kann, da sie einer Systemanwendung angehört.

Löschen Sie stattdessen den Dienst, dann wird auch die Infrastrukturressourcengruppe gelöscht.

Wählen Sie für verwaltete Anwendungen den Dienst, den Sie bereitgestellt haben, aus.

![Auswählen des Diensts](./media/resource-group-lock-resources/select-service.png)

Beachten Sie, dass der Dienst einen Link für eine **verwaltete Ressourcengruppe** aufweist. In dieser Ressourcengruppe befindet sich die Infrastruktur, und sie ist gesperrt. Sie kann nicht direkt gelöscht werden.

![Anzeigen der verwalteten Gruppe](./media/resource-group-lock-resources/show-managed-group.png)

Um alle Elemente für den Dienst zu löschen, einschließlich der gesperrten Infrastrukturressourcengruppe, wählen Sie **Löschen** für den Dienst aus.

![Suchdienst löschen](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portal
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Vorlage

Wenn Sie eine Sperre mithilfe einer Resource Manager-Vorlage bereitstellen, verwenden Sie je nach Umfang der Sperre unterschiedliche Werte für den Namen und den Typ.

Beim Anwenden einer Sperre für eine **Ressource** verwenden Sie die folgenden Formate:

* Name: `{resourceName}/Microsoft.Authorization/{lockName}`
* Typ: `{resourceProviderNamespace}/{resourceType}/providers/locks`

Beim Anwenden einer Sperre für eine **Ressourcengruppe** oder ein **Abonnement** verwenden Sie die folgenden Formate:

* Name: `{lockName}`
* Typ: `Microsoft.Authorization/locks`

Das folgende Beispiel zeigt eine Vorlage, die einen App Service-Plan, eine Website und eine Sperre für die Website erstellt. Der Ressourcentyp der Sperre ist der Ressourcentyp der zu sperrenden Ressource und **/providers/locks**. Der Name der Sperre wird erstellt, indem der Ressourcenname mit **/Microsoft.Authorization/** und dem Namen der Sperre verkettet wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Ein Beispiel für das Festlegen einer Sperre für eine Ressourcengruppe finden Sie unter [Erstellen und Sperren einer Ressourcengruppe](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Sie sperren bereitgestellte Ressourcen mit Azure PowerShell über den Befehl [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock).

Geben Sie zum Sperren einer Ressource den Namen der Ressource, ihren Ressourcentyp und ihren Ressourcengruppennamen an.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Geben Sie zum Sperren einer Ressourcengruppe ihren Namen an.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Rufen Sie Informationen zu einer Sperre mithilfe von [Get-AzureRmResourceLock](/powershell/module/az.resources/get-azresourcelock) ab. Rufen Sie alle Sperren im Abonnement mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock
```

Rufen Sie alle Sperren für eine Ressource mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Rufen Sie alle Sperren für eine Ressourcengruppe mit dem folgenden Befehl ab:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Eine Sperre löschen Sie folgendermaßen:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Sperren Sie bereitgestellte Ressourcen mit der Azure CLI, indem Sie den Befehl [az lock create](/cli/azure/lock#az-lock-create) verwenden.

Geben Sie zum Sperren einer Ressource den Namen der Ressource, ihren Ressourcentyp und ihren Ressourcengruppennamen an.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Geben Sie zum Sperren einer Ressourcengruppe ihren Namen an.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Verwenden Sie zum Abrufen von Informationen zu einer Sperre [az lock list](/cli/azure/lock#az-lock-list). Rufen Sie alle Sperren im Abonnement mit dem folgenden Befehl ab:

```azurecli
az lock list
```

Rufen Sie alle Sperren für eine Ressource mit dem folgenden Befehl ab:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Rufen Sie alle Sperren für eine Ressourcengruppe mit dem folgenden Befehl ab:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Eine Sperre löschen Sie folgendermaßen:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API
Sie können bereitgestellte Ressourcen mit der [REST-API für Verwaltungssperren](https://docs.microsoft.com/rest/api/resources/managementlocks) sperren. Die REST-API ermöglicht es Ihnen, Sperren zu erstellen und zu löschen sowie Informationen zu vorhandenen Sperren abzurufen.

Führen Sie zum Erstellen einer Sperre Folgendes durch:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Bei dem Bereich kann es sich um ein Abonnement, die Ressourcengruppe oder die Ressource handeln. Geben Sie für "lock-name" den jeweiligen Namen der Sperre ein. Verwenden Sie als „api-version“ die Einstellung **2016-09-01**.

Schließen Sie in die Anforderung ein JSON-Objekt ein, das die Eigenschaften für die Sperre angibt.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Nächste Schritte
* Informationen zum logischen Organisieren von Ressourcen finden Sie unter [Verwenden von Tags zum Organisieren von Ressourcen](resource-group-using-tags.md)
* Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../governance/policy/overview.md).
* Anleitungen dazu, wie Unternehmen Abonnements mit Resource Manager effektiv verwalten können, finden Sie unter [Azure-Unternehmensgerüst - Präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance).

