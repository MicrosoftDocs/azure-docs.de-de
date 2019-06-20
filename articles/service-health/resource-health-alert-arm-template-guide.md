---
title: Konfigurieren von Integritätswarnungen für Azure-Ressourcen mithilfe von Resource Manager-Vorlagen | Microsoft-Dokumentation
description: Erstellen Sie programmgesteuert Warnungen, die Sie informieren, wenn Ihre Azure-Ressourcen nicht mehr verfügbar sind.
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 9/4/2018
ms.openlocfilehash: e552b90a4c98eff7b04cbeaef26aa10749ce672c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66493394"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Konfigurieren von Ressourcenintegritätswarnungen mithilfe von Resource Manager-Vorlagen

In diesem Artikel wird gezeigt, wie Sie mithilfe von Azure Resource Manager-Vorlagen und Azure PowerShell programmgesteuert Resource Health-Aktivitätsprotokollwarnungen erstellen.

Azure Resource Health informiert Sie über den aktuellen und den vergangenen Integritätsstatus Ihrer Azure-Ressourcen. Azure Resource Health-Warnungen können Sie nahezu in Echtzeit informieren, wenn sich der Integritätsstatus dieser Ressourcen ändert. Die programmgesteuerte Erstellung von Resource Health-Warnungen ermöglicht Benutzern das Massenerstellen und -anpassen von Warnungen.

> [!NOTE]
> Resource Health-Warnungen sind derzeit als Vorschau verfügbar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Anweisungen auf dieser Seite ausführen können, müssen Sie vorab einige Komponenten einrichten:

