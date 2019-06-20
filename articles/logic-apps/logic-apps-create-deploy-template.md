---
title: Erstellen von Bereitstellungsvorlagen für Azure Logic Apps | Microsoft-Dokumentation
description: Erstellen von Azure Resource Manager-Vorlagen für die Bereitstellung von Logik-Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128599"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Erstellen von Azure Resource Manager-Vorlagen für die Bereitstellung von Logik-Apps

Wenn Sie eine Logik-App erstellen, können Sie die Definition Ihrer Logik-App in eine [Azure Resource Manager-Vorlage](../azure-resource-manager/resource-group-overview.md) erweitern. Sie können dann mit dieser Vorlage Bereitstellungen automatisieren, indem Sie die Ressourcen und Parameter definieren, die Sie für die Bereitstellung verwenden möchten, und die Parameterwerte über eine [Parameterdatei](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) bereitstellen.
Auf diese Weise können Sie Logik-Apps einfacher und in jeder beliebigen Umgebung oder Azure-Ressourcengruppe bereitstellen. 

Azure Logic Apps bietet eine [vordefinierte Azure Resource Manager-Vorlage für Logik-Apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json), die Sie nicht nur zum Erstellen von Logik-Apps, sondern auch zum Definieren der Ressourcen und Parameter für die Bereitstellung wiederverwenden können. Die Vorlage kann für eigene Unternehmensszenarien verwendet oder an Ihre individuellen Anforderungen angepasst werden. Weitere Informationen finden Sie unter [Resource Manager-Vorlagen: Struktur und Syntax](../azure-resource-manager/resource-group-authoring-templates.md). Informationen zu JSON-Syntax und Eigenschaften finden Sie unter [Microsoft.Logic resource types](/azure/templates/microsoft.logic/allversions) (Microsoft.Logic-Ressourcentypen).

Weitere Informationen zu Azure Resource Manager-Vorlagen finden Sie in den folgenden Artikeln:

