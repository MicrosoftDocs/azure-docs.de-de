---
title: Erstellen von Azure Advisor-Warnungen für neue Empfehlungen per Resource Manager-Vorlage
description: Hier wird veranschaulicht, wie Sie eine Warnung für neue Empfehlungen von Azure Advisor über eine Azure Resource Manager-Vorlage (ARM-Vorlage) einrichten.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 7b10ad06e5397ab733987373a3bd18de981c8c97
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590066"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Schnellstart: Erstellen von Azure Advisor-Warnungen für neue Empfehlungen per ARM-Vorlage

In diesem Artikel wird veranschaulicht, wie Sie eine Warnung für neue Empfehlungen von Azure Advisor über eine Azure Resource Manager-Vorlage (ARM-Vorlage) einrichten.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Sobald Azure Advisor eine neue Empfehlung für eine Ihrer Ressourcen erkennt, wird ein Ereignis im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md) gespeichert. Sie können Warnungen für diese Ereignisse von Azure Advisor mithilfe einer Benutzeroberfläche für die Erstellung empfehlungsspezifischer Warnungen einrichten. Sie können ein Abonnement und optional eine Ressourcengruppe auswählen, um die Ressourcen anzugeben, für die Sie Warnungen erhalten möchten.

Sie können auch die Arten von Empfehlungen mithilfe der folgenden Eigenschaften festlegen:

- Category
- Auswirkungsstufe
- Empfehlungstyp

Sie können auch die Aktion konfigurieren, die bei Auslösen einer Warnung durch folgende Ereignisse ausgeführt wird:

- Auswählen einer vorhandenen Aktionsgruppe
- Erstellen einer neuen Aktionsgruppe

Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md).

> [!NOTE]
> Advisor-Warnungen stehen derzeit nur für Hochverfügbarkeits-, Leistungs- und Kostenempfehlungen zur Verfügung. Sicherheitsempfehlungen werden nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Installieren Sie die Azure CLI oder die Azure PowerShell-Module, um die Befehle auf Ihrem lokalen Computer auszuführen. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) und [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mit der folgenden Vorlage wird eine Aktionsgruppe mit einem E-Mail-Ziel erstellt, und alle Dienstintegritätsbenachrichtigungen für das Zielabonnement werden aktiviert. Speichern Sie diese Vorlage als *CreateAdvisorAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
```

Die Vorlage definiert zwei Ressourcen:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage mit einer Standardmethode zur [Bereitstellung einer ARM-Vorlage](../azure-resource-manager/templates/deploy-portal.md) bereit, z. B. wie in den folgenden Beispielen per CLI und PowerShell. Ersetzen Sie die Beispielwerte für **Resource Group** und **emailAddress** durch die entsprechenden Werte für Ihre Umgebung. Der Name des Arbeitsbereichs muss für alle Azure-Abonnements eindeutig sein.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Überprüfen Sie mit einem der folgenden Befehle, ob der Arbeitsbereich erstellt wurde. Ersetzen Sie die Beispielwerte für **Resource Group** durch den Wert, den Sie oben verwendet haben.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen. Löschen Sie die Ressourcengruppe, wenn Sie die Ressourcen nicht mehr benötigen. Hierdurch werden die Warnungsregel und die zugehörigen Ressourcen gelöscht. Löschen der Ressourcengruppe per Azure-Befehlszeilenschnittstelle oder Azure PowerShell

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Nächste Schritte

- Verschaffen Sie sich eine [Übersicht über Aktivitätsprotokollwarnungen](../azure-monitor/alerts/alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.
- Weitere Informationen zu [Aktionsgruppen](../azure-monitor/alerts/action-groups.md).