1. Sie müssen das [Azure PowerShell-Modul](https://docs.microsoft.com/powershell/azure/install-Az-ps) installieren.
2. Sie können [eine Aktionsgruppe erstellen oder wiederverwenden](../azure-monitor/platform/action-groups.md), die so konfiguriert ist, dass Sie benachrichtigt werden.

## <a name="instructions"></a>Anleitung
1. Melden Sie sich unter Verwendung von PowerShell und Ihres Kontos bei Azure an, und wählen Sie das gewünschte Abonnement aus.

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > Sie können mit `Get-AzSubscription` die Abonnements auflisten, auf die Sie Zugriff haben.

2. Suchen Sie die vollständige Azure Resource Manager-ID für Ihre Aktionsgruppe, und speichern Sie sie.

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Erstellen Sie eine Resource Manager-Vorlage für Resource Health-Warnungen, und speichern Sie sie unter `resourcehealthalert.json`. ([Details siehe unten](#resource-manager-template-options-for-resource-health-alerts))

4. Erstellen Sie mit dieser Vorlage eine neue Azure Resource Manager-Bereitstellung.

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. Sie werden zur Eingabe des Warnungsnamen und der Ressourcen-ID der Aktionsgruppe aufgefordert, die Sie zuvor kopiert haben:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Wurden alle Schritte erfolgreich ausgeführt, erhalten Sie eine Bestätigung in PowerShell.

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Hinweis: Wenn Sie diesen Prozess vollständig automatisieren möchten, müssen Sie einfach die Resource Manager-Vorlage so bearbeiten, dass in Schritt 5 nicht zur Eingabe der Werte aufgefordert wird.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Resource Manager-Vorlagenoptionen für Resource Health-Warnungen

Sie können diese Basisvorlage als Ausgangspunkt zum Erstellen von Resource Health-Warnungen verwenden. Diese Vorlage funktioniert wie vorliegend, und Sie werden für den Empfang von Warnungen für alle neu aktivierten Ressourcenintegritätsereignisse in allen Ressourcen eines Abonnements registriert.

> Am Ende dieses Artikels finden Sie außerdem eine komplexere Warnungsvorlage, die das Signal-Rausch-Verhältnis für Resource Health-Warnungen im Vergleich zu dieser Vorlage erhöht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Eine allgemeine Vorlage wie diese wird im Allgemeinen jedoch nicht empfohlen. Im Anschluss erfahren Sie, wie Sie diese Warnung weiter einschränken, um sich auf die für uns relevanten Ereignisse zu konzentrieren.

### <a name="adjusting-the-alert-scope"></a>Anpassen des Warnungsbereichs

Resource Health-Warnungen können so konfiguriert werden, dass sie Ereignisse in drei verschiedenen Bereichen überwachen:

 * Abonnementebene
 * Ressourcengruppenebene
 * Ressourcenebene

Die Warnungsvorlage wird auf Abonnementebene konfiguriert. Wenn Sie die Warnung jedoch so konfigurieren möchten, dass Sie nur über bestimmte Ressourcen oder Ressourcen in einer bestimmten Ressourcengruppe benachrichtigt werden, müssen Sie lediglich den Abschnitt `scopes` in der Vorlage oben ändern.

Für einen Bereich auf Ressourcengruppenebene sieht der Abschnitt „scopes“ etwa wie folgt aus:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

Für einen Bereich auf Ressourcenebene sieht der Abschnitt „scopes“ etwa wie folgt aus:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Beispiel: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> Diese Zeichenfolge erhalten Sie, indem Sie Ihre Azure-Ressource im Azure-Portal anzeigen und sich die URL ansehen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>Anpassen der Ressourcentypen, die Sie benachrichtigen

Warnungen auf Abonnement- oder Ressourcengruppenebene enthalten unter Umständen unterschiedliche Ressourcen. Wenn Sie Warnungen auf eine bestimmte Teilmenge von Ressourcentypen beschränken möchten, können Sie dies im Abschnitt `condition` der Vorlage festlegen. Beispiel:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Compute/virtualMachines",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "Microsoft.Storage/storageAccounts",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Hier wird mit dem Wrapper `anyOf` zugelassen, dass die Ressourcenintegritätswarnung einer der angegebenen Bedingungen entspricht. Dadurch werden Warnungen für bestimmte Ressourcentypen ermöglicht.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>Anpassen der Resource Health-Ereignisse, die Sie benachrichtigen
Wenn für Ressourcen ein Integritätsereignis auftritt, können sie eine Reihe von Phasen durchlaufen, die den Status des Integritätsereignisses darstellen: `Active`, `InProgress`, `Updated` und `Resolved`.

Sie möchten möglicherweise nur benachrichtigt werden, wenn eine Ressource fehlerhaft ist. In diesem Fall sollten Sie Ihre Warnung so konfigurieren, dass Sie nur benachrichtigt werden, wenn für `status` der Wert `Active` lautet. Wenn Sie jedoch auch über die anderen Phasen benachrichtigt werden möchten, können Sie diese Details wie folgt hinzufügen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "InProgress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Wenn Sie bei allen vier Phasen der Integritätsereignisse informiert werden möchten, können Sie diese Bedingung vollständig entfernen, und Sie werden unabhängig von der Eigenschaft `status` benachrichtigt.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>Anpassen der Resource Health-Warnungen, um Ereignisse vom Typ „Unknown“ (Unbekannt) zu vermeiden

Azure Resource Health führt zur Überwachung Ihrer Ressourcen kontinuierlich Testläufe durch und hält Sie so über die aktuelle Integrität Ihrer Ressourcen auf dem Laufenden. Die relevanten gemeldeten Integritätsstatus lauten: „Verfügbar“, „Nicht verfügbar“ und „Heruntergestuft“. Wenn der Testlauf und die Azure-Ressource nicht kommunizieren können, wird für die Ressource der Integritätsstatus „Unknown“ gemeldet, und dieser Status wird als Integritätsereignis vom Typ „Active“ (Aktiv) betrachtet.

Wenn für eine Ressource jedoch „Unknown“ gemeldet wird, besteht die Wahrscheinlichkeit, dass sich ihr Integritätsstatus seit dem letzten korrekten Bericht nicht geändert hat. Wenn Sie Warnungen für Ereignisse vom Typ „Unknown“ vermeiden möchten, können Sie die folgende Logik in die Vorlage aufnehmen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

In diesem Beispiel wird nur bei Ereignissen eine Benachrichtigung gesendet, deren aktueller und vorheriger Integritätsstatus nicht „Unknown“ lautet. Diese Änderung kann eine nützliche Ergänzung sein, wenn Ihre Warnungen direkt an Ihr Mobiltelefon oder Ihre mobile E-Mail gesendet wird. 

Hinweis: Die Eigenschaften „currentHealthStatus“ und „previousHealthStatus“ können bei bestimmten Ereignissen den Wert NULL haben. Wenn beispielsweise ein Ereignis mit dem Zustand „Updated“ auftritt, ist es sehr wahrscheinlich, dass sich der Integritätsstatus der Ressource seit dem letzten Bericht nicht geändert hat und nur zusätzliche Ereignisinformationen wie das Feld „cause“ verfügbar sind. Deshalb werden möglicherweise einige Warnungen nicht ausgelöst, wenn die obige Klausel verwendet wird. Dies liegt daran, dass die Werte der Eigenschaften „properties.currentHealthStatus“ und „properties.previousHealthStatus“ dann auf NULL festgelegt sind.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>Anpassen der Warnung, um vom Benutzer initiierte Ereignisse zu vermeiden

Resource Health-Ereignisse können durch Ereignisse ausgelöst werden, die von der Plattform oder vom Benutzer initiiert wurden. Es kann sinnvoll sein, nur eine Benachrichtigung zu senden, wenn das Integritätsereignis von der Azure-Plattform verursacht wird.

Die Warnung kann einfach so konfiguriert werden, dass sie nur nach diesen Ereignistypen filtert:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Hinweis: Das Feld „cause“ darf bei einigen Ereignissen nicht den Wert NULL haben. Das bedeutet, dass sich der Integritätsstatus ändert (z. B. von „Verfügbar“ in „Nicht verfügbar“) und das Ereignis sofort protokolliert wird, um Verzögerungen von Benachrichtigungen zu vermeiden. Daher kann es sein, dass keine Warnung ausgelöst wird, wenn Sie die obige Klausel verwenden, weil der Eigenschaftswert „properties.clause“ auf NULL festgelegt ist.

## <a name="complete-resource-health-alert-template"></a>Abschließen einer Resource Health-Warnungsvorlage

Nachfolgend finden Sie eine Beispielvorlage mit den im vorherigen Abschnitt beschriebenen Anpassungen, die für die Maximierung des Signal-Rausch-Verhältnisses konfiguriert ist. Denken Sie daran, dass die Eigenschaften „currentHealthStatus“ und „previousHealthStatus“ sowie die Eigenschaftswerte für „cause“ wie weiter oben beschrieben bei einigen Ereignissen den Wert NULL haben können.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "InProgress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

Sie können jedoch selbst am besten einschätzen, welche Konfigurationen effektiv sind. Nehmen Sie daher mit den in dieser Dokumentation vorgestellten Methoden eigene Anpassungen vor.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Resource Health:
-  [Übersicht über Azure Resource Health](Resource-health-overview.md)
-  [Über Azure Resource Health verfügbare Ressourcentypen und Integritätsüberprüfungen](resource-health-checks-resource-types.md)


Erstellen von Service Health-Warnungen:
-  [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Ereignisschema des Azure-Aktivitätsprotokolls](../azure-monitor/platform/activity-log-schema.md)
