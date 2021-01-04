---
title: Reparieren eines fehlerhaften Azure Automanage-Kontos
description: Wenn Sie vor kurzem ein Abonnement mit einem Automanage-Konto in einen neuen Mandanten verschoben haben, müssen Sie es neu konfigurieren. Dieser Artikel enthält eine entsprechende Anleitung.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183701"
---
# <a name="repair-an-automanage-account"></a>Reparieren eines Automanage-Kontos
Ihr [Azure Automanage-Konto](./automanage-virtual-machines.md#automanage-account) ist der Sicherheitskontext bzw. die Identität, unter dem bzw. unter der die automatisierten Vorgänge ausgeführt werden. Wenn Sie vor kurzem ein Abonnement mit einem Automanage-Konto in einen neuen Mandanten verschoben haben, müssen Sie das Konto neu konfigurieren. Hierzu müssen Sie den Identitätstyp zurücksetzen und die entsprechenden Rollen für das Konto zuweisen.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>Schritt 1: Zurücksetzen des Identitätstyps des Automanage-Kontos
Setzen Sie den Identitätstyp des Automanage-Kontos mithilfe der folgenden ARM-Vorlage (Azure Resource Manager) zurück. Speichern Sie die Datei lokal unter „armdeploy.json“ oder unter einem ähnlichen Namen. Notieren Sie sich Name und Ort Ihres Automanage-Kontos, da es sich bei diesen Angaben um erforderliche Parameter in der ARM-Vorlage handelt.

1. Erstellen Sie mithilfe der folgenden Vorlage eine Resource Manager-Bereitstellung. Verwenden Sie `identityType = None`.
    * Die Bereitstellung kann über die Azure CLI mithilfe von `az deployment sub create` erstellt werden. Weitere Informationen finden Sie unter [az deployment sub](/cli/azure/deployment/sub).
    * Die Bereitstellung kann per PowerShell mithilfe des Moduls `New-AzDeployment` erstellt werden. Weitere Informationen finden Sie unter [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Führen Sie die gleiche ARM-Vorlage noch einmal mit `identityType = SystemAssigned` aus.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Schritt 2: Zuweisen geeigneter Rollen für das Automanage-Konto
Das Automanage-Konto muss für das Abonnement mit den virtuellen Computern, die von Automanage verwaltet werden, über die Rollen „Mitwirkender“ und „Mitwirkender bei Ressourcenrichtlinien“ verfügen. Diese Rollen können über das Azure-Portal, mithilfe von ARM-Vorlagen oder per Azure CLI zugewiesen werden.

Bei Verwendung einer ARM-Vorlage oder der Azure CLI benötigen Sie die Prinzipal-ID (oder auch Objekt-ID) Ihres Automanage-Kontos. (Bei Verwendung des Azure-Portals wird diese ID nicht benötigt.) Diese ID finden Sie wie folgt:

- [Azure CLI](/cli/azure/ad/sp): Verwenden Sie den Befehl `az ad sp list --display-name <name of your Automanage Account>`.

- Azure-Portal: Navigieren Sie zu **Azure Active Directory**, und suchen Sie anhand des Namens nach Ihrem Automanage-Konto. Wählen Sie unter **Unternehmensanwendungen** den Namen des Automanage-Kontos aus, wenn es angezeigt wird.

### <a name="azure-portal"></a>Azure-Portal
1. Navigieren Sie unter **Abonnements** zu dem Abonnement, das Ihre automatisch verwalteten virtuellen Computer enthält.
1. Wechseln Sie zu **Zugriffssteuerung (IAM)** .
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie die Rolle **Mitwirkender** aus, und geben Sie den Namen Ihres Automanage-Kontos ein.
1. Wählen Sie **Speichern** aus.
1. Wiederholen Sie die Schritte 3 bis 5 mit der Rolle **Mitwirkender bei Ressourcenrichtlinien**.

### <a name="arm-template"></a>ARM-Vorlage
Führen Sie die folgende ARM-Vorlage aus. Sie benötigen die Prinzipal-ID Ihres Automanage-Kontos. Die Schritte zum Abrufen dieses Werts finden Sie am Anfang dieses Abschnitts. Geben Sie die ID ein, wenn Sie dazu aufgefordert werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Führen Sie diese Befehle aus:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Automanage finden Sie [hier](./automanage-virtual-machines.md).