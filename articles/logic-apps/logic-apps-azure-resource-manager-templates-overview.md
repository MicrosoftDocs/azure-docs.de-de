---
title: 'Übersicht: Automatisieren der Bereitstellung für Azure Logic Apps'
description: Erfahren Sie mehr über Azure Resource Manager-Vorlagen, um die Bereitstellung für Azure Logic Apps zu automatisieren.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/06/2020
ms.openlocfilehash: 4070f373175f3497156ced011a57e2ed7bd6e770
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009771"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Übersicht: Automatisieren der Bereitstellung für Azure Logic Apps durch Verwenden von Azure Resource Manager-Vorlagen

Wenn Sie soweit sind, dass Sie das Erstellen und Bereitstellen Ihrer Logik-App automatisieren möchten, können Sie die Workflowdefinition, die Ihrer Logik-App zugrunde liegt, zu einer [Azure Resource Manager-Vorlage](../azure-resource-manager/management/overview.md) erweitern. In dieser Vorlage sind die Infrastruktur, die Ressourcen, die Parameter und weitere Informationen für die Bereitstellung Ihrer Logik-App definiert. Durch das Definieren von Parametern für Werte, die bei der Bereitstellung variieren, auch als *Parametrisierung* bezeichnet, können Sie Logik-Apps wiederholt und konsistent entsprechend unterschiedlicher Bereitstellungsanforderungen bereitstellen.

Wenn Sie beispielsweise in Umgebungen für Entwicklung, Testen und Produktion bereitstellen, verwenden Sie wahrscheinlich unterschiedliche Verbindungszeichenfolgen für jede Umgebung. Sie können Vorlagenparameter deklarieren, die unterschiedliche Verbindungszeichenfolgen akzeptieren, und diese Zeichenfolgen dann in einer separaten [Parameterdatei](../azure-resource-manager/templates/parameter-files.md) speichern. So können Sie diese Werte ändern, ohne die Vorlage aktualisieren und erneut bereitstellen zu müssen. Für Fälle, in denen Sie Parameterwerte haben, die vertraulich sind oder geschützt werden müssen, etwa Kennwörter und Geheimnisse, können Sie diese Werte in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) speichern und diese Werte in die Parameterdatei abrufen. In diesen Fällen würden Sie jedoch ein erneutes Bereitstellen vornehmen, um die aktuellen Werte abzurufen.

