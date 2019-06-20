---
title: Bereitstellen von Logik-Apps mit Azure Resource Manager-Vorlagen – Azure Logic Apps
description: Bereitstellen von Logik-Apps mit Azure Resource Manager-Vorlagen
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 21603ff213bc8babb4145209a76aee0b4150cc12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60681754"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Bereitstellen von Logik-Apps mit Azure Resource Manager-Vorlagen

Nachdem Sie eine Azure Resource Manager-Vorlage für die Bereitstellung Ihrer Logik-App erstellt haben, können Sie Ihre Vorlage folgendermaßen bereitstellen:

* [Azure-Portal](#portal)
* [Azure PowerShell](#powershell)
* [Azure-Befehlszeilenschnittstelle](#cli)
* [Azure-Ressourcen-Manager-REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps mit Azure Pipelines](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Bereitstellen über das Azure-Portal

Um eine Logik-App-Vorlage automatisch in Azure bereitzustellen, können Sie die folgende Schaltfläche **In Azure bereitstellen** auswählen. Darüber werden Sie im Azure-Portal angemeldet und zur Eingabe von Informationen zu Ihrer Logik-App aufgefordert. Sie können dann alle notwendigen Änderungen an der Logik-App-Vorlage oder den Parametern vornehmen.

[![In Azure bereitstellen](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Sie werden beispielsweise nach der Anmeldung beim Azure-Portal zur Eingabe dieser Informationen aufgefordert:

* Name des Azure-Abonnements
* Gewünschte Ressourcengruppe
* Speicherort der Logik-App
* Der Name Ihrer Logik-App
* Test-URI
* Annahme der angegebenen Bedingungen

Weitere Informationen hierzu finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-Portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autorisieren von OAuth-Verbindungen

Nach der Bereitstellung funktioniert die Logik-App vollständig mit gültigen Parametern. Sie müssen OAuth-Verbindungen aber dennoch autorisieren, um ein gültiges Zugriffstoken zu generieren. Für automatisierte Bereitstellungen können Sie ein Skript verwenden, das jeder OAuth-Verbindung zustimmt, wie beispielsweise dieses [Beispielskript im GitHub-Projekt „LogicAppConnectionAuth“](https://github.com/logicappsio/LogicAppConnectionAuth). Sie können OAuth-Verbindungen auch über das Azure-Portal oder in Visual Studio autorisieren, indem Sie Ihre Logik-App im Designer für Logik-Apps öffnen.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Bereitstellen mit Azure PowerShell

Um für eine bestimmte *Azure-Ressourcengruppe* bereitzustellen, verwenden Sie diesen Befehl:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Um für ein bestimmtes Azure-Abonnement bereitzustellen, verwenden Sie diesen Befehl:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](/powershell/module/az.resources/new-azresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Um für eine bestimmte *Azure-Ressourcengruppe* bereitzustellen, verwenden Sie diesen Befehl:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Um für ein bestimmtes Azure-Abonnement bereitzustellen, verwenden Sie diesen Befehl:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Weitere Informationen finden Sie in den folgenden Themen: 

* [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure-CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Bereitstellen mit Azure DevOps

Um Logik-App-Vorlagen bereitzustellen und Umgebungen zu verwalten, verwenden Teams häufig ein Tool wie [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines enthält eine [Aufgabe für die Bereitstellung von Azure-Ressourcengruppen](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2), die jeder Build- oder Releasepipeline hinzugefügt werden kann.
Für die Autorisierung zur Bereitstellung und Generierung der Releasepipeline benötigen Sie außerdem ein Azure Active Directory (AD)-[Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md). Weitere Informationen zum [Verwenden von Dienstprinzipalen mit Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Hier sind allgemeine Schritte für die Verwendung von Azure Pipelines:

1. Erstellen Sie in Azure Pipelines eine leere Pipeline.

1. Wählen Sie die Ressourcen, die Sie für die Pipeline benötigen, wie z.B. Ihre Logik-App-Vorlage und Ihre Parameterdateien für die Vorlagen, die Sie manuell oder im Rahmen des Build-Prozesses generieren.

1. Suchen Sie die Aufgabe **Bereitstellung einer Azure-Ressourcengruppe** und fügen Sie sie zum Agent-Auftrag hinzu.

   ![Hinzufügen der Aufgabe „Bereitstellung einer Azure-Ressourcengruppe“](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Führen Sie die Konfiguration mit einem [Dienstprinzipal](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure) durch. 

1. Fügen Sie Verweise auf Ihre Logik-App-Vorlage und Parameterdateien für die Vorlage hinzu.

1. Erstellen Sie nach Bedarf weitere Schritte im Freigabeprozess für andere Umgebungen, automatisierte Tests oder genehmigende Personen.

## <a name="get-support"></a>Support

Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überwachen von Logik-Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md)
