---
title: 'Schnellstart: Erstellen einer Azure SignalR Service-Instanz – ARM-Vorlage'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure SignalR Service-Instanz erstellen.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: zhshang
ms.date: 10/02/2020
ms.openlocfilehash: 4ab029048b37a4dcb44ef405249dcb9e20de70cf
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841614"
---
# <a name="quickstart-use-an-arm-template-to-deploy-azure-signalr-service"></a>Schnellstart: Verwenden einer ARM-Vorlage zum Bereitstellen von Azure SignalR Service

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine Azure SignalR Service-Instanz erstellen. Sie können die Azure SignalR Service-Instanz im Azure-Portal, mit PowerShell oder mit der CLI bereitstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet, sobald Sie sich anmelden.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Schaltfläche zum Bereitstellen von Azure SignalR Service in Azure mithilfe einer ARM-Vorlage im Azure-Portal":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

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

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-signalr/).

:::code language="json" source="~/quickstart-templates/101-signalr/azuredeploy.json":::

Die Vorlage definiert eine Azure-Ressource:

* [**Microsoft.SignalRService/SignalR**](/azure/templates/microsoft.signalrservice/signalr)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie den folgenden Link aus, um die Azure SignalR Service-Instanz per ARM-Vorlage im Azure-Portal bereitzustellen:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Schaltfläche zum Bereitstellen von Azure SignalR Service in Azure per ARM-Vorlage im Azure-Portal":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-signalr%2fazuredeploy.json)

Auf der Seite **Deploy an Azure SignalR service** (Azure SignalR Service bereitstellen):

1. Bei Bedarf können Sie die Standardeinstellung von **Abonnement** ändern.

2. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, geben Sie einen Namen für die neue Ressourcengruppe ein, und wählen Sie **OK** aus.

3. Wenn Sie eine neue Ressourcengruppe erstellt haben, wählen Sie eine **Region** für die Ressourcengruppe aus.

4. Sie können für die Azure SignalR Service-Instanz einen neuen **Namen** und **Standort** (z. B. **eastus2**) eingeben. Wenn Sie keinen Namen angeben, wird er automatisch generiert. Der Standort für die Azure SignalR Service-Instanz kann sich in derselben Region wie die Ressourcengruppe oder in einer anderen Region befinden. Wenn Sie keinen Standort angeben, wird dieselbe Region wie für die Ressourcengruppe verwendet.

5. Wählen Sie den **Tarif** (**Free_F1** oder **Standard_S1**) aus, geben Sie die **Kapazität** (Anzahl von SignalR-Einheiten) ein, und wählen Sie einen **Dienstmodus** aus: **Standard** (Hubserver erforderlich), **Serverlos** (keine Serververbindung zulässig) oder **Klassisch** (nur Weiterleitung an Hubserver, wenn der Hub über eine Serververbindung verfügt). Wählen Sie anschließend die Option **Enable Connectivity Logs** (Konnektivitätsprotokolle aktivieren) oder **Enable Messaging Logs** (Messagingprotokolle aktivieren) aus.

    > [!NOTE]
    > Für den Tarif **Free_F1** ist die Kapazität auf 1 Einheit beschränkt.

    :::image type="content" source="./media/signalr-quickstart-azure-signalr-service-arm-template/deploy-azure-signalr-service-arm-template-portal.png" alt-text="Screenshot: ARM-Vorlage zum Erstellen einer Azure SignalR Service-Instanz im Azure-Portal":::

6. Klicken Sie auf **Überprüfen + erstellen**.

7. Lesen Sie die Geschäftsbedingungen, und wählen Sie dann **Erstellen** aus.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

> [!NOTE]
> Wenn Sie die PowerShell-Skripts lokal ausführen möchten, geben Sie zunächst `Connect-AzAccount` ein, um Ihre Azure-Anmeldeinformationen einzurichten.

Verwenden Sie den folgenden Code, um die Azure SignalR Service-Instanz mithilfe der ARM-Vorlage bereitzustellen. Vom Code werden Sie zum Angeben der folgenden Informationen aufgefordert:

* Name und Region der neuen Azure SignalR Service-Instanz
* Name und Region einer neuen Ressourcengruppe
* Azure-Tarif (**Free_F1** oder **Standard_S1**)
* Kapazität der SignalR-Einheit (1, 2, 5, 10, 20, 50 oder 100)
  > [!NOTE]
  > Für den Tarif **Free_F1** ist die Kapazität auf 1 Einheit beschränkt.
* Dienstmodus: **Standard** (Hubserver erforderlich), **Serverlos** (keine Serververbindung zulässig) oder **Klassisch** (nur Weiterleitung an einen Hubserver, wenn der Hub über eine Serververbindung verfügt).
* Aktivierung/Deaktivierung der Protokolle für Konnektivität oder Messaging (**true** oder **false**)

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter a name for the new Azure SignalR Service"
$serviceLocation = Read-Host -Prompt "Enter an Azure region (for example, westus2) for the service"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group to contain the service"
$resourceGroupRegion = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"

