---
title: 'Schnellstart: Erstellen einer Blaupause mit PowerShell'
description: In dieser Schnellstartanleitung verwenden Sie Azure Blueprints, um Artefakte mithilfe von PowerShell zu erstellen, zu definieren und bereitzustellen.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 65d573d0aec7d5f292bc985483e1f12c350ae03a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918278"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Schnellstart: Definieren und Zuweisen einer Azure-Blaupause mit PowerShell

Wenn Sie mit der Erstellung und Zuweisung von Blaupausen vertraut sind, können Sie allgemeine Muster definieren, um wiederverwendbare und schnell bereitstellbare Konfigurationen zu entwickeln, die auf Azure Resource Manager-Vorlagen (ARM-Vorlagen), Richtlinien, Sicherheit usw. basieren. In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Blueprint einige allgemeine Aufgaben im Zusammenhang mit der organisationsweiten Erstellung, Veröffentlichung und Zuweisung einer Blaupause ausführen:

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.
- Falls das Modul **AZ.Blueprint** nicht bereits installiert ist, befolgen Sie die Anweisungen unter [Hinzufügen des Moduls „AZ.Blueprint“](./how-to/manage-assignments-ps.md#add-the-azblueprint-module), um das Modul aus dem PowerShell-Katalog zu installieren und zu überprüfen.
- Registrieren Sie den Ressourcenanbieter über Azure PowerShell mit `Register-AzResourceProvider -ProviderNamespace Microsoft.Blueprint`, falls Sie Azure Blueprints noch nicht verwendet haben.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Erstellen einer Blaupause

Im ersten Schritt beim Definieren eines Standardmusters für die Konformität wird eine Blaupause aus den verfügbaren Ressourcen erstellt. Wir erstellen eine Blaupause namens „MyBlueprint“, um Rollen- und Richtlinienzuweisungen für das Abonnement zu konfigurieren. Anschließend fügen wir eine Ressourcengruppe, eine ARM-Vorlage und eine Rollenzuweisung für die Ressourcengruppe hinzu.

> [!NOTE]
> Bei Verwendung von PowerShell wird als Erstes das Objekt _blueprint_ erstellt. Für jedes hinzugefügte _Artefakt_, das über Parameter verfügt, müssen die Parameter vorab in der anfänglichen _Blaupause_ definiert werden.

1. Erstellen Sie das anfängliche _blueprint_-Objekt. Der Parameter **BlueprintFile** akzeptiert eine JSON-Datei, die die Eigenschaften der Blaupause, die zu erstellenden Ressourcengruppen und alle Parameter auf Blaupausenebene enthält. Die Parameter werden im Rahmen der Zuweisung festgelegt und von den in späteren Schritten hinzugefügten Artefakten verwendet.

   - JSON-Datei: blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "defaultValue": "Standard_LRS",
                     "allowedValues": [
                         "Standard_LRS",
                         "Standard_GRS",
                         "Standard_ZRS",
                         "Premium_LRS"
                     ],
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Verwenden Sie den Dateinamen _blueprint.json_, wenn Sie Ihre Blaupausendefinitionen programmgesteuert erstellen.
     > Dieser Dateiname wird beim Aufrufen von [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact) verwendet.

     Das Blaupausenobjekt wird standardmäßig im Standardabonnement erstellt. Verwenden Sie zum Angeben der Verwaltungsgruppe den Parameter **ManagementGroupId**. Verwenden Sie zum Angeben des Abonnements den Parameter **SubscriptionId**.

1. Fügen Sie eine Rollenzuweisung für das Abonnement hinzu. **ArtifactFile** definiert die _Art_ des Artefakts. Die Eigenschaften orientieren sich am Rollendefinitionsbezeichner, und die Prinzipalbezeichner werden als Array von Werten übergeben. Im Beispiel unten ist für die Prinzipalbezeichner, denen die angegebene Rolle zugewiesen wird, ein Parameter konfiguriert, der bei der Blaupausenzuweisung festgelegt wird. In diesem Beispiel wird die integrierte Rolle _Mitwirkender_ mit der GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` verwendet.

   - JSON-Datei: \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Fügen Sie eine Richtlinienzuweisung für das Abonnement hinzu. **ArtifactFile** definiert die _Art_ des Artefakts sowie die Eigenschaften, die sich an einer Richtlinie oder Initiativdefinition orientieren. Er konfiguriert außerdem die Richtlinienzuweisung für die Verwendung der definierten Blaupausenparameter, die im Rahmen der Blaupausenzuweisung konfiguriert werden. In diesem Beispiel wird die integrierte Richtlinie _Tag und dessen Standardwert auf Ressourcengruppen anwenden_ mit der `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` verwendet.

   - JSON-Datei: \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Fügen Sie eine weitere Richtlinienzuweisung für das Storage-Tag (unter Wiederverwendung des Parameters _storageAccountType_) für das Abonnement hinzu. Dieses zusätzliche Artefakt der Richtlinienzuweisung veranschaulicht, dass ein für die Blaupause definierter Parameter von mehreren Artefakten verwendet werden kann. In dem Beispiel wird **storageAccountType** verwendet, um ein Tag für die Ressourcengruppe festzulegen. Dieser Wert stellt Informationen zum im nächsten Schritt erstellten Speicherkonto bereit. In diesem Beispiel wird die integrierte Richtlinie _Tag und dessen Standardwert auf Ressourcengruppen anwenden_ mit der `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` verwendet.

   - JSON-Datei: \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Fügen Sie eine Vorlage unter der Ressourcengruppe hinzu. **TemplateFile** für eine ARM-Vorlage enthält die normale JSON-Komponente der Vorlage. Darüber hinaus verwendet die Vorlage auch die Vorlagenparameter **storageAccountType**, **tagName** und **tagValue** wieder, indem sie jeweils an die Vorlage übergeben werden. Die Blaupausenparameter werden für die Vorlage mithilfe des Parameters **TemplateParameterFile** bereitgestellt, und innerhalb des JSON-Codes der Vorlage wird der Wert mithilfe dieses Schlüssel-Wert-Paars eingefügt. Die Namen des Blaupausen- und des Vorlagenparameters können identisch sein.

   - JSON-ARM-Vorlagendatei: \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON-ARM-Vorlagenparameterdatei: \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Fügen Sie eine Rollenzuweisung unter der Ressourcengruppe hinzu. Ähnlich wie beim vorherigen Rollenzuweisungseintrag wird im folgenden Beispiel der Definitionsbezeichner für die Rolle **Besitzer** verwendet und dafür ein anderer Parameter aus der Blaupause angegeben. In diesem Beispiel wird die integrierte Rolle _Besitzer_ mit der GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` verwendet.

   - JSON-Datei: \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Veröffentlichen einer Blaupause

Nach dem Hinzufügen der Artefakte zur Blaupause kann diese veröffentlicht werden. Durch die Veröffentlichung kann sie einem Abonnement zugewiesen werden.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Der Wert für `{BlueprintVersion}` ist eine Zeichenfolge mit Buchstaben, Zahlen und Bindestrichen (keine Leerzeichen oder Sonderzeichen) mit einer maximalen Länge von 20 Zeichen. Verwenden Sie einen eindeutigen und aussagekräftigen Wert, z.B. **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Zuweisen einer Blaupause

Nachdem eine Blaupause mithilfe von PowerShell veröffentlicht wurde, kann sie einem Abonnement zugewiesen werden. Weisen Sie die erstellte Blaupause einem der Abonnements unter Ihrer Verwaltungsgruppenhierarchie zu. Wenn die Blaupause in einem Abonnement gespeichert wird, kann sie nur diesem Abonnement zugewiesen werden. Der Parameter **Blueprint** gibt die zuzuweisende Blaupause an. Verwenden Sie zum Angeben von Parametern für Name, Speicherort, Identität, Sperrung und Blaupause die entsprechenden PowerShell-Parameter im Cmdlet `New-AzBlueprintAssignment`, oder geben Sie sie in der JSON-Parameterdatei **AssignmentFile** an.

1. Führen Sie die Blaupausenbereitstellung aus, indem Sie sie einem Abonnement zuweisen. Da für die Parameter **contributors** und **owners** ein Array von Objekt-IDs der Prinzipale, denen die Rollenzuweisung erteilt wird, erforderlich ist, verwenden Sie die [Azure Active Directory Graph-API](../../active-directory/develop/active-directory-graph-api.md), um die Objekt-IDs zu sammeln, die in **AssignmentFile** für Ihre eigenen Benutzer, Gruppen oder Dienstprinzipale verwendet werden.

   - JSON-Datei: blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell-Befehl

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Benutzerseitig zugewiesene verwaltete Identität

     Eine Blaupausenzuweisung kann auch eine [benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) verwenden.
     In diesem Fall ändert sich der Identitätsteil (**identity**) der JSON-Zuweisungsdatei wie folgt. Ersetzen Sie `{tenantId}`, `{subscriptionId}`, `{yourRG}` und `{userIdentity}` durch Ihre Mandanten-ID, Ihre Abonnement-ID, den Namen Ihrer Ressourcengruppe bzw. den Namen Ihrer benutzerseitig zugewiesenen verwalteten Identität.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     Die **benutzerseitig zugewiesene verwaltete Identität** kann sich in einem beliebigen Abonnement/in einer beliebigen Ressourcengruppe befinden, für das/für die der Benutzer, der die Blaupause zuweist, über Berechtigungen verfügt.

     > [!IMPORTANT]
     > Die benutzerseitig zugewiesene verwaltete Identität wird nicht von Azure Blueprints verwaltet. Benutzer müssen angemessene Rollen und Berechtigungen zuweisen. Andernfalls ist die Blaupausenzuweisung nicht erfolgreich.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="unassign-a-blueprint"></a>Aufheben der Zuweisung einer Blaupause

Sie können eine Blaupause aus einem Abonnement entfernen. Dieser Schritt wird häufig ausgeführt, wenn die Artefaktressourcen nicht mehr benötigt werden. Wenn eine Blaupause entfernt wird, werden die als Teil der Blaupause zugewiesenen Artefakte beibehalten. Verwenden Sie zum Entfernen einer Blaupausenzuweisung das Cmdlet `Remove-AzBlueprintAssignment`:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Blaupause mit PowerShell erstellt, zugewiesen und entfernt. Weitere Informationen zu Azure Blueprints finden Sie im Artikel zum Lebenszyklus von Blaupausen.

> [!div class="nextstepaction"]
> [Informationen zum Lebenszyklus von Blaupausen](./concepts/lifecycle.md)