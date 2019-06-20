---
title: Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen in Azure Monitor
description: Hier erfahren Sie, wie Sie Aktivitätsprotokollwarnungen über das Azure-Portal, mithilfe einer Azure Resource Manager-Vorlage und mithilfe von Azure PowerShell erstellen.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: f25321fa5a13ed5a39a62a4115bb0bc10306d36f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244955"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor  

## <a name="overview"></a>Übersicht
Aktivitätsprotokollwarnungen werden aktiviert, wenn ein neues Aktivitätsprotokollereignis eintritt, das die in der Warnung angegebenen Bedingungen erfüllt.

Diese Warnungen gelten für Azure-Ressourcen und können mit einer Azure Resource Manager-Vorlage erstellt werden. Sie können auch im Azure-Portal erstellt, aktualisiert oder gelöscht werden. Normalerweise erstellen Sie Aktivitätsprotokollwarnungen, um Benachrichtigungen zu erhalten, wenn es für Ressourcen in Ihrem Azure-Abonnement zu spezifischen Änderungen kommt, die häufig für bestimmte Ressourcengruppen oder Ressourcen gelten. Es kann beispielsweise sein, dass Sie benachrichtigt werden möchten, wenn ein virtueller Computer in **myProductionResourceGroup** (Beispielressourcengruppe) gelöscht wird oder wenn einem Benutzer Ihres Abonnements neue Rollen zugewiesen werden.

> [!IMPORTANT]
> Warnungen zur Service Health-Benachrichtigung können nicht über die Schnittstelle zur Erstellung von Aktivitätsprotokollwarnungen erstellt werden. Weitere Informationen zum Erstellen und Verwenden von Dienstintegritätsbenachrichtigungen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Azure-Portal

> [!NOTE]
> 
>  Stellen Sie beim Erstellen der Warnungsregeln Folgendes sicher:
> 
> - Das Abonnement im Bereich unterscheidet sich nicht von dem Abonnement, unter dem die Warnung erstellt wird.
> - Folgende Kriterien müssen verwendet werden: Ebene/Status/Aufrufer/Ressourcengruppe/Ressourcen-ID/Ressourcentyp/Ereigniskategorie, unter der die Warnung konfiguriert wird.
> - Der JSON-Code der Warnungskonfiguration enthält keine „anyOf“-Bedingung oder geschachtelten Bedingungen (nur ein „allOf“-Element ohne weitere „allOf“/„anyOf“-Elemente ist zulässig).
> - Wenn die Kategorie „Administration“ lautet. Sie müssen mindestens eine der oben genannten Kriterien in der Warnung angeben. Eine Warnung, die jedes Mal aktiviert wird, wenn ein Ereignis in den Aktivitätsprotokollen erstellt wird, kann nicht erstellt werden.

### <a name="create-with-azure-portal"></a>Erstellen mit dem Azure-Portal

Gehen Sie dazu wie folgt vor:

1. Wählen Sie im Azure-Portal die Option **Überwachen** > **Warnungen**.
2. Klicken Sie oben im Fenster **Warnungen** auf **Neue Warnungsregel**.

     ![Neue Warnungsregel](media/alerts-activity-log/AlertsPreviewOption.png)

     Das Fenster **Regel erstellen** wird angezeigt.

      ![Optionen für neue Warnungsregel](media/alerts-activity-log/create-new-alert-rule-options.png)