* [Erstellen von Azure Resource-Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md)
* [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Struktur der Logik-App

Die Definition Ihrer Logik-App verfügt über diese grundlegenden Abschnitte, die Sie anzeigen können, indem Sie von der „Designeransicht“ zur „Codeansicht“ wechseln oder ein Tool wie [Azure-Ressourcen-Explorer](http://resources.azure.com) verwenden. Definitionen von Logik-Apps verwenden JSON (Javascript Object Notation), daher finden Sie weitere Informationen zu Syntax und Eigenschaften von JSON unter [Microsoft.Logic-Ressourcentypen](/azure/templates/microsoft.logic/allversions).

* **Logik-App-Ressource**: Beschreibt Informationen wie den Speicherort oder die Region Ihrer Logik-App, die Preisübersicht und die Workflowdefinition.

* **Workflowdefinition**: Beschreibt die Auslöser und Aktionen Ihrer Logik-App und wie der Azure Logic Apps-Dienst den Workflow ausführt. In der Codeansicht finden Sie die Workflowdefinition im Abschnitt `definition`.

* **Verbindungen**: Wenn Sie in Ihrer Logik-App verwaltete Connectors verwenden, verweist der Abschnitt `$connections` auf andere Ressourcen, die Metadaten über diese Verbindungen zwischen Ihrer Logik-App und anderen Systemen oder Diensten sicher speichern, z. B. Verbindungszeichenfolgen und Zugriffstoken. Innerhalb der Definition Ihrer Logik-App werden Verweise auf diese Verbindungen im Abschnitt `parameters` der Definition Ihrer Logik-App angezeigt.

Um eine Logik-App-Vorlage zu erstellen, die Sie mit Bereitstellungen für Azure-Ressourcengruppen verwenden können, müssen Sie die Ressourcen definieren und bei Bedarf parametrieren. Beispiel: Wenn Sie eine App in einer Entwicklungs-, einer Test- und einer Produktionsumgebung bereitstellen möchten, sollten Sie unterschiedliche Verbindungszeichenfolgen zur SQL-Datenbank in jeder Umgebung verwenden.
Alternativ dazu können Sie die App auch in verschiedenen Abonnements oder Ressourcengruppen bereitstellen.

## <a name="create-logic-app-deployment-templates"></a>Erstellen von Bereitstellungsvorlagen für Logik-Apps

Verwenden Sie Visual Studio und die Azure Logic Apps-Tools für die Visual Studio-Erweiterung, um auf einfachste Weise eine gültige Vorlage für die Bereitstellung von Logik-Apps zu erstellen. Wenn Sie Ihre Logik-App aus dem Azure-Portal in Visual Studio herunterladen, erhalten Sie eine gültige Bereitstellungsvorlage, die Sie mit jedem Azure-Abonnement und jedem Standort verwenden können. Außerdem wird beim Herunterladen Ihrer Logik-App automatisch die Definition der Logik-App parametrisiert, die in der Vorlage eingebettet ist.
Weitere Informationen zum Erstellen und Verwalten von Logik-Apps in Visual Studio finden Sie unter [Erstellen von Logik-Apps mit Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) und [Verwalten von Logik-Apps mit Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Zusätzlich zu Visual Studio oder der manuellen Erstellung Ihrer Vorlage und der erforderlichen Parameter, indem Sie der Anleitung in diesem Thema folgen, können Sie auch das [PowerShell-Modul zum Erstellen von Logik-App-Vorlagen](https://github.com/jeffhollan/LogicAppTemplateCreator) verwenden. Dieses Open Source-Modul bewertet zunächst Ihre Logik-App und alle Verbindungen, die die Logik-App verwendet. Das Modul generiert dann Vorlagenressourcen mit den erforderlichen Parametern für die Bereitstellung. Angenommen, Sie haben eine Logik-App, die eine Nachricht von einer Azure Service Bus-Warteschlange empfängt und Daten zu einer Azure SQL-Datenbank hinzufügt. Das Modultool bewahrt die gesamte Orchestrierungslogik und parametrisiert die SQL- und Service Bus-Verbindungszeichenfolgen, sodass Sie diese Werte bei der Bereitstellung festlegen können.

> [!IMPORTANT]
> Verbindungen müssen in derselben Azure-Ressourcengruppe wie die Logik-App vorhanden sein.
> Damit das PowerShell-Modul mit jedem Azure-Mandanten und Abonnementzugriffstoken funktioniert, verwenden Sie das Modul mit dem [Azure Resource Manager-Clienttool](https://github.com/projectkudu/ARMClient). Weitere Informationen finden Sie in diesem [Artikel zum Azure Resource Manager Clienttool](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html), in dem der ARMClient näher erläutert wird.

### <a name="install-powershell-module-for-logic-app-templates"></a>Installieren des PowerShell-Moduls für Logik-App-Vorlagen

Am einfachsten lässt sich das Modul über den [PowerShell-Katalog](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1) mit dem folgenden Befehl installieren:

`Install-Module -Name LogicAppTemplate`

Sie können das PowerShell-Modul auch manuell installieren:

1. Laden Sie den neuesten [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator/releases) herunter.

1. Extrahieren Sie den Ordner in Ihren PowerShell-Modulordner (in der Regel `%UserProfile%\Documents\WindowsPowerShell\Modules`).

### <a name="generate-logic-app-template---powershell"></a>Generieren von Logik-App-Vorlagen – PowerShell

Nach der Installation von PowerShell können Sie mithilfe des folgenden Befehls eine Vorlage generieren:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` ist die Azure-Abonnement-ID. Diese erste Zeile ruft über ARMClient ein Zugriffstoken ab, übergibt es an das PowerShell-Skript und erstellt dann die Vorlage in einer JSON-Datei.

## <a name="parameters-in-logic-app-templates"></a>Parameter in Logik-App-Vorlagen

Nachdem Sie Ihre Logik-App-Vorlage erstellt haben, können Sie alle notwendigen Parameter hinzufügen und bearbeiten. Ihre Vorlage verfügt z. B. über mehr als einen `parameters`-Abschnitt: 

* Die Workflowdefinition Ihrer Logik-App hat ihren eigenen [`parameters`-Abschnitt](../logic-apps/logic-apps-workflow-definition-language.md#parameters), in dem Sie alle Parameter definieren können, die Ihre Logik-App für die Annahme von Eingaben bei der Bereitstellung verwendet.

* Ihre Resource Manager-Vorlage verfügt über einen eigenen [`parameters`-Abschnitt](../azure-resource-manager/resource-group-authoring-templates.md#parameters), der vom `parameters`-Abschnitt Ihrer Logik-App getrennt ist. Beispiel:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Nehmen Sie z. B. an, dass die Definition Ihrer Logik-App auf eine Ressourcen-ID verweist, die eine Azure-Funktion oder einen geschachtelten Logik-App-Workflow darstellt, und Sie möchten diese Ressourcen-ID zusammen mit Ihrer Logik-App als einzelne Bereitstellung bereitstellen. Sie können diese ID als Ressource in Ihrer Vorlage hinzufügen und diese ID parametrisieren. Sie können denselben Ansatz für Verweise auf benutzerdefinierte APIs oder OpenAPI-Endpunkte (ehemals „Swagger“) verwenden, die Sie mit den einzelnen Azure-Ressourcengruppen bereitstellen möchten.

Wenn Sie Parameter in Ihrer Bereitstellungsvorlage verwenden, befolgen Sie diese Anleitung, damit der Designer für Logik-Apps diese Parameter ordnungsgemäß anzeigen kann:

* Verwenden Sie Parameter nur in diesen Auslösern und Aktionen:

  * Azure Functions-App
  * Workflow für geschachtelte oder untergeordnete Logik-Apps
  * API Management-Aufruf
  * Laufzeit-URL für API-Verbindung
  * API-Verbindungspfad

* Achten Sie bei der Definition von Parametern darauf, dass Sie Standardwerte mit dem Eigenschaftswert `defaultValue` angeben. Beispiel:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Um vertrauliche Informationen in Vorlagen zu schützen oder zu verbergen, sichern Sie Ihre Parameter. Weitere Informationen zum [Verwenden von geschützten Parametern](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Hier folgt ein Beispiel, das zeigt, wie Sie die Aktion „Nachricht senden“ von Azure Service Bus parametrieren können:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Verweisen auf abhängige Ressourcen

Wenn Ihre Logik-App Verweise auf abhängige Ressourcen erfordert, können Sie in Ihrer Logik-App-Bereitstellungsvorlage [Azure Resource Manager-Vorlagenfunktionen](../azure-resource-manager/resource-group-template-functions.md) verwenden. Nehmen Sie z. B. an, dass Ihre Logik-App auf eine Azure-Funktion oder ein Integrationskonto mit Definitionen für Partner, Vereinbarungen und andere Artefakte verweisen soll, die Sie zusammen mit Ihrer Logik-App bereitstellen möchten.
Sie können Resource Manager-Vorlagenfunktionen wie `parameters`, `variables`, `resourceId`, `concat` usw. verwenden.

Hier folgt ein Beispiel, das zeigt, wie Sie die Ressourcen-ID für eine Azure-Funktion durch die Definition dieser Parameter ersetzen können:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Im Folgenden wird beschrieben, wie Sie diese Parameter verwenden, wenn Sie auf die Azure-Funktion verweisen:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Hinzufügen einer Logik-App zum Ressourcengruppenprojekt

Wenn Sie über ein vorhandenes Azure-Ressourcengruppenprojekt verfügen, können Sie Ihre Logik-App im Fenster mit der JSON-Gliederung dem Projekt hinzufügen. Außerdem können Sie eine weitere Logik-App zusammen mit der zuvor erstellten App hinzufügen.

1. Öffnen Sie im Projektmappen-Explorer die Datei `<template>.json`.

2. Wählen Sie im Menü **Ansicht** die Option **Weitere Fenster** > **JSON-Gliederung** aus.

3. Wählen Sie am oberen Rand des Fensters mit der JSON-Gliederung die Option **Ressource hinzufügen** aus, um der Vorlagendatei eine Ressource hinzuzufügen. Klicken Sie im Fenster mit der JSON-Gliederung mit der rechten Maustaste auf **Ressourcen**, und wählen Sie **Neue Ressource hinzufügen** aus.

   ![Fenster mit der JSON-Gliederung](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Wählen Sie im Dialogfeld **Ressource hinzufügen** die Option **Logik-App** aus. Benennen Sie Ihre Logik-App, und wählen Sie **Hinzufügen** aus.

   ![Ressource hinzufügen](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Support

Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen von Logik-App-Vorlagen](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
