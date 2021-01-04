---
title: Erstellen einer App Service-App mithilfe einer Azure Resource Manager-Vorlage
description: Mit einer ARM-Vorlage (Azure Resource Manager) können Sie Ihre erste Azure App Service-App innerhalb von wenigen Sekunden erstellen. Hierbei handelt es sich um eine von vielen Bereitstellungsmöglichkeiten in App Service.
author: msangapu-msft
ms.author: msangapu
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 10/16/2020
ms.custom: subject-armqs, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5be4cea7d8ddb00a4c8a22a696dbdce1c062765c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183021"
---
# <a name="quickstart-create-app-service-app-using-an-arm-template"></a>Schnellstart: Erstellen einer App Service-App mit einer ARM-Vorlage

Beginnen Sie mit der Verwendung von [Azure App Service](overview.md), indem Sie zunächst eine App mit einer ARM-Vorlage (Azure Resource Manager) und der [Azure CLI](/cli/azure/get-started-with-azure-cli) in Cloud Shell in der Cloud bereitstellen. Da Sie einen kostenlosen App Service-Tarif verwenden, fallen bei dieser Schnellstartanleitung keine Kosten an.

 [!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

Verwenden Sie für die Bereitstellung unter **Linux** die folgende Schaltfläche:

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-linux%2Fazuredeploy.json)

Verwenden Sie für die Bereitstellung unter **Windows** die folgende Schaltfläche:

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-service-docs-windows%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Überprüfen der Vorlage

::: zone pivot="platform-windows"
Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-app-service-docs-windows). Sie stellt einen App Service-Plan und eine App Service-App unter Windows bereit. Zudem ist sie mit .NET Core, .NET Framework, PHP, Node.js und statischen HTML-Apps kompatibel. Informationen zu Java finden Sie unter [Erstellen einer Java-App](./quickstart-java.md).

:::code language="json" source="~/quickstart-templates/101-app-service-docs-windows/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App

Diese Vorlage enthält mehrere vordefinierte Parameter. In der folgenden Tabelle finden Sie Standardwerte und Beschreibungen für die Parameter:

| Parameter | type    | Standardwert                | Beschreibung |
|------------|---------|------------------------------|-------------|
| webAppName | Zeichenfolge  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | App-Name |
| location   | Zeichenfolge  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-Region |
| sku        | Zeichenfolge  | "F1"                         | Instanzgröße (F1 = Free-Tarif) |
| language   | Zeichenfolge  | ".net"                       | Sprachstapel (.NET, PHP, Node, HTML) |
| helloWorld | boolean | False                        | True = Hallo Welt-App bereitstellen |
| repoUrl    | Zeichenfolge  | " "                          | Externes Git-Repository (optional) |
::: zone-end
::: zone pivot="platform-linux"
Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-app-service-docs-linux). Sie stellt einen App Service-Plan und eine App Service-App unter Linux bereit. Zudem ist sie mit allen in App Service unterstützten Programmiersprachen kompatibel.

:::code language="json" source="~/quickstart-templates/101-app-service-docs-linux/azuredeploy.json":::

Zwei Azure-Ressourcen sind in der Vorlage definiert:

* [**Microsoft.Web/serverfarms**](/azure/templates/microsoft.web/serverfarms): erstellt einen App Service-Plan
* [**Microsoft.Web/sites**](/azure/templates/microsoft.web/sites): erstellt eine App Service-App

Diese Vorlage enthält mehrere vordefinierte Parameter. In der folgenden Tabelle finden Sie Standardwerte und Beschreibungen für die Parameter:

| Parameter | type    | Standardwert                | Beschreibung |
|------------|---------|------------------------------|-------------|
| webAppName | Zeichenfolge  | "webApp- **[`<uniqueString>`](../azure-resource-manager/templates/template-functions-string.md#uniquestring)** " | App-Name |
| location   | Zeichenfolge  | "[[resourceGroup().location](../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)]" | App-Region |
| sku        | Zeichenfolge  | "F1"                         | Instanzgröße (F1 = Free-Tarif) |
| linuxFxVersion   | Zeichenfolge  | "DOTNETCORE&#124;3.0        | Sprachstapel &#124; Version |
| repoUrl    | Zeichenfolge  | " "                          | Externes Git-Repository (optional) |

---
::: zone-end

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Hier wird die Azure CLI zum Bereitstellen der Vorlage verwendet. Sie können auch das Azure-Portal, Azure PowerShell oder die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-powershell.md).

Der folgende Code erstellt eine Ressourcengruppe, einen App Service-Plan und eine Web-App. Eine Standardressourcengruppe, ein App Service-Plan und ein Standort wurden für Sie festgelegt. Ersetzen Sie `<app-name>` durch einen global eindeutigen App-Namen (gültige Zeichen sind `a-z`, `0-9` und `-`).

::: zone pivot="platform-windows"
Führen Sie den folgenden Code aus, um eine .NET Framework-App unter Windows bereitzustellen.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup \
--parameters language=".net" helloWorld="true" webAppName="<app-name>" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-windows/azuredeploy.json"
::: zone-end
::: zone pivot="platform-linux"
Run the code below to create a Python app on Linux.

```azurecli-interactive
az group create --name myResourceGroup --location "southcentralus" &&
az deployment group create --resource-group myResourceGroup --parameters webAppName="<app-name>" linuxFxVersion="PYTHON|3.7" \
--template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-app-service-docs-linux/azuredeploy.json"
```

Aktualisieren Sie `linuxFxVersion` mit den entsprechenden Werten, um einen anderen Sprachstapel bereitzustellen. Im Anschluss finden Sie einige Beispiele. Führen Sie in Cloud Shell den folgenden Befehl aus, um die aktuellen Versionen anzuzeigen: `az webapp config show --resource-group myResourceGroup --name <app-name> --query linuxFxVersion`

| Sprache    | Beispiel                                              |
|-------------|------------------------------------------------------|
| **.NET**    | linuxFxVersion="DOTNETCORE&#124;3.0"                 |
| **PHP**     | linuxFxVersion="PHP&#124;7.4"                        |
| **Node.js** | linuxFxVersion="NODE&#124;10.15"                     |
| **Java**    | linuxFxVersion="JAVA&#124;1.8 &#124;TOMCAT&#124;9.0" |
| **Python**  | linuxFxVersion="PYTHON&#124;3.7"                     |
| **Ruby**    | linuxFxVersion="RUBY&#124;2.6"                       |

---
::: zone-end

> [!NOTE]
> [Hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sites) finden Sie weitere Beispiele für Azure App Service-Vorlagen.

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

Rufen Sie `http://<app_name>.azurewebsites.net/` auf, und überprüfen Sie, ob die Erstellung erfolgreich war.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[Löschen Sie die Ressourcengruppe](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), wenn Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen über lokales Git](deploy-local-git.md)

> [!div class="nextstepaction"]
> [ASP.NET Core mit SQL-Datenbank](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Python mit Postgres](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [PHP mit MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Schnellstart: Abfragen einer Azure SQL-Datenbank mithilfe von Java](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Zuordnen einer benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain.md)
