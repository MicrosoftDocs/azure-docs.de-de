---
title: 'Schnellstart: Erstellen einer Blaupause mit Azure CLI'
description: In dieser Schnellstartanleitung verwenden Sie Azure Blueprint, um Artefakte mithilfe der Azure CLI zu erstellen, zu definieren und bereitzustellen.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 88ccd3fe6271a79d201007954e7997b68d2dc69b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920558"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Schnellstart: Definieren und Zuweisen einer Azure-Blaupause mit der Azure CLI

Wenn Sie mit der Erstellung und Zuweisung von Blaupausen vertraut sind, können Sie allgemeine Muster definieren, um wiederverwendbare und schnell bereitstellbare Konfigurationen zu entwickeln, die auf Azure Resource Manager-Vorlagen (ARM-Vorlagen), Richtlinien, Sicherheit usw. basieren. In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Blueprint einige allgemeine Aufgaben im Zusammenhang mit der organisationsweiten Erstellung, Veröffentlichung und Zuweisung einer Blaupause ausführen:

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Hinzufügen der Blueprint-Erweiterung

Damit Azure CLI Blaupausendefinitionen und -zuweisungen verwalten kann, muss die Erweiterung hinzugefügt werden.
Diese Erweiterung funktioniert überall, wo die Azure-Befehlszeilenschnittstelle verwendet werden kann, einschließlich [bash unter Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (sowohl eigenständig als auch innerhalb des Portals), [Azure CLI-Docker-Image](https://hub.docker.com/_/microsoft-azure-cli) oder lokaler Installation.

1. Vergewissern Sie sich, dass die neueste Azure CLI (mindestens **2.0.76**) installiert ist. Falls es noch nicht installiert ist, befolgen Sie [diese Anweisungen](/cli/azure/install-azure-cli-windows).

1. Importieren Sie sie mit folgendem Befehl in die Azure CLI-Umgebung Ihrer Wahl:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Überprüfen Sie, ob die Erweiterung installiert wurde und in der erwarteten Version vorliegt (mindestens **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Erstellen einer Blaupause

Im ersten Schritt beim Definieren eines Standardmusters für die Konformität wird eine Blaupause aus den verfügbaren Ressourcen erstellt. Wir erstellen eine Blaupause namens „MyBlueprint“, um Rollen- und Richtlinienzuweisungen für das Abonnement zu konfigurieren. Anschließend fügen wir eine Ressourcengruppe, eine ARM-Vorlage und eine Rollenzuweisung für die Ressourcengruppe hinzu.

> [!NOTE]
> Bei Verwendung von Azure CLI wird als Erstes das Objekt _blueprint_ erstellt. Für jedes hinzugefügte _Artefakt_, das über Parameter verfügt, müssen die Parameter vorab in der anfänglichen _Blaupause_ definiert werden.

1. Erstellen Sie das anfängliche _blueprint_-Objekt. Der **parameters**-Parameter nimmt eine JSON-Datei an, die alle Parameter der Blaupausenebene enthält. Die Parameter werden im Rahmen der Zuweisung festgelegt und von den in späteren Schritten hinzugefügten Artefakten verwendet.

   - JSON-Datei: blueprintparms.json

     ```json
     {
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
     }
     ```

   - Azure CLI-Befehl

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Verwenden Sie den Dateinamen _blueprint.json_, wenn Sie Ihre Blaupausendefinitionen importieren.
     > Dieser Dateiname wird beim Aufrufen von [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import) verwendet.

     Das Blaupausenobjekt wird standardmäßig im Standardabonnement erstellt. Verwenden Sie zum Angeben der Verwaltungsgruppe den Parameter **managementgroup**. Verwenden Sie zum Angeben des Abonnements den Parameter **subscription**.

1. Fügen Sie die Ressourcengruppe für die Speicherartefakte der Definition hinzu.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Fügen Sie eine Rollenzuweisung für das Abonnement hinzu. Im Beispiel unten ist für die Prinzipalbezeichner, denen die angegebene Rolle zugewiesen wird, ein Parameter konfiguriert, der bei der Blaupausenzuweisung festgelegt wird. In diesem Beispiel wird die integrierte Rolle _Mitwirkender_ mit der GUID `b24988ac-6180-42a0-ab88-20f7382dd24c` verwendet.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Fügen Sie eine Richtlinienzuweisung für das Abonnement hinzu. In diesem Beispiel wird die integrierte Richtlinie _Tag und dessen Standardwert auf Ressourcengruppen anwenden_ mit der `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` verwendet.

   - JSON-Datei: artifacts\policyTags.json

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Azure CLI-Befehl

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. Fügen Sie eine weitere Richtlinienzuweisung für das Storage-Tag (unter Wiederverwendung des Parameters _storageAccountType_) für das Abonnement hinzu. Dieses zusätzliche Artefakt der Richtlinienzuweisung veranschaulicht, dass ein für die Blaupause definierter Parameter von mehreren Artefakten verwendet werden kann. In dem Beispiel wird **storageAccountType** verwendet, um ein Tag für die Ressourcengruppe festzulegen. Dieser Wert stellt Informationen zum im nächsten Schritt erstellten Speicherkonto bereit. In diesem Beispiel wird die integrierte Richtlinie _Tag und dessen Standardwert auf Ressourcengruppen anwenden_ mit der `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` verwendet.

   - JSON-Datei: artifacts\policyStorageTags.json

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Azure CLI-Befehl

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. Fügen Sie eine Vorlage unter der Ressourcengruppe hinzu. Der Parameter **template** für eine ARM-Vorlage enthält die normale JSON-Komponente der Vorlage. Darüber hinaus verwendet die Vorlage auch die Vorlagenparameter **storageAccountType**, **tagName** und **tagValue** wieder, indem sie jeweils an die Vorlage übergeben werden. Die Blaupausenparameter werden für die Vorlage mithilfe des Parameters **parameters** bereitgestellt, und innerhalb des JSON-Codes der Vorlage wird der Wert mithilfe dieses Schlüssel-Wert-Paars eingefügt. Die Namen des Blaupausen- und des Vorlagenparameters können identisch sein.

   - JSON-ARM-Vorlagendatei: artifacts\templateStorage.json

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

   - JSON-ARM-Vorlagenparameterdatei: artifacts\templateStorageParams.json

     ```json
     {
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
     ```

   - Azure CLI-Befehl

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. Fügen Sie eine Rollenzuweisung unter der Ressourcengruppe hinzu. Ähnlich wie beim vorherigen Rollenzuweisungseintrag wird im folgenden Beispiel der Definitionsbezeichner für die Rolle **Besitzer** verwendet und dafür ein anderer Parameter aus der Blaupause angegeben. In diesem Beispiel wird die integrierte Rolle _Besitzer_ mit der GUID `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` verwendet.

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Veröffentlichen einer Blaupause

Nach dem Hinzufügen der Artefakte zur Blaupause kann diese veröffentlicht werden. Durch die Veröffentlichung kann sie einem Abonnement zugewiesen werden.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

Der Wert für `{BlueprintVersion}` ist eine Zeichenfolge mit Buchstaben, Zahlen und Bindestrichen (keine Leerzeichen oder Sonderzeichen) mit einer maximalen Länge von 20 Zeichen. Verwenden Sie einen eindeutigen und aussagekräftigen Wert, z. B. **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Zuweisen einer Blaupause

Nach dem Veröffentlichen einer Blaupause mit der Azure CLI kann sie einem Abonnement zugewiesen werden. Weisen Sie die erstellte Blaupause einem der Abonnements unter Ihrer Verwaltungsgruppenhierarchie zu. Wenn die Blaupause in einem Abonnement gespeichert wird, kann sie nur diesem Abonnement zugewiesen werden. Der Parameter **blueprint-name** gibt die zuzuweisende Blaupause an. Verwenden Sie zum Angeben von Parametern für Name, Speicherort, Identität, Sperrung und Blaupause die entsprechenden Azure CLI-Parameter im `az blueprint assignment create`-Befehl, oder geben Sie sie in der JSON-Datei **parameters** an.

1. Führen Sie die Blaupausenbereitstellung aus, indem Sie sie einem Abonnement zuweisen. Da für die Parameter **Mitwirkende** und **Besitzer** ein Array von Objekt-IDs der Prinzipale, denen die Rollenzuweisung erteilt wird, erforderlich ist, verwenden Sie die [Azure Active Directory Graph-API](../../active-directory/develop/active-directory-graph-api.md) zum Sammeln der Objekt-IDs zur Verwendung in **parameters** für Ihre eigenen Benutzer, Gruppen oder Dienstprinzipale.

   - JSON-Datei: blueprintAssignment.json

     ```json
     {
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
     ```

   - Azure CLI-Befehl

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Benutzerseitig zugewiesene verwaltete Identität

     Eine Blaupausenzuweisung kann auch eine [benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) verwenden.
     In diesem Fall wird der **identity-type**-Parameter auf _UserAssigned_ festgelegt, und der **user-assigned-identities**-Parameter gibt die Identität an. Ersetzen Sie `{userIdentity}` durch den Namen Ihrer vom Benutzer zugewiesenen verwalteten Identität.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     Die **benutzerseitig zugewiesene verwaltete Identität** kann sich in einem beliebigen Abonnement/in einer beliebigen Ressourcengruppe befinden, für das/für die der Benutzer, der die Blaupause zuweist, über Berechtigungen verfügt.

     > [!IMPORTANT]
     > Die benutzerseitig zugewiesene verwaltete Identität wird nicht von Azure Blueprints verwaltet. Benutzer müssen angemessene Rollen und Berechtigungen zuweisen. Andernfalls ist die Blaupausenzuweisung nicht erfolgreich.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

### <a name="unassign-a-blueprint"></a>Aufheben der Zuweisung einer Blaupause

Sie können eine Blaupause aus einem Abonnement entfernen. Dieser Schritt wird häufig ausgeführt, wenn die Artefaktressourcen nicht mehr benötigt werden. Wenn eine Blaupause entfernt wird, werden die als Teil der Blaupause zugewiesenen Artefakte beibehalten. Verwenden Sie zum Entfernen einer Blaupausenzuweisung den `az blueprint assignment delete`-Befehl:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Blaupause mit Azure CLI erstellt, zugewiesen und entfernt. Weitere Informationen zu Azure Blueprints finden Sie im Artikel zum Lebenszyklus von Blaupausen.

> [!div class="nextstepaction"]
> [Informationen zum Lebenszyklus von Blaupausen](./concepts/lifecycle.md)