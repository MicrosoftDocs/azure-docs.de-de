---
title: Erstellen einer benutzerdefinierten Azure-Rolle mithilfe einer Azure Resource Manager-Vorlage – Azure RBAC
description: Erfahren Sie, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM) und der rollenbasierten Zugriffssteuerung (Azure RBAC) eine benutzerdefinierte Azure-Rolle erstellen.
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: 96dfdc0a1c32237c55d4e65bb25989656e2a4ad2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097021"
---
# <a name="create-an-azure-custom-role-using-an-arm-template"></a>Erstellen einer benutzerdefinierten Rolle in Azure mithilfe einer ARM-Vorlage

Wenn die [integrierten Azure-Rollen](built-in-roles.md) die Anforderungen Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen [benutzerdefinierten Rollen](custom-roles.md) erstellen. In diesem Artikel wird beschrieben, wie eine benutzerdefinierte Rolle mithilfe einer Azure Resource Manager-Vorlage (ARM) erstellt wird.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Zum Erstellen einer benutzerdefinierten Rolle geben Sie einen Rollennamen, Berechtigungen und den Ort an, an dem die Rolle verwendet werden kann. In diesem Artikel erstellen Sie eine Rolle namens _Custom Role – RG Reader_ mit Ressourcenberechtigungen, die in einem Abonnementbereich oder einer niedrigeren Ebene zugewiesen werden können.

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen einer benutzerdefinierten Rolle benötigen Sie Folgendes:

- Berechtigungen zum Erstellen von benutzerdefinierten Rollen, wie z. B. [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator)

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in diesem Artikel verwendete Vorlage stammt aus den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/create-role-def). Die Vorlage hat vier Parameter und einen Ressourcenabschnitt. Die vier Parameter sind:

- Array von Aktionen mit dem Standardwert `["Microsoft.Resources/subscriptions/resourceGroups/read"]`
- Array von `notActions` mit einem leeren Standardwert
- Rollenname mit dem Standardwert `Custom Role - RG Reader`
- Rollenbeschreibung mit dem Standardwert `Subscription Level Deployment of a Role Definition`

Der Bereich, in dem diese benutzerdefinierte Rolle zugeordnet werden kann, wird auf das aktuelle Abonnement festgelegt.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

In der Vorlage ist die folgende Ressource definiert:

- [Microsoft.Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Führen Sie diese Schritte aus, um die vorherige Vorlage bereitzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie Azure Cloud Shell für PowerShell.

1. Kopieren Sie das folgende Skript, und fügen Sie es in Cloud Shell ein.

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Geben Sie einen Standort für die Bereitstellung ein, z. B. *centralus*.

1. Geben Sie eine Liste von Aktionen für die benutzerdefinierte Rolle als durch Trennzeichen getrennte Liste ein, z. B. *Microsoft.Resources/resources/read,Microsoft.Resources/subscriptions/resourceGroups/read*.

1. Drücken Sie ggf. die EINGABETASTE, um den Befehl `New-AzDeployment` auszuführen.

    Der Befehl [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) stellt die Vorlage zum Erstellen der benutzerdefinierten Rolle bereit.

    Die Ausgabe sollte etwa folgendermaßen aussehen:

    ```azurepowershell-interactive
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions

    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0

    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition

    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Führen Sie diese Schritte aus, um sicherzustellen, dass die benutzerdefinierte Rolle erstellt wurde.

1. Führen Sie den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) aus, um die benutzerdefinierte Rolle aufzulisten.

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Die Ausgabe sollte etwa folgendermaßen aussehen:

    ```azurepowershell-interactive
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Öffnen Sie im Azure-Portal Ihr Abonnement.

1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie die Registerkarte **Rollen** aus.

1. Legen Sie die Liste **Typ** auf **CustomRole** fest.

1. Stellen Sie sicher, dass die Rolle **Benutzerdefinierte Rolle – RG Reader** aufgeführt ist.

   ![Neue benutzerdefinierte Rolle im Azure-Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die benutzerdefinierte Rolle zu entfernen, führen Sie die folgenden Schritte aus.

1. Führen Sie den folgenden Befehl aus, um die benutzerdefinierte Rolle zu entfernen.

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Geben Sie **J** ein, um zu bestätigen, dass Sie die benutzerdefinierte Rolle entfernen möchten.

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zu Azure-Rollendefinitionen](role-definitions.md)
- [Schnellstart: Hinzufügen einer Azure-Rollenzuweisung mithilfe einer Azure Resource Manager-Vorlage](quickstart-role-assignments-template.md)
- [Dokumentation zu ARM-Vorlagen](../azure-resource-manager/templates/index.yml)