3. Geben Sie unter **Warnungsbedingung definieren** die folgenden Informationen an, und klicken Sie auf **Fertig**.

   - **Warnungsziel:** Verwenden Sie zum Anzeigen und Auswählen des Ziels für die neue Warnung die Option **Nach Abonnement filtern** / **Nach Ressourcentyp filtern**, und wählen Sie die Ressource oder Ressourcengruppe aus der angegebenen Liste aus.

     > [!NOTE]
     > 
     > Sie können eine Ressource, eine Ressourcengruppe oder ein gesamtes Abonnement für das Aktivitätsprotokollsignal auswählen.

     **Beispielansicht für Warnungsziel**
     ![Ziel auswählen](media/alerts-activity-log/select-target.png)

   - Klicken Sie unter **Zielkriterien** auf **Kriterien hinzufügen**. Daraufhin werden alle verfügbaren Signale für das Ziel angezeigt, einschließlich der Signale aus verschiedenen Kategorien des **** Aktivitätsprotokolls, wobei der Kategoriename an den Namen für den **Monitordienst** angehängt wird.

   - Wählen Sie das Signal in der Liste mit den verschiedenen möglichen Vorgängen für den Typ **Aktivitätsprotokoll** aus.

     Sie können die Zeitachse für den Protokollverlauf und die entsprechende Warnungslogik für dieses Zielsignal auswählen:

     **Anzeige „Kriterien hinzufügen“**

     ![Kriterien hinzufügen](media/alerts-activity-log/add-criteria.png)

     **Verlaufszeit**: Die für den ausgewählten Vorgang verfügbaren Ereignisse können über die letzten 6/12/24 Stunden oder über die letzte Woche aufgezeichnet werden.

     **Warnungslogik**:

     - **Ereignisstufe**: Der Schweregrad des Ereignisses. _Ausführlich_, _Information_, _Warnung_, _Fehler_ oder _Kritisch_.
     - **Status:** Der Status des Ereignisses. _Gestartet_, _Fehlgeschlagen_ oder _Erfolgreich_.
     - **Ereignis initiiert von:** Auch als Aufrufer bekannt; die E-Mail-Adresse oder der Azure Active Directory-Bezeichner des Benutzers, der den Vorgang durchgeführt hat.

       Beispielsignaldiagramm mit angewandter Warnungslogik:

       ![ ausgewählten Kriterien](media/alerts-activity-log/criteria-selected.png)

4. Geben Sie unter **define alert rules details** (Informationen zu Warnungsregeln definieren) die folgenden Details an:

    - **Name der Warnungsregel**: Der Name für die neue Warnungsregel.
    - **Beschreibung**: Die Beschreibung für die neue Warnungsregel.
    - **Warnung in Ressourcengruppe speichern**: Wählen Sie die Ressourcengruppe aus, in der Sie die neue Regel speichern möchten.

5. Geben Sie unter **Aktionsgruppe** im Dropdownmenü die Aktionsgruppe an, der Sie die neue Warnungsregel zuweisen möchten. Alternativ hierzu können Sie auch [eine neue Aktionsgruppe erstellen](../../azure-monitor/platform/action-groups.md) und der neuen Regel zuweisen. Klicken Sie zum Erstellen einer neuen Gruppe auf **+ Neue Gruppe**.

6. Klicken Sie zum Aktivieren der Regeln nach der Erstellung für **Regel beim Erstellen aktivieren** auf **Ja**.
7. Klicken Sie auf **Warnungsregel erstellen**.

    Die neue Warnungsregel für das Aktivitätsprotokoll wird erstellt, und oben rechts im Fenster wird eine Bestätigungsmeldung angezeigt.

    Sie können eine Regel aktivieren, deaktivieren, bearbeiten oder löschen. Erfahren Sie mehr über die Verwaltung von Aktivitätsprotokollregeln.


