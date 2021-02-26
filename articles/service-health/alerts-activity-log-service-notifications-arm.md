---
title: Empfangen von Aktivitätsprotokollwarnungen zu Azure-Dienstbenachrichtigungen per Resource Manager-Vorlage
description: Lassen Sie sich per SMS, E-Mail oder Webhook benachrichtigen, wenn Ereignisse beim Azure-Dienst eintreten.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 532fbae505e0bcaa6ab31a2e935362114537d134
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594958"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Schnellstart: Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen mit einer ARM-Vorlage

In diesem Artikel wird veranschaulicht, wie Sie Aktivitätsprotokollwarnungen für Benachrichtigungen zur Dienstintegrität einrichten, indem Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Benachrichtigungen zur Dienstintegrität werden im [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/platform-logs-overview.md) gespeichert. Angesichts der möglicherweise großen Menge an Informationen, die im Aktivitätsprotokoll gespeichert werden, ist eine separate Benutzeroberfläche vorhanden, um die Anzeige und Einrichtung von Benachrichtigungen zur Dienstintegrität zu erleichtern.

Sie können eine Warnung erhalten, wenn Azure Benachrichtigungen zur Dienstintegrität an Ihr Azure-Abonnement sendet. Sie können die Warnung konfigurieren auf der Grundlage von:

- Der Klasse der Dienstintegritätsbenachrichtigung (Dientsprobleme, Geplante Wartung, Integritätsempfehlungen).
- Dem betroffenen Abonnement.
- Den betroffenen Diensten.
- Den betroffenen Regionen.

> [!NOTE]
> Dienstintegritätsbenachrichtigungen senden keine Warnung bezüglich Ereignissen der Ressourcenintegrität.

Sie können auch konfigurieren, an wen die Warnung gesendet werden soll:

- Wählen Sie eine vorhandene Aktionsgruppe aus.
- Erstellen Sie eine neue Aktionsgruppe (die für zukünftige Warnungen verwendet werden kann).

Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Installieren Sie die Azure CLI oder die Azure PowerShell-Module, um die Befehle auf Ihrem lokalen Computer auszuführen. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) und [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mit der folgenden Vorlage wird eine Aktionsgruppe mit einem E-Mail-Ziel erstellt, und alle Dienstintegritätsbenachrichtigungen für das Zielabonnement werden aktiviert. Speichern Sie diese Vorlage als *CreateServiceHealthAlert.json*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

Die Vorlage definiert zwei Ressourcen:

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die Vorlage mit einer Standardmethode zur [Bereitstellung einer ARM-Vorlage](../azure-resource-manager/templates/deploy-portal.md) bereit, z. B. wie in den folgenden Beispielen per CLI und PowerShell. Ersetzen Sie die Beispielwerte für **Resource Group** und **emailAddress** durch die entsprechenden Werte für Ihre Umgebung.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Überprüfen Sie mit einem der folgenden Befehle, ob der Arbeitsbereich erstellt wurde. Ersetzen Sie die Beispielwerte für **Resource Group** durch den Wert, den Sie oben verwendet haben.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- Erfahren Sie mehr über [bewährte Methoden zum Einrichten von Azure Service Health-Warnungen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Erfahren Sie, wie Sie [mobile Pushbenachrichtigungen für Azure Service Health einrichten](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Erfahren Sie, wie Sie [Webhookbenachrichtigungen für vorhandene Problemverwaltungssysteme konfigurieren](service-health-alert-webhook-guide.md).
- Weitere Informationen zu [Dienstintegritätsbenachrichtigungen](service-notifications.md).
- Erfahren Sie mehr über [Ratenlimits für SMS, E-Mail-Nachrichten und Webhooks](../azure-monitor/alerts/alerts-rate-limiting.md).
- Weitere Informationen zum [Webhookschema für Aktivitätsprotokollwarnungen](../azure-monitor/alerts/activity-log-alerts-webhook.md).
- Verschaffen Sie sich eine [Übersicht über Aktivitätsprotokollwarnungen](../azure-monitor/alerts/alerts-overview.md), und erfahren Sie, wie Sie Warnungen empfangen können.
- Weitere Informationen zu [Aktionsgruppen](../azure-monitor/alerts/action-groups.md).
