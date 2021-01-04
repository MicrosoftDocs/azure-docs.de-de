---
title: Einstellungen für intelligente Erkennungsregeln – Azure Application Insights
description: Automatisieren der Verwaltung und Konfiguration der intelligenten Erkennungsregeln von Azure Application Insights mit Azure Resource Manager-Vorlagen
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 169ad40e32f688ae20a9d02f61db161844b1254a
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890512"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Verwalten von intelligenten Erkennungsregeln von Azure Application Insights mit Azure Resource Manager-Vorlagen

Intelligente Erkennungsregeln in Application Insights können mit [Azure Resource Manager-Vorlagen](../../azure-resource-manager/templates/template-syntax.md) verwaltet und konfiguriert werden.
Diese Methode kann bei der Bereitstellung neuer Application Insights-Ressourcen mit Azure Resource Manager-Automatisierung oder zur Änderung der Einstellungen vorhandener Ressourcen verwendet werden.

## <a name="smart-detection-rule-configuration"></a>Konfiguration der intelligenten Erkennungsregel

Sie können die folgenden Einstellungen für intelligente Erkennungsregel konfigurieren:
- Wenn die Regel aktiviert ist (der Standardwert ist **true** .)
- Ob bei einer Erkennung E-Mails an Benutzer gesendet werden sollen, denen die Rollen [Überwachungsleser](../../role-based-access-control/built-in-roles.md#monitoring-reader) und [Überwachungsmitwirkender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) zugewiesen sind (Standardwert ist **true** ).
- Alle weiteren E-Mail-Empfänger, die eine Benachrichtigung erhalten sollen, wenn eine Erkennung gefunden wird.
    -  Die E-Mail-Konfiguration ist für Regeln für die intelligente Erkennung, die als _Vorschauversion_ markiert sind, nicht verfügbar.

Um die Konfiguration der Regeleinstellungen über den Azure Resource Manager zu ermöglichen, ist die Konfiguration der intelligenten Erkennungsregeln nun als interne Ressource innerhalb der Application Insights-Ressource mit dem Namen **ProactiveDetectionConfigs** verfügbar.
Für maximale Flexibilität kann jede intelligente Erkennungsregel mit individuellen Benachrichtigungseinstellungen konfiguriert werden.

## <a name="examples"></a>Beispiele

Im Folgenden finden Sie einige Beispiele dafür, wie Sie die Einstellungen der intelligenten Erkennungsregeln mithilfe von Azure Resource Manager-Vorlagen konfigurieren können.
Alle Beispiele beziehen sich auf eine Application Insights-Ressource namens _„myApplication“_ und auf die „long dependency duration smart detection rule“, die intern als _„longdependencyduration“_ bezeichnet ist.
Stellen Sie sicher, dass Sie den Application Insights-Ressourcennamen ersetzen und den entsprechenden internen Namen der intelligenten Erkennungsregel angeben. In der folgenden Tabelle finden Sie eine Liste der entsprechenden internen Azure Resource Manager-Namen für jede intelligente Erkennungsregel.

### <a name="disable-a-smart-detection-rule"></a>Deaktivieren einer intelligenten Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Deaktivieren der Sendung von E-Mail-Benachrichtigungen für eine intelligente Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Hinzufügen zusätzlicher E-Mail-Empfänger für eine intelligente Erkennungsregel

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "Application_Type": "web"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ["alice@contoso.com", "bob@contoso.com"],
            "enabled": true
          }
        }
      ]
    }

```


## <a name="smart-detection-rule-names"></a>Name der intelligente Erkennungsregel

Im Folgenden finden Sie eine Tabelle mit den Namen der intelligenten Erkennungsregeln, wie sie im Portal angezeigt werden, zusammen mit ihren internen Namen, die in der Azure Resource Manager-Vorlage verwendet werden sollten.

> [!NOTE]
> Intelligente Erkennungsregeln, die als _Vorschauversion_ markiert sind, unterstützen keine E-Mail-Benachrichtigungen. Aus diesem Grund können Sie nur die _enabled_ -Eigenschaft für diese Regeln festlegen. 

| Name der Regel im Azure-Portal | Interner Name
|:---|:---|
| Langsame Seitenladezeit | slowpageloadtime |
| Langsame Serverantwortzeit | slowserverresponsetime |
| Lange Abhängigkeitsdauer | longdependencyduration |
| Beeinträchtigung der Serverantwortzeit | degradationinserverresponsetime |
| Leistungsminderung der Abhängigkeitsdauer | degradationindependencyduration |
| Verschlechterung des Schweregrads der Ablaufverfolgung (Vorschau) | extension_traceseveritydetector |
| Anormaler Anstieg in Ausnahmevolume (Vorschau) | extension_exceptionchangeextension |
| Möglicher Speicherverluste erkannt (Vorschau) | extension_memoryleakextension |
| Mögliches Sicherheitsproblem erkannt (Vorschau) | extension_securityextensionspackage |
| Anormaler Anstieg des täglichen Datenvolumens (Vorschauversion) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Warnungsregel für Fehleranomalien

Diese Azure Resource Manager-Vorlage zeigt, wie eine Warnungsregel für Fehleranomalien mit dem Schweregrad 2 konfiguriert wird. Diese neue Version der Warnungsregel für Fehleranomalien ist Teil der neuen Azure-Warnungsplattform und ersetzt die klassische Version, die im Rahmen der [Einstellung klassischer Warnungen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/) eingestellt wird.

> [!NOTE]
> Fehleranomalien werden in einem globalen Dienst verarbeitet, daher wird die Regel für den globalen Speicherort erstellt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Diese Azure Resource Manager-Vorlage gilt nur für die Warnungsregel für Fehleranomalien und unterscheidet sich von den anderen klassischen Regeln für die intelligente Erkennung, die in diesem Artikel beschrieben werden. Wenn Sie Fehleranomalien manuell verwalten möchten, erfolgt dies in Azure Monitor-Warnungen. Alle anderen Regeln für die intelligente Erkennung werden im Bereich „Intelligente Erkennung“ über die Benutzeroberfläche verwaltet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur automatischen Erkennung finden Sie hier:

- [Anomalien bei Fehlern](./proactive-failure-diagnostics.md)
- [Arbeitsspeicherverluste](./proactive-potential-memory-leak.md)
- [Leistungsanomalien](./proactive-performance-diagnostics.md)