Eine einfache Analogie zum Verständnis der Bedingungen, unter denen Warnungsregeln für das Aktivitätsprotokoll erstellt werden können, stellt alternativ die Untersuchung oder Filterung von Ereignissen über das [Aktivitätsprotokoll im Azure-Portal](activity-log-view.md#azure-portal) dar. In „Azure Monitor – Aktivitätsprotokoll“ können Sie nach erforderlichen Ereignissen filtern oder suchen und dann mithilfe der Schaltfläche **Aktivitätsprotokollwarnung hinzufügen** eine Warnung erstellen. Führen Sie anschließend die oben angegebenen Tutorialschritte ab Schritt 4 aus.
    
 ![ Hinzufügen einer Warnung aus dem Aktivitätsprotokoll](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Anzeigen und Verwalten im Azure-Portal

1. Klicken Sie im Azure-Portal auf **Überwachen** > **Warnungen** und dann oben links im Fenster auf **Regeln verwalten**.

    ![ Verwalten von Warnungsregeln](media/alerts-activity-log/manage-alert-rules.png)

    Die Liste mit den verfügbaren Regeln wird angezeigt.

2. Suchen Sie nach der zu ändernden Aktivitätsprotokollregel.

    ![ Suche nach Regel für Aktivitätsprotokollwarnungen](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Sie können die verfügbaren Filter _Abonnement_, _Ressourcengruppe_, _Ressource_, _Signaltyp_ oder _Status_ verwenden, um die Aktivitätsregel zu ermitteln, die Sie bearbeiten möchten.

   > [!NOTE]
   > 
   > Sie können nur **Beschreibung**, **Zielkriterien** und **Aktionsgruppen** bearbeiten.

3. Wählen Sie die Regel aus, und doppelklicken Sie darauf, um die Regeloptionen zu bearbeiten. Nehmen Sie die erforderlichen Änderungen vor, und klicken Sie dann auf **Speichern**.

   ![ Verwalten von Warnungsregeln](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Sie können eine Regel deaktivieren, aktivieren oder löschen. Wählen Sie oben im Fenster die entsprechende Option, nachdem Sie die Regel wie in Schritt 2 beschrieben ausgewählt haben.


## <a name="azure-resource-template"></a>Azure-Ressourcenvorlage
Um eine Aktivitätsprotokollwarnung mithilfe einer Resource Manager-Vorlage zu erstellen, müssen Sie eine Ressource des Typs `microsoft.insights/activityLogAlerts` erstellen. Anschließend tragen Sie alle zugehörigen Eigenschaften ein. Hier sehen Sie eine Vorlage, mit der eine Aktivitätsprotokollwarnung erstellt wird.

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
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
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
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
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
Die Json-Beispiel oben kann im Rahmen dieser exemplarischen Vorgehensweise z. B. als „sampleActivityLogAlert.json“ gespeichert und mithilfe von [Azure Resource Manager im Azure-Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md) bereitgestellt werden.

> [!NOTE]
> Es kann bis zu 5 Minuten dauern, bis eine neue Warnungsregel des Aktivitätsprotokolls aktiv wird.

## <a name="rest-api"></a>REST-API 
[Azure Monitor – API für Aktivitätsprotokollwarnungen](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) ist eine REST-API und vollständig kompatibel mit der Azure Resource Manager-REST-API. Daher kann es über Powershell mit dem Resource Manager-Cmdlet und der Azure CLI verwendet werden.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Bereitstellen einer Resource Manager-Vorlage mit PowerShell
Um PowerShell zum Bereitstellen der Beispielressourcenvorlage zu verwenden, die in einem früheren [Resourcenvorlagenabschnitt](#resource-manager-template, gezeigt wurde, verwenden Sie den folgenden Befehl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Dabei enthält die Datei „sampleActivityLogAlert.parameters.json“ die Werte für die Parameter, die für die Erstellung der Warnungsregeln erforderlich sind.

### <a name="use-activity-log-powershell-cmdlets"></a>Verwenden von PowerShell-Aktivitätsprotokoll-Cmdlets

In Aktivitätsprotokollwarnungen stehen folgende dedizierte PowerShell-Cmdlets zur Verfügung:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0): Erstellt eine neue oder aktualisiert eine vorhandene Aktivitätsprotokollwarnung.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0): Ruft mindestens eine Aktivitätsprotokoll-Warnungsressource ab.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0): Aktiviert eine vorhandene Aktivitätsprotokollwarnung und legt deren Tags fest.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0): Deaktiviert eine vorhandene Aktivitätsprotokollwarnung und legt deren Tags fest.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0): Entfernt eine Aktivitätsprotokollwarnung.

## <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Unter [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) stehen dedizierte Azure CLI-Befehle zum Verwalten von Aktivitätsprotokoll-Warnungsregeln zur Verfügung.

Verwenden Sie für die Erstellung einer neuen Aktivitätsprotokoll-Warnungsregel die Befehle in der folgenden Reihenfolge:

1. [az monitor activity-log alert create:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create) Erstellen einer neuen Ressource für Aktivitätsprotokoll-Warnungsregeln
1. [az monitor activity-log alert scope:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope) Hinzufügen des Bereichs für die erstellte Aktivitätsprotokoll-Warnungsregel
1. [az monitor activity-log alert action-group:](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group) Hinzufügen einer Aktionsgruppe zur Aktivitätsprotokoll-Warnungsregel

Zum Abrufen einer Ressource für Aktivitätsprotokoll-Warnungsregeln kann der Azure CLI-Befehl [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) verwendet werden. Zum Anzeigen aller Ressourcen für Aktivitätsprotokoll-Warnungsregeln in einer Ressourcengruppe verwenden Sie den Befehl [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Ressourcen für Aktivitätsprotokoll-Warnungsregeln können mit dem Azure CLI-Befehl [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

- [Webhookschema für Aktivitätsprotokolle](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Übersicht über Aktivitätsprotokolle](../../azure-monitor/platform/activity-log-alerts.md) 
- Weitere Informationen zu [Aktionsgruppen](../../azure-monitor/platform/action-groups.md).  
- Weitere Informationen zu [Dienstintegritätsbenachrichtigungen](../../azure-monitor/platform/service-notifications.md).
