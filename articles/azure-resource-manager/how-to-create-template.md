---
title: So erstellen Sie Azure Resource Manager-Vorlagen
description: In diesem Artikel wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage erstellen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60551001"
---
# <a name="create-azure-resource-manager-template"></a>Erstellen einer Azure Resource Manager-Vorlage

In diesem Artikel wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage erstellen und welche Entscheidungen Sie dabei treffen müssen. Sie erhalten eine Übersicht zu Beispielen und Features, die Ihnen beim Erstellen Ihrer Vorlage helfen können. In diesem Artikel wird vorausgesetzt, dass Sie Ressourcen für eine Ressourcengruppe bereitstellen. Wenn Sie Ressourcen für Ihr Azure-Abonnement bereitstellen müssen, also z. B. Azure-Richtlinien oder Aufgaben für die rollenbasierte Zugriffssteuerung erstellen, finden Sie weitere Informationen unter [Erstellen von Ressourcengruppen und Ressourcen für ein Azure-Abonnement](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Auswählen des JSON-Editors

Es handelt sich bei der JSON-Datei um eine Resource Manager-Vorlage. Sie benötigen ein gutes Erstellungstool, um an der JSON-Datei zu arbeiten. Sie können verschiedene Tools verwenden, wenn Sie aber keinen bestimmten Editor bevorzugen, wird die Installation von [Visual Studio Code (VS Code)](https://code.visualstudio.com/) empfohlen. 

Wenn Sie dies erledigt haben, fügen Sie die [Erweiterung für Azure Resource Manager-Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) hinzu. Diese Erweiterung umfasst einige Features, die das Erstellen von Vorlagen erleichtern.

![Visual Studio Code-Vorlage](./media/how-to-create-template/template-visual-studio-code.png)

Auf diesem Screenshot sehen Sie eine in VS Code geöffnete Resource Manager-Vorlage. 

Ein Tutorial zum Installieren der Erweiterung für Resource Manager-Tools und zum Verwenden von VS Code finden Sie unter [Schnellstart: Erstellen von Azure Resource Manager-Vorlagen mit Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>Grundlegendes zur Vorlagenstruktur

Nachfolgend werden die einzelnen Bestandteile der Vorlage erläutert, damit Sie ein besseres Verständnis über deren Funktionsweise erlangen. Möglicherweise weist Ihre Vorlage nicht alle Abschnitte auf. Konzentrieren Sie sich auf die folgenden Abschnitte:

* Der Abschnitt [parameters](resource-group-authoring-templates.md#parameters) enthält die Werte, die Sie bei der Bereitstellung angeben können, um die bereitgestellte Infrastruktur anzupassen. 

* Der Abschnitt [variables](resource-group-authoring-templates.md#variables) enthält die Werte, die in der gesamten Vorlage verwendet werden.

* Der Abschnitt [functions](resource-group-authoring-templates.md#functions) enthält die angepassten Vorlagenausdrücke, die in Ihrer Vorlage verwendet werden.

* Der Abschnitt [resources](resource-group-authoring-templates.md#resources) enthält die Azure-Ressourcen, die für Ihr Abonnement bereitgestellt werden.

* Der Abschnitt [outputs](resource-group-authoring-templates.md#outputs) enthält die Werte, die nach Abschluss der Bereitstellung zurückgegeben werden.

## <a name="look-for-similar-templates"></a>Suchen nach ähnlichen Vorlagen

Häufig gibt es bereits eine Vorlage zur Bereitstellung einer Lösung, die größtenteils Ihren Anforderungen genügt. Unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/) finden Sie Hunderte von Vorlagen der Community.

![Schnellstartvorlagenrepository](./media/how-to-create-template/template-quickstart-repo.png)

Durchsuchen Sie dieses Repository nach einer Vorlage, die Ihren Anforderungen größtenteils genügen. Die Vorlage muss nicht genau Ihren Anforderungen entsprechen, da Sie sie bearbeiten können.

Wenn Sie eine Vorlage gefunden haben, klicken Sie auf **Browse on Github** (Auf GitHub suchen), und kopieren Sie die **azuredeploy.json**-Datei aus dem Repository. Erstellen Sie in VS Code eine Datei mit dem Namen **azuredeploy.json**, und fügen Sie die Inhalte der Vorlagendatei hinzu, die Sie aus dem Schnellstartrepository kopiert haben.

## <a name="add-resources"></a>Hinzufügen von Ressourcen

Damit die Vorlage genau Ihren Anforderungen entspricht, sollten Sie diese anpassen. Prüfen Sie dafür zunächst, welche Ressourcen bereitgestellt werden. Möglicherweise müssen Sie Ressourcen zur Vorlage hinzufügen, aus ihr entfernen oder in ihr ändern. Beschreibungen und Syntaxbeispiele finden Sie in der [Referenz zu Azure Resource Manager-Vorlagen](/azure/templates/).

![Vorlagenreferenz](./media/how-to-create-template/template-reference.png)

Nehmen Sie alle erforderlichen Änderungen vor, wenn Sie diese Eigenschaften überprüft haben. Empfehlungen zum Definieren von Ressourcen finden Sie unter [resources - recommended practices (Ressourcen: empfohlene Vorgehensweisen)](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Hinzufügen oder Entfernen von Parametern

Möglicherweise müssen Sie auch die Parameter Ihrer Vorlage anpassen. In Abhängigkeit davon, wie hoch das Maß an Anpassungen bei der Bereitstellung sein soll, können Sie Parameter hinzufügen oder entfernen. Empfehlungen zur Verwendung von Parametern finden Sie unter [parameters - recommended practices (Parameter: empfohlene Vorgehensweisen)](template-best-practices.md#parameters).

## <a name="add-tags"></a>Hinzufügen von Tags

Sie können Tags zu Ihren Ressourcen hinzufügen, um diese auf logische Weise nach Kategorien zu sortieren, und Abrechnungskosten aufteilen. Sie können ganz einfach Tags hinzufügen, indem Sie sie in der JSON-Datei für die Ressource anwenden. Beispielsweise weist das folgende Speicherkonto zwei Tags auf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

Sie können die Tags auch auf dynamische Weise über Parameter anwenden. Weitere Informationen finden Sie unter [tags in template (Tags in Vorlagen)](resource-group-using-tags.md#templates).

## <a name="review-template-functions"></a>Prüfen von Vorlagenfunktionen

Möglicherweise finden Sie Ausdrücke in Ihrer Vorlage, die in Klammern stehen, z. B. `"[some-expression]"`. Es handelt sich bei diesen Ausdrücken um Vorlagenfunktionen, mit denen Werte bei der Bereitstellung auf dynamische Weise erstellt werden.

Beispielsweise werden häufig Ausdrücke wie der folgende verwendet:

```json
"name": "[parameters('siteName')]"
```

Dieser Ausdruck ruft den Wert eines Parameters ab. Dieser Wert wird der Eigenschaft „name“ zugewiesen.

Möglicherweise stoßen Sie auch auf einen komplexeren Ausdruck wie den folgenden, der mehrere Funktionen verwendet:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Dieser Ausdruck ruft ein Objekt mit den Eigenschaften eines Speicherkontos ab.

Weitere Informationen zu den einzelnen Funktionen finden Sie in der [Referenzdokumentation zu den Vorlagenfunktionen](resource-group-template-functions.md).

## <a name="create-more-than-one-instance"></a>Erstellen mehrerer Instanzen

Manchmal müssen Sie mehr als eine Instanz einer Ressource erstellen. Dies ist z. B. der Fall, wenn Sie mehrere Speicherkonten benötigen. Sie können die `copy`-Syntax verwenden, um mehrere Instanzen anzugeben, anstatt die Ressource mithilfe Ihrer Vorlage mehrmals zu verwenden.

Im folgenden Beispiel werden drei Speicherkonten erstellt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Sie können die Anzahl der Instanzen auch auf dynamische Weise über einen Parameter angeben. Weitere Informationen finden Sie unter [Bereitstellen mehrerer Instanzen einer Ressource oder Eigenschaft in Azure Resource Manager-Vorlagen](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Bedingtes Bereitstellen einer Ressource

Manchmal müssen Sie bei der Bereitstellung angeben, ob eine Ressource in der Vorlage bereitgestellt wird. Dies ist z. B. der Fall, wenn Sie flexibel bleiben möchten, um entweder eine neue oder eine bereits vorhandene Ressource bereitzustellen. Mithilfe des `condition`-Elements können Sie die Bereitstellung für eine Ressource aktivieren oder deaktivieren. Wenn der Ausdruck im condition-Element den Wert TRUE hat, wird die Ressource bereitgestellt. Wenn der Wert FALSE lautet, wird die Ressource während der Bereitstellung übersprungen.

Das folgende Beispiel stellt bedingt ein Speicherkonto bereit:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Weitere Informationen finden Sie unter [condition-Element](resource-group-authoring-templates.md#condition).

## <a name="review-dependencies"></a>Prüfen von Abhängigkeiten

Einige Ressourcen in Ihrer Vorlage müssen vor anderen Ressourcen bereitgestellt werden. Beispielsweise muss bereits eine SQL-Server-Instanz vorhanden sein, bevor die SQL-Datenbank erstellt wird. Der Resource Manager legt auf implizite Weise die Reihenfolge für die Bereitstellung von Ressourcen fest, wenn die [reference-Funktion](resource-group-template-functions-resource.md#reference) verwendet wird. In einigen Fällen müssen Sie allerdings die Abhängigkeiten mithilfe des `dependsOn`-Elements explizit definieren. Überprüfen Sie, ob Abhängigkeiten zu Ihrer Vorlage hinzugefügt werden müssen. Achten Sie darauf, keine unnötigen Abhängigkeiten hinzuzufügen, da diese die Bereitstellung verlangsamen oder zu Zirkelbezügen führen können.

Die folgende Abbildung zeigt die Reihenfolge der Abhängigkeiten für verschiedene App Service-Ressourcen:

![Web-App-Abhängigkeiten](./media/how-to-create-template/web-dependencies.png)

Das folgende Beispiel zeigt einen Teil einer Vorlage, der Abhängigkeiten definiert.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Weitere Informationen finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in Azure Resource Manager-Vorlagen](resource-group-define-dependencies.md).

## <a name="review-recommended-practices"></a>Prüfen von empfohlenen Vorgehensweisen

Lesen Sie sich den Abschnitt [Azure Resource Manager template best practices (Bewährte Methoden für die Azure Resource Manager-Vorlage)](template-best-practices.md) durch, bevor Sie Ihre Vorlage bereitstellen. Möglicherweise stellen Sie dabei fest, dass sich einige empfohlene Ansätze zur Implementierung in Ihre Vorlage eignen.

Wenn Sie Ihre Vorlage in anderen Azure-Cloudumgebungen verwenden möchten, erhalten Sie weitere Informationen unter [Informationen zum Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](templates-cloud-consistency.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Bereitstellen einer Vorlage finden Sie unter [Bereitstellen mit der Azure CLI](resource-group-template-deploy-cli.md) oder [Bereitstellen mit PowerShell](resource-group-template-deploy.md).
* Einen ausführlichen Schnellstart zum Erstellen einer Vorlage finden Sie unter [Schnellstart: Erstellen von Azure Resource Manager-Vorlagen mit Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* Eine Liste der verfügbaren Funktionen in einer Vorlage finden Sie unter [Funktionen von Azure Resource Manager-Vorlagen](resource-group-template-functions.md).
