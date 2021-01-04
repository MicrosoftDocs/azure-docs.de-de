---
title: 'Schnellstart: Bereitstellen von Azure API for FHIR mit einer ARM-Vorlage'
description: In diesem Schnellstart erfahren Sie, wie Sie Azure API for Fast Healthcare Interoperability Resources (FHIR®) mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: cavoeg
ms.date: 10/06/2020
ms.openlocfilehash: 0cc31c26a71775dfdf3e9ee94852fda8af2c0cfc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844538"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-api-for-fhir"></a>Schnellstart: Verwenden Sie eine ARM-Vorlage zum Bereitstellen von Azure API for FHIR

In diesem Schnellstart erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um Azure API for Fast Healthcare Interoperability Resources (FHIR®) bereitzustellen. Sie können Azure API for FHIR über das Azure-Portal, mit PowerShell oder mit der CLI bereitstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet, sobald Sie sich anmelden.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen eines Azure API for FHIR-Diensts mithilfe einer ARM-Vorlage im Azure-Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten, verwenden Sie [Azure PowerShell](/powershell/azure/install-az-ps).

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

* Ein Azure-Konto mit einem aktiven Abonnement. [Erstellen Sie ein kostenloses Konto.](https://azure.microsoft.com/free/)
* Wenn Sie den Code lokal ausführen möchten:
    * Eine Bash-Shell (z.B. Git Bash, in [Git für Windows](https://gitforwindows.org) enthalten).
    * [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

---

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-azure-api-for-fhir/).

:::code language="json" source="~/quickstart-templates/101-azure-api-for-fhir/azuredeploy.json":::

Die Vorlage definiert eine Azure-Ressource:

* **Microsoft.HealthcareApis/services**

<!--

Replace the line above with the following line once https://docs.microsoft.com/azure/templates/microsoft.healthcareapis/services goes live:

* [**Microsoft.HealthcareApis/services**](/azure/templates/microsoft.healthcareapis/services)

-->

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie den folgenden Link aus, um Azure API for FHIR mit einer ARM-Vorlage im Azure-Portal bereitzustellen:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen eines Azure API for FHIR-Diensts mithilfe der ARM-Vorlage im Azure-Portal.":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-azure-api-for-fhir%2fazuredeploy.json)

Auf der Seite **Azure API for FHIR bereitstellen**:

1. Wenn Sie möchten, ändern Sie das **Abonnement** aus dem Standardabonnement in ein anderes Abonnement.

2. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und wählen Sie **OK** aus.

3. Wenn Sie eine neue Ressourcengruppe erstellt haben, wählen Sie eine **Region** für die Ressourcengruppe aus.

4. Geben Sie einen neuen **Dienstnamen** ein, und wählen Sie den **Standort** für Azure API for FHIR aus. Der Standort kann mit dem Standort der Ressourcengruppe identisch sein oder sich davon unterscheiden.

    :::image type="content" source="./media/fhir-paas-arm-template-quickstart/deploy-azure-api-fhir.png" alt-text="Bereitstellen von Azure API for FHIR mit einer ARM-Vorlage im Azure-Portal.":::

5. Klicken Sie auf **Überprüfen + erstellen**.

6. Lesen Sie die Geschäftsbedingungen, und wählen Sie dann **Erstellen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Wenn Sie die PowerShell-Skripts lokal ausführen möchten, geben Sie zunächst `Connect-AzAccount` ein, um Ihre Azure-Anmeldeinformationen einzurichten.

Wenn der `Microsoft.HealthcareApis`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren. Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

Verwenden Sie den folgenden Code, um den Azure API for FHIR-Dienst mit der ARM-Vorlage bereitzustellen. Der Code fordert Sie auf, den Namen des neuen FHIR-Diensts, den Namen einer neuen Ressourcengruppe und die Speicherorte für beides anzugeben.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure API for FHIR service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion
Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure API for FHIR service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json `
    -serviceName $serviceName `
    -location $serviceLocation
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Wenn der `Microsoft.HealthcareApis`-Ressourcenanbieter noch nicht für Ihr Abonnement registriert ist, können Sie ihn mit dem folgenden interaktiven Code registrieren. Wählen Sie zum Ausführen des Codes in Azure Cloud Shell in der oberen Ecke eines beliebigen Codeblocks **Ausprobieren** aus.

```azurecli-interactive
az extension add --name healthcareapis
```

Verwenden Sie den folgenden Code, um den Azure API for FHIR-Dienst mit der ARM-Vorlage bereitzustellen. Der Code fordert Sie auf, den Namen des neuen FHIR-Diensts, den Namen einer neuen Ressourcengruppe und die Speicherorte für beides anzugeben.

```azurecli-interactive
read -p "Enter a name for the new Azure API for FHIR service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
params='serviceName='$serviceName' location='$serviceLocation &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure API for FHIR service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-api-for-fhir/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Die Bereitstellung dauert einige Minuten. Notieren Sie sich die Namen für den Azure API for FHIR-Dienst und die Ressourcengruppe, die Sie verwenden, um die bereitgestellten Ressourcen später zu überprüfen.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine Übersicht über Ihren neuen Azure API for FHIR-Dienst anzuzeigen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure API for FHIR**, und wählen Sie diese Option dann aus.

2. Wählen Sie in der FHIR-Liste den neuen Dienst aus. Die Seite **Übersicht** für den neuen Azure API for FHIR-Dienst wird angezeigt.

3. Um zu überprüfen, ob das neue FHIR-API-Konto bereitgestellt wurde, wählen Sie den Link neben **FHIR-Metadatenendpunkt** aus, um die FHIR-API-Funktionsanweisung abzurufen. Der Link weist das Format `https://<service-name>.azurehealthcareapis.com/metadata` auf. Wenn das Konto bereitgestellt wird, wird eine große JSON-Datei angezeigt.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure API for FHIR-Dienst anzuzeigen. Sie müssen den Namen des neuen Diensts und die Ressourcengruppe eingeben.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure API for FHIR service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzHealthcareApisService -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned"

$requestUri="https://" + $serviceName + ".azurehealthcareapis.com/metadata"
$metadata = Invoke-WebRequest -Uri $requestUri
$metadata.RawContent
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrem Azure API for FHIR-Dienst anzuzeigen. Sie müssen den Namen des neuen Diensts und die Ressourcengruppe eingeben.

```azurecli-interactive
read -p "Enter the name of your Azure API for FHIR service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName" &&
az healthcareapis service show --resource-group $resourceGroupName --resource-name $serviceName &&
read -p "Press [ENTER] to fetch the FHIR API capability statement, which shows that the new service has been provisioned: " &&
requestUrl='https://'$serviceName'.azurehealthcareapis.com/metadata' &&
curl --url $requestUrl &&
read -p "Press [ENTER] to continue: "
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Ressourcen in der Ressourcengruppe gelöscht.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die entsprechende Option aus.

2. Wählen Sie in der Liste der Ressourcengruppen den Namen Ihrer Ressourcengruppe aus.

3. Wählen Sie auf der Seite **Übersicht** der Ressourcengruppe die Option **Ressourcengruppe löschen** aus.

4. Geben Sie im Bestätigungsdialogfeld den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to delete"
Write-Verbose "Remove-AzResourceGroup -Name $resourceGroupName" -Verbose
Remove-AzResourceGroup -Name $resourceGroupName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
read -p "Enter the name of the resource group to delete: " resourceGroupName &&
echo "DELETE A RESOURCE GROUP (AND ITS RESOURCES):  az group delete --name $resourceGroupName" &&
az group delete --name $resourceGroupName &&
read -p "Press [ENTER] to continue: "
```

---

Ein Tutorial mit schrittweisen Anleitungen zum Erstellen einer ARM-Vorlage finden Sie im [Tutorial zum Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Azure API for FHIR in Ihrem Abonnement bereitgestellt. Wenn Sie zusätzliche Einstellungen in Azure API for FHIR festlegen möchten, fahren Sie mit der Schrittanleitung für zusätzliche Einstellungen fort. Wenn Sie Azure API for FHIR verwenden möchten, informieren Sie sich ausführlicher über das Registrieren von Anwendungen.

>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen in Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Registrieren der Azure Active Directory-Apps für Azure API for FHIR](fhir-app-registration.md)