$priceTier = Read-Host -Prompt "Enter the pricing tier (Free_F1 or Standard_S1)"
$unitCapacity = Read-Host -Prompt "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100)"
$servicingMode = Read-Host -Prompt "Enter the service mode (Default, Serverless, or Classic)"
$enableConnectionLogs = Read-Host -Prompt "Specify whether to enable connectivity logs (true or false)"
$enableMessageLogs = Read-Host -Prompt "Specify whether to enable messaging logs (true or false)"

Write-Verbose "New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion" -Verbose
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupRegion

$paramObjHashTable = @{
    name = $serviceName
    location = $serviceLocation
    pricingTier = $priceTier
    capacity = [int]$unitCapacity
    serviceMode = $servicingMode
    enableConnectivityLogs = $enableConnectionLogs
    enableMessagingLogs = $enableMessageLogs
}

Write-Verbose "Run New-AzResourceGroupDeployment to create an Azure SignalR Service using an ARM template" -Verbose
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateParameterObject $paramObjHashTable `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Verwenden Sie den folgenden Code, um die Azure SignalR Service-Instanz mithilfe der ARM-Vorlage bereitzustellen. Vom Code werden Sie zum Angeben der folgenden Informationen aufgefordert:

* Name und Region der neuen Azure SignalR Service-Instanz
* Name und Region einer neuen Ressourcengruppe
* Azure-Tarif (**Free_F1** oder **Standard_S1**)
* Kapazität der SignalR-Einheit (1, 2, 5, 10, 20, 50 oder 100)
    > [!NOTE]
    > Für den Tarif **Free_F1** ist die Kapazität auf 1 Einheit beschränkt.
* Dienstmodus: **Standard** (Hubserver erforderlich), **Serverlos** (keine Serververbindung zulässig) oder **Klassisch** (nur Weiterleitung an einen Hubserver, wenn der Hub über eine Serververbindung verfügt).
* Aktivierung/Deaktivierung der Protokolle für Konnektivität oder Messaging (**true** oder **false**)

```azurecli-interactive
read -p "Enter a name for the new Azure SignalR Service: " serviceName &&
read -p "Enter an Azure region (for example, westus2) for the service: " serviceLocation &&
read -p "Enter a name for the new resource group to contain the service: " resourceGroupName &&
read -p "Enter an Azure region (for example, centralus) for the resource group: " resourceGroupRegion &&
read -p "Enter the pricing tier (Free_F1 or Standard_S1): " priceTier &&
read -p "Enter the number of SignalR units (1, 2, 5, 10, 20, 50, or 100): " unitCapacity &&
read -p "Enter the service mode (Default, Serverless, or Classic): " servicingMode &&
read -p "Specify whether to enable connectivity logs (true or false): " enableConnectionLogs &&
read -p "Specify whether to enable messaging logs (true or false): " enableMessageLogs &&
params='name='$serviceName' location='$serviceLocation' pricingTier='$priceTier' capacity='$unitCapacity' serviceMode='$servicingMode' enableConnectivityLogs='$enableConnectionLogs' enableMessagingLogs='$enableMessageLogs &&
echo "CREATE RESOURCE GROUP:  az group create --name $resourceGroupName --location $resourceGroupRegion" &&
az group create --name $resourceGroupName --location $resourceGroupRegion &&
echo "RUN az deployment group create, which creates an Azure SignalR Service using an ARM template" &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-signalr/azuredeploy.json &&
read -p "Press [ENTER] to continue: "
```

---

> [!NOTE]
> Die Bereitstellung kann einige Minuten dauern. Notieren Sie sich die Namen für die Azure SignalR Service-Instanz und die Ressourcengruppe, die Sie später benötigen, um die bereitgestellten Ressourcen zu überprüfen.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine Übersicht über Ihre neue Azure SignalR Service-Instanz anzuzeigen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **SignalR**, und wählen Sie diese Option aus.

2. Wählen Sie in der SignalR-Liste Ihren neuen Dienst aus. Die Seite **Übersicht** für die neue Azure SignalR Service-Instanz wird angezeigt.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrer Azure SignalR Service-Instanz anzuzeigen. Sie müssen den Namen des neuen Diensts und die Ressourcengruppe eingeben.

```azurepowershell-interactive
$serviceName = Read-Host -Prompt "Enter the name of your Azure SignalR Service"
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Write-Verbose "Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName" -Verbose
Get-AzSignalR -ResourceGroupName $resourceGroupName -Name $serviceName
Read-Host "Press [ENTER] to continue"
```

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

Führen Sie den folgenden interaktiven Code aus, um Details zu Ihrer Azure SignalR Service-Instanz anzuzeigen. Sie müssen den Namen des neuen Diensts und die Ressourcengruppe eingeben.

```azurecli-interactive
read -p "Enter the name of your Azure SignalR Service: " serviceName &&
read -p "Enter the resource group name: " resourceGroupName &&
echo "SHOW SERVICE DETAILS:  az signalr show --resource-group $resourceGroupName --name $serviceName" &&
az signalr show --resource-group $resourceGroupName --name $serviceName &&
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

## <a name="next-steps"></a>Nächste Schritte

Ein Tutorial mit Schritt-für-Schritt-Anleitungen zum Erstellen einer ARM-Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
