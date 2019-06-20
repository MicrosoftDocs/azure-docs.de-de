---
title: Automatisieren von Azure Application Insights mit PowerShell | Microsoft-Dokumentation
description: Automatisieren Sie die Ressourcen-, Warnungs- und Verfügbarkeitstesterstellung in PowerShell mithilfe einer Azure Resource Manager-Vorlage.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mbullwin
ms.openlocfilehash: 07d52544b584adb02cc60790b7cb63c8aee1e366
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514475"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Erstellen von Application Insights-Ressourcen mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dieser Artikel beschreibt, wie Sie die Erstellung und Aktualisierung von [Application Insights](../../azure-monitor/app/app-insights-overview.md)-Ressourcen mit der Azure-Ressourcenverwaltung automatisieren können. Dies kann z. B. als Teil eines Buildvorgangs erfolgen. Zusammen mit der grundlegenden Application Insights-Ressource können Sie [Verfügbarkeitswebtests](../../azure-monitor/app/monitor-web-app-availability.md) erstellen, [Warnungen](../../azure-monitor/app/alerts.md) einrichten, das [Preisschema](pricing.md) festlegen und andere Azure-Ressourcen erstellen.

Im Wesentlichen werden diese Ressourcen mit JSON-Vorlagen für den [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md) erstellt. Die Vorgehensweise lässt sich wie folgt zusammenfassen: Sie laden die JSON-Definitionen vorhandener Ressourcen herunter, parametrisieren bestimmte Werte, z. B. Namen, und führen dann die Vorlage immer aus, wenn Sie eine neue Ressource erstellen möchten. Sie können mehrere Ressourcen zusammenfassen und in einem Durchgang erstellen, z. B. einen App-Monitor mit Verfügbarkeitstests, Warnungen und Speicher für fortlaufenden Export. Einige Parametrisierungen weisen Besonderheiten auf, die hier erläutert werden.

## <a name="one-time-setup"></a>Einmalige Konfiguration
Wenn Sie PowerShell noch nicht mit Ihrem Azure-Abonnement verwendet haben:

Installieren Sie das Azure PowerShell-Modul auf dem Computer, auf dem die Skripts ausgeführt werden sollen:

1. Installieren Sie [Microsoft-Webplattform-Installer (Version 5 oder höher)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Installieren Sie hiermit Microsoft Azure PowerShell.

## <a name="create-an-azure-resource-manager-template"></a>Erstellen einer Azure Resource Manager-Vorlage
Erstellen Sie eine neue JSON-Datei, in diesem Beispiel die Datei `template1.json` . Kopieren Sie den folgenden Inhalt in die Datei:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Per GB (Basic), 2 = Per Node (Enterprise)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Erstellen von Application Insights-Ressourcen
1. Melden Sie sich in PowerShell bei Azure an:
   
    `Connect-AzAccount`
2. Führen Sie beispielsweise den folgenden Befehl aus:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` ist die Gruppe, in der Sie die neuen Ressourcen erstellen möchten.
   * `-TemplateFile` muss vor den benutzerdefinierten Parametern angegeben werden.
   * `-appName` ist der Name der zu erstellenden Ressource.

Sie können noch weitere Parameter hinzufügen. Die entsprechenden Beschreibungen finden Sie im Abschnitt „Parameter“ der Vorlage.

## <a name="to-get-the-instrumentation-key"></a>So rufen Sie den Instrumentierungsschlüssel ab
Nach dem Erstellen einer Anwendungsressource benötigen Sie den Instrumentierungsschlüssel: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Festlegen des Tarifs

Sie können den [Tarif](pricing.md) festlegen.

Gehen Sie wie folgt vor, um mit der obigen Vorlage eine App-Ressource mit dem Enterprise-Preisplan zu erstellen:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|Tarif|
|---|---|
|1|Basic|
|2|Enterprise|

* Wenn Sie nur den Standardtarif „Basic“ verwenden möchten, können Sie die Ressource CurrentBillingFeatures aus der Vorlage löschen.
* Wenn Sie den Tarif nach dem Erstellen der Komponentenressource wechseln möchten, können Sie eine Vorlage verwenden, in der die Ressource „microsoft.insights/components“ ausgelassen wird. Lassen Sie zudem den Knoten `dependsOn` aus der Abrechnungsressource aus. 

Um den aktualisierten Tarif zu prüfen, betrachten Sie das Blatt **Nutzung und geschätzte Kosten** im Browser. **Aktualisieren Sie die Browseransicht**, um sicherzustellen, dass der aktuelle Status wiedergegeben wird.



## <a name="add-a-metric-alert"></a>Hinzufügen einer Metrikwarnung

Um eine Metrikwarnung gleichzeitig mit der App-Ressource zu testen, können Sie Code dieser Art in der Vorlagendatei zusammenführen:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Beim Aufrufen der Vorlage können Sie optional diesen Parameter hinzufügen:

    `-responseTime 2`

Natürlich können Sie auch andere Felder parametrisieren. 

Wenn Sie die Typnamen und Konfigurationsdetails von anderen Warnungsregeln ermitteln möchten, erstellen Sie manuell eine Regel und untersuchen diese dann im [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Hinzufügen eines Verfügbarkeitstests

In diesem Beispiel wird ein Pingtest verwendet (zum Testen einer einzelnen Seite).  

Ein Verfügbarkeitstest besteht aus **zwei Teilen**: Dem Test selbst und der Warnung, mit der Sie über Fehler informiert werden.

Fügen Sie den folgenden Code in die Vorlagendatei ein, mit der die App erstellt wird.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Um die Codeelemente für andere Testorte zu ermitteln oder die Erstellung von komplexeren Webtests zu automatisieren, erstellen Sie manuell ein Beispiel und parametrisieren den Code dann mit [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Hinzufügen weiterer Ressourcen

Um die Erstellung von beliebigen anderen Ressourcen zu automatisieren, erstellen Sie manuell ein Beispiel und kopieren und parametrisieren den Code dann über [Azure Resource Manager](https://resources.azure.com/). 

1. Öffnen Sie den [Azure-Ressourcen-Manager](https://resources.azure.com/). Navigieren Sie über `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` nach unten zu Ihrer Anwendungsressource. 
   
    ![Navigation im Azure-Ressourcen-Explorer](./media/powershell/01.png)
   
    *Komponenten* sind die grundlegenden Application Insights-Ressourcen zum Anzeigen von Anwendungen. Für die zugeordneten Warnungsregeln und Verfügbarkeitswebtests liegen separate Ressourcen vor.
2. Kopieren Sie die JSON-Definition der Komponente an die entsprechende Stelle in der Datei `template1.json`.
3. Löschen Sie folgende Eigenschaften:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Öffnen Sie die Abschnitte „webtests“ und „alertrules“, und kopieren Sie die JSON-Definition für einzelne Elemente in die Vorlage. (Kopieren Sie die Definition nicht aus den Knoten „webtests“ oder „alertrules“, sondern aus den Elementen unter diesen Knoten.)
   
    Jeder Webtest weist eine zugeordnete Warnungsregel auf, die Sie auch kopieren müssen.
   
    Sie können auch Warnungen für Metriken hinzufügen. [Metriknamen](powershell-alerts.md#metric-names).
5. Fügen Sie diese Zeile in jede Ressource ein:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrisieren der Vorlage
Nun müssen Sie die Namen durch Parameter ersetzen. Zum [Parametrisieren einer Vorlage](../../azure-resource-manager/resource-group-authoring-templates.md) schreiben Sie Ausdrücke mithilfe einer [Reihe von Hilfsfunktionen](../../azure-resource-manager/resource-group-template-functions.md). 

Sie können nicht einen Teil einer Zeichenfolge parametrisieren. Verwenden Sie daher `concat()` zum Erstellen von Zeichenfolgen.

Es folgen einige Beispiele der Ersetzungen, die Sie vornehmen können. Es gibt mehrere Vorkommen der einzelnen Ersetzungen. In Ihrer Vorlage müssen Sie unter Umständen andere Ersetzungen vornehmen. In diesen Beispielen werden die Parameter und Variablen verwendet, die oben in der Vorlage definiert wurden.

| Suchen | Ersetzen durch |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (Kleinbuchstaben) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Löschen Sie „Guid“ und „Id“. |

### <a name="set-dependencies-between-the-resources"></a>Festlegen von Abhängigkeiten zwischen den Ressourcen
Die Ressourcen sollten in Azure in strikter Reihenfolge eingerichtet werden. Um sicherzustellen, dass eine Einrichtung abgeschlossen ist, bevor die nächste beginnt, fügen Sie Abhängigkeitszeilen hinzu:

* In der Ressource für Verfügbarkeitstests:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* In der Warnungsressource für einen Verfügbarkeitstest:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Nächste Schritte
Andere Artikel zu Automation:

* [Erstellen einer Application Insights-Ressource](powershell-script-create-resource.md) : Schnellverfahren ohne Verwendung einer Vorlage.
* [Einrichten von Warnungen](powershell-alerts.md)
* [Erstellen von Webtests](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Senden von Azure-Diagnosedaten an Application Insights](powershell-azure-diagnostics.md)
* [Bereitstellen in Azure aus GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Erstellen von Versionsanmerkungen](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