In dieser Übersicht sind die Attribute in einer Resource Manager-Vorlage beschrieben, die die Workflowdefinition einer Logik-App enthält. Sowohl für die Vorlage als auch für die Workflowdefinition wird JSON-Syntax verwendet. Es gibt jedoch einige Unterschiede, weil für die Workflowdefinition auch das [Schema der Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md) beachtet wird. Beispielsweise unterscheiden sich Vorlagenausdrücke und Workflowdefinitionsausdrücke sowohl darin, wie in ihnen auf [Parameter verwiesen](#parameter-references) wird, als auch in den Werten, die sie akzeptieren können.

> [!TIP]
> Als einfachste Möglichkeit, eine gültige parametrisierte Logik-App-Vorlage zu erhalten, die weitestgehend zur Bereitstellung bereit ist, verwenden Sie Visual Studio (kostenlose Community Edition oder höher) und die Azure Logic Apps-Tools für Visual Studio. Sie können dann entweder [Ihre Logik-App in Visual Studio erstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) oder [in Azure nach einer vorhandenen Logik-App suchen und diese nach Visual Studio herunterladen](../logic-apps/manage-logic-apps-with-visual-studio.md).
>
> Alternativ können Sie Logik-App-Vorlagen erstellen, indem Sie [Azure PowerShell mit dem LogicAppTemplate-Modul](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell) verwenden.

In der Beispiel-Logik-App in diesem Thema wird ein [Office 365 Outlook-Trigger](/connectors/office365/) verwendet, der ausgelöst wird, wenn eine neue E-Mail eingetroffen ist, und wird eine [Azure Blob Storage-Aktion](/connectors/azureblob/) verwendet, in der ein Blob für den E-Mail-Text erstellt und dieses Blob in einen Azure-Speichercontainer hochgeladen wird. In den Beispielen wird auch veranschaulicht, wie Werte parametrisiert werden, die bei der Bereitstellung variieren.

Weitere Informationen zu Resource Manager-Vorlagen finden Sie in den folgenden Themen:

* [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)
* [Bewährte Methoden für Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-best-practices.md)
* [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](../azure-resource-manager/templates/templates-cloud-consistency.md)

Vorlagenressourceninformationen, die speziell für Logik-Apps, Integrationskonten, Integrationskontoartefakte und Integrationsdienstumgebungen gelten, finden Sie unter [Microsoft.Logic-Ressourcentypen](/azure/templates/microsoft.logic/allversions).

Logik-App-Beispielvorlagen finden Sie in den folgenden Beispielen:

* [Vollständige Beispielvorlage](#full-example-template), die für die Beispiele in diesem Thema verwendet wird
* [Logik-App-Beispielvorlage für Schnellstart](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) in GitHub

Beginnen Sie für die Logic Apps-REST-API mit der [Übersicht über die Azure Logic Apps-REST-API](/rest/api/logic).

<a name="template-structure"></a>

## <a name="template-structure"></a>Vorlagenstruktur

Auf der obersten Ebene hat eine Resource Manager-Vorlage diese Struktur, die vollständig im Thema [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md) beschrieben ist:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

In einer Logik-App-Vorlage arbeiten Sie hauptsächlich mit diesen Vorlagenobjekten:

| attribute | BESCHREIBUNG |
|-----------|-------------|
| `parameters` | Deklariert die [Vorlagenparameter](../azure-resource-manager/templates/template-syntax.md#parameters) zum Akzeptieren der Werte, die verwendet werden sollen, wenn Ressourcen zur Bereitstellung in Azure erstellt und angepasst werden. Diese Parameter akzeptieren z. B. die Werte für den Namen und den Speicherort Ihrer Logik-App, Verbindungen und weitere Ressourcen, die für die Bereitstellung erforderlich sind. Sie können diese Parameterwerte in einer [Parameterdatei](#template-parameter-files) speichern, die weiter unten in diesem Thema beschrieben ist. Allgemeine Informationen finden Sie unter [Parameter – Struktur und Syntax einer Resource Manager-Vorlage](../azure-resource-manager/templates/template-syntax.md#parameters). |
| `resources` | Definiert die [Ressourcen](../azure-resource-manager/templates/template-syntax.md#resources), die erstellt oder aktualisiert und in einer Azure-Ressourcengruppe bereitgestellt werden sollen, wozu Ihre Logik-App, Verbindungen, Azure-Speicherkonten usw. gehören. Allgemeine Informationen finden Sie unter [Ressourcen – Struktur und Syntax einer Resource Manager-Vorlage](../azure-resource-manager/templates/template-syntax.md#resources). |
||||

Für eine Logik-App-Vorlage wird das folgende Dateinamenformat verwendet:

**<*logik-app-name*>.json**

> [!IMPORTANT]
> Für Vorlagensyntax wird die Groß-/Kleinschreibung beachtet. Daher müssen darauf achten, eine konsistente Schreibung zu verwenden. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Vorlagenparameter

Eine Logik-App-Vorlage hat mehrere `parameters`-Objekte, die auf unterschiedlichen Ebenen vorhanden sind und unterschiedliche Funktionen erfüllen. Beispielsweise können Sie auf der obersten Ebene [Vorlagenparameter](../azure-resource-manager/templates/template-syntax.md#parameters) für die Werte deklarieren, die zur Bereitstellung akzeptiert und verwendet werden sollen, wenn Sie Ressourcen in Azure erstellen und bereitstellen. Dazu gehören zum Beispiel:

* Ihre Logik-App
* Verbindungen, die in ihrer Logik verwendet werden, um über [verwaltete Connectors](../connectors/apis-list.md) auf andere Dienste und Systeme zuzugreifen
* Weitere Ressourcen, die zur Bereitstellung Ihrer Logik-App erforderlich sind

  Wird in Ihrer Logik-App beispielsweise ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) für B2B-Szenarien (Business-to-Business) verwendet, wird im `parameters`-Objekt auf oberster Ebene der Vorlage der Parameter deklariert, der die Ressourcen-ID für dieses Integrationskonto akzeptiert.

Nachstehend sind die allgemeine Struktur und Syntax für eine Parameterdefinition angegeben, die vollständig unter [Parameter – Struktur und Syntax einer Resource Manager-Vorlage](../azure-resource-manager/templates/template-syntax.md#parameters) beschrieben ist:

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

In diesem Beispiel sind lediglich die Vorlagenparameter für die Werte aufgeführt, die zum Erstellen und Bereitstellen dieser Ressourcen in Azure verwendet werden:

* Name und Speicherort für Ihre Logik-App
* ID, die für ein Integrationskonto verwendet werden soll, das mit der Logik-App verknüpft ist

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Jeder dieser Parameter hat, sofern er kein Parameter ist, in dem ein sensibler oder zu schützender Wert (etwa Benutzername, Kennwort und Geheimnis) verwaltet wird, ein `defaultValue`-Attribut, wobei es Fälle gibt, in denen der Standardwert ein leerer Wert ist. Die Bereitstellungswerte, die für diese Vorlagenparameter verwendet werden sollen, sind in der Beispiel-[Parameterdatei](#template-parameter-files) enthalten, die weiter unten in diesem Thema beschrieben ist.

Weitere Informationen zum Sichern von Vorlagenparametern finden Sie in diesen Themen:

* [Sicherheitsempfehlungen für Vorlagenparameter](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Verbessern der Sicherheit für Vorlagenparameter](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Übergeben von geschützten Parameterwerten mit Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

In anderen Vorlagenobjekten wird häufig auf Vorlagenparameter verwiesen, sodass für sie die Werte verwendet werden können, die über Vorlagenparameter übergeben werden, beispielsweise:

* Im [„resources“-Objekt Ihrer Vorlage](#template-resources), das weiter unten in diesem Thema beschrieben ist, ist jede Ressource in Azure definiert, die Sie erstellen und bereitstellen möchten, etwa die [Ressourcendefinition Ihrer Logik-App](#logic-app-resource-definition). Für diese Ressourcen werden häufig Vorlagenparameterwerte verwendet, z. B. der Name und Speicherort Ihrer Logik-App sowie Verbindungsinformationen.

* Auf einer tieferen Ebene in der Ressourcendefinition ihrer Logik-App sind im [„parameters“-Objekt Ihrer Workflowdefinition](#workflow-definition-parameters) Parameter für die Werte deklariert, die in der Runtime Ihrer Logik-App zu verwenden sind. Beispielsweise können Sie Workflowdefinitionsparameter für den Benutzernamen und das Kennwort deklarieren, die in einem HTTP-Trigger zur Authentifizierung verwendet werden. Um die Werte für Workflowdefinitionsparameter anzugeben, verwenden Sie das `parameters`-Objekt, das sich *außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition Ihrer Logik-App befindet. In diesem äußeren `parameters`-Objekt können Sie auf zuvor deklarierte Vorlagenparameter verweisen, die bei einer Bereitstellung Werte aus einer Parameterdatei übernehmen können.

Beim Verweisen auf Parameter wird für Vorlagenausdrücke und-Funktionen eine unterschiedliche Syntax verwendet und verhalten sich diese anders als Workflowdefinitionsausdrücke und -funktionen. Weitere Informationen zu diesen Unterschieden finden Sie weiter unten in diesem Thema unter [Verweise auf Parameter](#parameter-references).

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Bewährte Methoden: Vorlagenparameter

Nachstehend sind einige bewährte Methoden zum Definieren von Parametern beschrieben:

* Deklarieren Sie Parameter nur für Werte, die je nach Ihren Bereitstellungsanforderungen variieren. Deklarieren Sie keine Parameter für Werte, die über verschiedene Bereitstellungsanforderungen hinweg gleich bleiben.

* Fügen Sie das `defaultValue`-Attribut, das leere Werte angeben kann, für alle Parameter ein, die keine sensiblen oder zu schützenden Daten enthalten. Verwenden Sie für Benutzernamen, Kennwörter und Geheimnisse immer geschützte Parameter. Um sensible Parameterwerte auszublenden oder zu schützen, befolgen Sie die Anweisungen in den folgenden Themen:

  * [Sicherheitsempfehlungen für Vorlagenparameter](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Verbessern der Sicherheit für Vorlagenparameter](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Übergeben von geschützten Parameterwerten mit Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Um Vorlagenparameternamen von Workflowdefinitionsparameternamen zu unterscheiden, können Sie beschreibende Vorlagenparameternamen verwenden, etwa: `TemplateFabrikamPassword`

Weitere bewährte Methoden für Vorlagen finden Sie unter [Bewährte Methoden für Vorlagenparameter](../azure-resource-manager/templates/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Vorlagenparameterdatei

Wenn Sie die Werte für Vorlagenparameter bereitstellen möchten, speichern Sie diese Werte in einer [Parameterdatei](../azure-resource-manager/templates/parameter-files.md). Auf diese Weise können Sie entsprechend Ihren Bereitstellungsanforderungen unterschiedliche Parameterdateien verwenden. Das für einen Dateinamen zu verwendende Format sieht wie folgt aus:

* Name der Vorlagendatei einer Logik-App: **<*logik-app-name*>.json**
* Name einer Parameterdatei: **<*logik-app-name*>.parameters.json**

Die Parameterdatei hat die folgende Struktur, wobei die Datei einen Schlüsseltresorverweis (Key Vault-Verweis) [zum Übergeben eines geschützten Parameterwerts mit Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) enthält:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

In dieser Beispielparameterdatei sind die Werte für die Vorlagenparameter angegeben, die weiter oben in diesem Thema deklariert sind:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Vorlagenressourcen

Die Vorlage hat ein `resources`-Objekt. Dieses Objekt ist ein Array, das Definitionen für jede Ressource enthält, die in Azure erstellt und bereitgestellt werden soll. Dazu gehören die [Ressourcendefinition Ihrer Logik-App](#logic-app-resource-definition), [Verbindungsressourcendefinitionen](#connection-resource-definitions) und alle weiteren Ressourcen, die Ihre Logik-App zur Bereitstellung benötigt.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Vorlagen können Ressourcendefinitionen für mehrere Logik-Apps enthalten. Stellen Sie daher sicher, dass in allen Ihrer Logik-App-Ressourcen dieselbe Azure-Ressourcengruppe angegeben ist. Wenn Sie die Vorlage über Visual Studio in einer Azure-Ressourcengruppe bereitstellen, werden Sie zur Angabe der Logik-App aufgefordert, die Sie öffnen möchten. Außerdem kann Ihr Azure-Ressourcengruppenprojekt mehrere Vorlagen enthalten. Achten Sie daher darauf, dass Sie die richtige Parameterdatei auswählen, wenn Sie dazu aufgefordert werden.

<a name="view-resource-definitions"></a>

### <a name="view-resource-definitions"></a>Anzeigen von Ressourcendefinitionen

Zum Überprüfen der Ressourcendefinitionen für alle Ressourcen [laden Sie die Logik-App von Azure in Visual Studio herunter](../logic-apps/manage-logic-apps-with-visual-studio.md). Dies ist die einfachste Möglichkeit, eine gültige parametrisierte Logik-App-Vorlage zu erstellen, die größtenteils für die Bereitstellung bereit ist.

Allgemeine Informationen zu Vorlagenressourcen und deren Attributen finden Sie in den folgenden Themen:

* [Ressourcen – Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md#resources)
* [Bewährte Methoden für Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Ressourcendefinition einer Logik-App

Die [Workflowressourcendefinition Ihrer Logik-App in einer Vorlage](/azure/templates/microsoft.logic/workflows) beginnt mit dem `properties`-Objekt, das folgende Informationen enthält:

* Der Zustand Ihrer Logik-App bei der Bereitstellung
* Die ID für jedes Integrationskonto, das von ihrer Logik-App verwendet wird
* Die Workflowdefinition ihrer Logik-App
* Ein `parameters`-Objekt, das die zur Laufzeit zu verwendenden Werte festlegt
* Weitere Ressourceninformationen zu Ihrer Logik-App, z. B. Name, Typ, Speicherort, eventuelle Laufzeitkonfigurationseinstellungen usw.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {},
            "runtimeConfiguration": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Die folgenden Attribute gehören speziell zur Ressourcendefinition Ihrer Logik-App:

| attribute | Erforderlich | type | Beschreibung |
|-----------|----------|------|-------------|
| `state` | Ja | String | Der Zustand Ihrer Logik-App bei der Bereitstellung, wobei `Enabled` bedeutet, dass Ihre Logik-App aktiv ist, und `Disabled` bedeutet, dass Ihre Logik-App inaktiv ist. Die Option `Disabled` können Sie beispielsweise verwenden, wenn Ihre Logik-App noch nicht aktiviert, aber bereits als Entwurfsversion bereitgestellt werden soll. |
| `integrationAccount` | Nein | Object | Wird in Ihrer Logik-App ein Integrationskonto verwendet, das Artefakte für B2B-Szenarien (Business-to-Business) speichert, enthält dieses Objekt das `id`-Attribut, das die ID für das Integrationskonto angibt. |
| `definition` | Ja | Object | Die Workflowdefinition, die Ihrer Logik-App zugrunde liegt. Diese Definition ist das Objekt, das in der Codeansicht angezeigt wird und im Thema [Schemareferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md) vollständig beschrieben ist. In dieser Workflowdefinition sind im `parameters`-Objekt die Parameter für die Werte deklariert, die zur Logik-App-Laufzeit verwendet werden sollen. Weitere Informationen hierzu finden Sie unter [Workflowdefinition und -parameter](#workflow-definition-parameters). <p><p>Um die Attribute anzuzeigen, die zur Workflowdefinition ihrer Logik-App gehören, wechseln Sie im Azure-Portal oder in Visual Studio von der „Entwurfsansicht“ in die „Codeansicht“, oder verwenden Sie ein Tool wie [Azure-Ressourcen-Explorer](https://resources.azure.com). |
| `parameters` | Nein | Object | Die zur [Workflowdefinition gehörenden Parameterwerte](#workflow-definition-parameters), die zur Logik-App-Laufzeit verwendet werden sollen. Die Parameterdefinitionen für diese Werte sind im [„parameters“-Objekt Ihrer Workflowdefinition](#workflow-definition-parameters) enthalten. Wenn in Ihrer Logik-App [verwaltete Connectors](../connectors/apis-list.md) für den Zugriff auf andere Dienste und Systeme verwendet werden, enthält dieses Objekt außerdem ein `$connections`-Objekt, das die zur Laufzeit zu verwendenden Verbindungswerte festlegt. |
| `accessControl` | Nein | Object | Hiermit werden Sicherheitsattribute für Ihre Logik-App angegeben, etwa Einschränken des IP-Zugriffs auf Anforderungstrigger oder Ausführungsverlaufseingaben und -ausgaben. Weitere Informationen finden Sie unter [Schützen des Zugriffs und der Daten in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md). |
| `runtimeConfiguration` | Nein | Object | Zum Angeben von `operationOptions`-Eigenschaften, mit denen gesteuert wird, wie sich Ihre Logik-App zur Laufzeit verhält. Sie können beispielsweise Ihre Logik-App in einem [Modus mit hohem Durchsatz](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) ausführen. |
|||||

Weitere Informationen zu Ressourcendefinitionen für diese Logic Apps-Objekte finden Sie unter [Microsoft.Logic-Ressourcentypen](/azure/templates/microsoft.logic/allversions):

* [Workflowressourcendefinition](/azure/templates/microsoft.logic/workflows)
* [Integrationsdienstumgebungs-Ressourcendefinition](/azure/templates/microsoft.logic/integrationserviceenvironments)
* [Integrationsdienstumgebung: verwaltete API-Ressourcendefinition](/azure/templates/microsoft.logic/integrationserviceenvironments/managedapis)

* [Integrationskonto-Ressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts)

* Integrationskontoartefakte:

  * [Vereinbarungsressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/agreements)

  * [Assmblyressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/assemblies)

  * [Batchkonfigurations-Ressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/batchconfigurations)

  * [Zertifikatressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/certificates)

  * [Zuordnungsressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/maps)

  * [Partnerressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/partners)

  * [Schemaressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/schemas)

  * [Sitzungsressourcendefinition](/azure/templates/microsoft.logic/integrationaccounts/sessions)

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Workflowdefinition und -parameter

Die Workflowdefinition ihrer Logik-App ist im `definition`-Objekt enthalten, das im `properties`-Objekt in der Ressourcendefinition ihrer Logik-App enthalten ist. Dieses `definition`-Objekt ist mit dem Objekt identisch, das in der Codeansicht angezeigt wird und im Thema [Schemareferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md) vollständig beschrieben ist. Die Workflowdefinition enthält ein inneres `parameters`-Deklarationsobjekt, in dem Sie die Parameter für die Werte definieren, die in Ihrer Workflowdefinition zur Laufzeit verwendet werden. Sie können dann im Trigger oder in Aktionen in Ihrem Workflow auf diese Parameter verweisen. Standardmäßig ist dieses `parameters`-Objekt leer. Dies gilt nur dann nicht, wenn in Ihrer Logik-App [verwaltete Connectors](../connectors/apis-list.md) verwendet werden, um Verbindungen mit anderen Diensten und Systemen herzustellen.

Um die Werte für Workflowdefinitionsparameter festzulegen, verwenden Sie das `parameters`-Objekt, das sich *außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition Ihrer Logik-App befindet. In diesem äußeren `parameters`-Objekt können Sie dann auf Ihre zuvor deklarierten Vorlagenparameter verweisen, die bei einer Bereitstellung Werte aus einer Parameterdatei übernehmen können.

> [!TIP]
>
> Es empfiehlt sich, dass Sie nicht direkt aus der Workflowdefinition auf Vorlagenparameter verweisen, die bei der Bereitstellung ausgewertet werden. Stattdessen sollten Sie einen Workflowdefinitionsparameter deklarieren, den Sie dann in dem `parameters`-Objekt festlegen können, das sich *außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition Ihrer Logik-App befindet. Weitere Informationen finden Sie unter [Verweise auf Parameter](#parameter-references).

In der folgenden Syntax ist zu sehen, wo Sie Parameter sowohl auf der Vorlagen- als auch der Workflowdefinitionsebene deklarieren können, und es ist zu sehen, wo Sie diese Parameterwerte festlegen können, indem Sie auf die Vorlagen- und Workflowdefinitionsparameter verweisen:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Sichere Workflowdefinitionsparameter

Deklarieren Sie einen Workflowdefinitionsparameter, der zur Laufzeit vertrauliche Informationen, Kennwörter, Zugriffsschlüssel oder Geheimnisse enthält, mit dem Parametertyp `securestring` oder `secureobject`. Auf diesen Parameter können Sie durchgängig und innerhalb Ihrer Workflowdefinition verweisen. Deklarieren Sie auf der obersten Ebene der Vorlage einen Parameter, der denselben Typ hat, um diese Informationen während der Bereitstellung zu verarbeiten.

Um den Wert für den Workflowdefinitionsparameter festzulegen, verwenden Sie das `parameters`-Objekt, das sich *außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition Ihrer Logik-App befindet, um auf den Vorlagenparameter zu verweisen. Zum Schluss speichern Sie diesen Wert, um ihn bei der Bereitstellung an Ihren Vorlagenparameter zu übergeben, in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md), und verweisen Sie auf diesen Schlüsseltresor in der [Parameterdatei](#template-parameter-files), die bei der Bereitstellung von Ihrer Vorlage verwendet wird.

In dieser Beispielvorlage ist gezeigt, wie Sie diese Aufgaben durchführen können, indem Sie bei Bedarf geschützte (sichere) Parameter definieren, damit Sie deren Werte in Azure Key Vault speichern können:

* Deklarieren Sie geschützte Parameter für die Werte, die zum Authentifizieren von Zugriffen verwendet werden.
* Verwenden Sie diese Werte sowohl auf der Vorlagen- als auch auf der Workflowdefinitionsebene.
* Stellen Sie diese Werte über eine Parameterdatei bereit.

**Vorlage**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Parameterdatei**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Bewährte Methoden: Workflowdefinitionsparameter

Gehen Sie entsprechend den folgenden bewährten Methoden vor, um sicherzustellen, dass der Logik-App-Designer Workflowdefinitionsparameter korrekt anzeigen kann:

* Fügen Sie das `defaultValue`-Attribut, das leere Werte angeben kann, für alle Parameter ein, die keine sensiblen oder zu schützenden Daten enthalten.

* Verwenden Sie für Benutzernamen, Kennwörter und Geheimnisse immer geschützte Parameter. Um sensible Parameterwerte auszublenden oder zu schützen, befolgen Sie die Anweisungen in den folgenden Themen:

  * [Sicherheitsempfehlungen für Aktionsparameter](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Sicherheitsempfehlungen für Parameter in Workflowdefinitionen](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Übergeben von sicheren Parameterwerten mit Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Weitere Informationen zu Workflowdefinitionsparametern finden Sie unter [Parameter – Schemareferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Verbindungsressourcendefinitionen

Werden in Ihrer Logik-App Verbindungen mit anderen Diensten und Systemen erstellt und verwendet, indem [verwaltete Connectors](../connectors/apis-list.md) verwendet werden, enthält Ihr `resources`-Objekt die Ressourcendefinitionen für diese Verbindungen. Obwohl Sie Verbindungen in einer Logik-App erstellen, sind Verbindungen separate Azure-Ressourcen mit eigenen Ressourcendefinitionen. Zum Überprüfen dieser Verbindungsressourcendefinitionen [laden Sie die Logik-App von Azure in Visual Studio herunter](../logic-apps/manage-logic-apps-with-visual-studio.md). Dies ist die einfachste Möglichkeit, eine gültige parametrisierte Logik-App-Vorlage zu erstellen, die größtenteils für die Bereitstellung bereit ist.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

In Verbindungsressourcendefinitionen wird für deren Werte auf Parameter auf der obersten Ebene der Vorlage verwiesen. Dies bedeutet, dass Sie diese Werte bei der Bereitstellung über eine Parameterdatei bereitstellen können. Achten Sie darauf, dass für Verbindungen dieselbe Azure-Ressourcengruppe und derselbe Azure-Speicherort wie für Ihre Logik-App verwendet werden.

Es folgt eine Beispielressourcendefinition für eine Office 365 Outlook-Verbindung und die entsprechenden Vorlagenparameter:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

Die Ressourcendefinition ihrer Logik-App funktioniert auch auf folgende Weise mit Verbindungsressourcendefinitionen:

* In Ihrer Workflowdefinition ist im `parameters`-Objekt ein `$connections`-Parameter für die Verbindungswerte deklariert, die zur Logik-App-Laufzeit verwendet werden sollen. Außerdem werden in dem Trigger oder der Aktion, in dem oder der eine Verbindung hergestellt wird, die entsprechenden Werte verwendet, die durch diesen `$connections`-Parameter übergeben werden.

* *Außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition ihrer Logik-App, werden in einem anderen `parameters`-Objekt die Werte festgelegt, die zur Laufzeit für den `$connections`-Parameter verwendet werden sollen, indem auf die entsprechenden Vorlagenparameter verwiesen wird. Für diese Werte werden Vorlagenausdrücke verwendet, um auf Ressourcen zu verweisen, in denen die Metadaten für die Verbindungen in ihrer Logik-App sicher gespeichert sind.

  Metadaten können z. B. Verbindungszeichenfolgen und Zugriffstoken enthalten, die Sie in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) speichern können. Um diese Werte an Ihre Vorlagenparameter zu übergeben, verweisen Sie in der [Parameterdatei](#template-parameter-files), die während der Bereitstellung für Ihre Vorlage verwendet wurde, auf diesen Schlüsseltresor. Weitere Informationen zu den Unterschieden beim Verweisen auf Parameter finden Sie weiter unten in diesem Thema unter [Verweise auf Parameter](#parameter-references).

  Wenn Sie die Workflowdefinition ihrer Logik-App im Azure-Portal oder in Visual Studio in der Codeansicht öffnen, wird das `$connections`-Objekt außerhalb Ihrer Workflow Definition, aber auf derselben Ebene angezeigt. Diese Reihenfolge in der Codeansicht erleichtert das Verweisen auf diese Parameter, wenn Sie die Workflowdefinition manuell aktualisieren:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* Die Ressourcendefinition ihrer Logik-App hat ein `dependsOn`-Objekt, in dem die Abhängigkeiten für die Verbindungen angegeben sind, die von ihrer Logik-App verwendet werden.

Jede Verbindung, die Sie erstellen, hat einen eindeutigen Namen in Azure. Wenn Sie mehrere Verbindungen mit demselben Dienst oder System erstellen, wird an jeden Verbindungsnamen eine Zahl angefügt, die mit jeder neu erstellten Verbindung inkrementiert wird, beispielsweise `office365`, `office365-1` usw.

In diesem Beispiel sind die Interaktionen zwischen der Ressourcendefinition ihrer Logik-App und einer Verbindungsressourcendefinition für Office 365 Outlook veranschaulicht:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Sichere Verbindungsparameter

Für einen Verbindungsparameter, der vertrauliche Informationen, Kennwörter, Zugriffsschlüssel oder Geheimnisse beinhaltet, enthält die Ressourcendefinition der Verbindung ein `parameterValues`-Objekt, in dem diese Werte im Format eines Name-Wert-Paars angegeben sind. Um diese Informationen auszublenden, können Sie die Vorlagenparameter für diese Werte mit dem Parametertyp `securestring` oder `secureobject` deklarieren. Diese Informationen können Sie dann in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) speichern. Um diese Werte an Ihre Vorlagenparameter zu übergeben, verweisen Sie in der [Parameterdatei](#template-parameter-files), die während der Bereitstellung für Ihre Vorlage verwendet wurde, auf diesen Schlüsseltresor.

Es folgt ein Beispiel, in dem der Kontoname und der Zugriffsschlüssel für eine Azure Blob Storage-Verbindung bereitstellt werden:

**Parameterdatei**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Vorlage**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Authentifizieren von Verbindungen

Nach der Bereitstellung funktioniert Ihre Logik-App vollständig mit gültigen Parametern. Allerdings müssen Sie weiterhin jede OAuth-Verbindung autorisieren, um gültige Zugriffstoken zum [Authentifizieren Ihrer Anmeldeinformationen](../active-directory/develop/authentication-vs-authorization.md) zu generieren. Weitere Informationen finden Sie unter [Autorisieren von OAuth-Verbindungen](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections).

Für einige Verbindungen wird die Verwendung eines Azure AD-[Dienstprinzipals](../active-directory/develop/app-objects-and-service-principals.md) (Azure Active Directory) unterstützt, um Verbindungen für eine Logik-App zu autorisieren, die [in Azure AD registriert ist](../active-directory/develop/quickstart-register-app.md). Als Beispiel ist in dieser Azure Data Lake-Verbindungsressourcendefinition gezeigt, wie auf die Vorlagenparameter verwiesen wird, die die Informationen des Dienstprinzipals enthalten, und wie diese Parameter in der Vorlage deklariert sind:

**Verbindungsressourcendefinition**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| attribute | BESCHREIBUNG |
|-----------|-------------|
| `token:clientId` | Die Anwendungs- oder Client-ID, die Ihrem Dienstprinzipal zugeordnet ist |
| `token:clientSecret` | Der Schlüsselwert, der Ihrem Dienstprinzipal zugeordnet ist |
| `token:TenantId` | Die Verzeichnis-ID für Ihren Azure AD-Mandanten |
| `token:grantType` | Der angeforderte Gewährungstyp, der gleich `client_credentials` sein muss. Weitere Informationen finden Sie unter [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). |
|||

**Vorlagenparameterdefinitionen**

In dem auf oberster Vorlagenebene befindlichen `parameters`-Objekt sind diese Parameter für die Beispielverbindung deklariert:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Weitere Informationen zum Arbeiten mit Dienstprinzipale finden Sie in den folgenden Themen:

* [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md)
* [Erstellen eines Azure-Dienstprinzipals mit Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps)
* [Gewusst wie: Verwenden von Azure PowerShell zum Erstellen eines Dienstprinzipals mit einem Zertifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Verweise auf Parameter

Um auf Vorlagenparameter zu verweisen, können Sie Vorlagenausdrücke mit [Vorlagenfunktionen](../azure-resource-manager/templates/template-functions.md) verwenden, die bei einer Bereitstellung ausgewertet werden. Für Vorlagenausdrücke werden eckige Klammern (**[]**) verwendet:

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Um auf Workflowdefinitionsparameter zu verweisen, verwenden Sie [Workflowdefinitions-Sprachausdrücke und -Funktionen](../logic-apps/workflow-definition-language-functions-reference.md), die zur Laufzeit ausgewertet werden. Möglicherweise bemerken Siet, dass einige Vorlagenfunktionen und Workflowdefinitionsfunktionen denselben Namen haben. Ein Workflowdefinitionsausdruck beginnt mit dem „At“-Symbol **(@** ):

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

Sie können Vorlagenparameterwerte an die Workflowdefinition für Ihre Logik-App übergeben, um die Werte zur Laufzeit zu verwenden. Sie sollten allerdings keine Vorlagenparameter, -ausdrücke und -syntax in Ihrer Workflowdefinition verwenden, da der Logik-App-Designer keine Vorlagenelemente unterstützt. Außerdem können Vorlagensyntax und -ausdrücke Code wegen Unterschieden dahingehend, wann Ausdrücke ausgewertet werden, komplizierter machen.

Führen Sie stattdessen die folgenden allgemeinen Schritte aus, um folgende Aufgaben zu erledigen: Deklarieren der und Verweisen auf die Workflowdefinitionsparameter, die zur Laufzeit verwendet werden sollen, Deklarieren der und Verweisen auf die Vorlagenparameter, die bei einer Bereitstellung verwendet werden sollen, und Angeben der Werte, die bei einer Bereitstellung über eine Parameterdatei übergeben werden sollen. Ausführliche Informationen finden weiter oben in diesem Thema im Abschnitt [Workflowdefinition und -parameter](#workflow-definition-parameters).

1. Erstellen Sie Ihre Vorlage, und deklarieren Sie die Vorlagenparameter für die Werte, die bei der Bereitstellung übernommen und verwendet werden sollen.

1. Deklarieren Sie in Ihrer Workflowdefinition die Parameter für die Werte, die zur Laufzeit übernommen und verwendet werden sollen. Auf diese Werte können Sie dann durchgängig und innerhalb Ihrer Workflowdefinition verweisen.

1. Legen Sie in dem `parameters`-Objekt, das sich *außerhalb* Ihrer Workflowdefinition, aber noch *innerhalb* der Ressourcendefinition ihrer Logik-App befindet, die Werte für Ihre Workflowdefinitionsparameter fest, indem Sie auf die entsprechenden Vorlagenparameter verweisen. Auf diese Weise können Sie Vorlagenparameterwerte an die Workflowdefinitionsparameter übergeben.

1. Geben Sie in der Parameterdatei die Werte für die Vorlage an, die bei einer Bereitstellung verwendet werden sollen.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Vollständige Beispielvorlage

Nachstehend ist die parametrisierte Beispielvorlage aufgeführt, die in den Beispielen dieses Themas verwendet wird:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen von Logik-App-Vorlagen](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
