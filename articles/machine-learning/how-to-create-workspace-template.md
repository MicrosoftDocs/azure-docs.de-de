---
title: Erstellen eines Arbeitsbereichs anhand einer Azure Resource Manager-Vorlage
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie eine Azure Resource Manager-Vorlage verwenden, um einen neuen Azure Machine Learning-Arbeitsbereich zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli, devx-track-azurepowershell
ms.author: larryfr
author: Blackmist
ms.date: 09/30/2020
ms.openlocfilehash: bd9199bc73e56ec36343b30d9b24f0b48799835e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445176"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning


<br>

In diesem Artikel erlernen Sie verschiedene Möglichkeiten zum Erstellen eines Azure Machine Learning-Arbeitsbereichs mithilfe von Azure Resource Manager-Vorlagen. Eine Resource Manager-Vorlage erleichtert das Erstellen von Ressourcen in einem einzelnen, koordinierten Vorgang. Eine Vorlage ist ein JSON-Dokument, das die Ressourcen definiert, die für eine Bereitstellung erforderlich sind. Es kann außerdem bestimmte Bereitstellungsparameter angeben. Parameter werden verwendet, um Eingabewerte bereitzustellen, wenn die Vorlage verwendet wird.

Weitere Informationen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Resource Manager-Vorlage](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

* Um eine Vorlage über eine Befehlszeilenschnittstelle zu verwenden, benötigen Sie entweder [Azure PowerShell](/powershell/azure/?view=azps-1.2.0) oder die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

* Für einige Szenarien müssen Sie ein Supportticket öffnen. Es handelt sich um folgende Szenarien:

    * __Arbeitsbereich mit aktiviertem Private Link mit einem kundenseitig verwalteten Schlüssel__
    * __Azure Container Registry für den Arbeitsbereich hinter Ihrem virtuellen Netzwerk__

    Weitere Informationen finden Sie unter [Verwalten und Erhöhen von Kontingenten](how-to-manage-quotas.md#private-endpoint-and-private-dns-quota-increases).

## <a name="limitations"></a>Einschränkungen

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="workspace-resource-manager-template"></a>Verwenden von Arbeitsbereich-Resource Manager-Vorlagen

Die in diesem Dokument verwendete Azure Resource Manager-Vorlage finden Sie im GitHub-Repository für Azure-Schnellstartvorlagen im [201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json)-Verzeichnis.

Diese Vorlage erstellt die folgenden Azure-Dienste:

* Azure Storage-Konto
* Azure-Schlüsseltresor
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-Arbeitsbereich

Die Ressourcengruppe ist der Container, der die Dienste enthält. Die verschiedenen Dienste sind für den Azure Machine Learning-Arbeitsbereich erforderlich.

Die Beispielvorlage verfügt über zwei **erforderliche** Parameter:

* Parameter **location** für den Ort, an dem die Ressourcen erstellt werden.

    Die Vorlage verwendet den von Ihnen ausgewählten Ort für die meisten Ressourcen. Die Ausnahme ist hierbei der Application Insights-Dienst, der nicht an allen Orten verfügbar ist, an denen die anderen Dienste verfügbar sind. Wenn Sie einen Ort auswählen, an dem er nicht verfügbar ist, wird der Dienst am Ort „USA, Süden-Mitte“ erstellt.

* Parameter **workspaceName**, den Anzeigenamen des Azure Machine Learning-Arbeitsbereichs.

    > [!NOTE]
    > Für den Namen des Arbeitsbereichs wird die Groß-/Kleinschreibung nicht beachtet.

    Die Namen der anderen Dienste werden nach dem Zufallsprinzip generiert.

> [!TIP]
> Während die Vorlage, die mit diesem Dokument verknüpft ist, eine neue Azure-Containerregistrierung erstellt, können Sie auch einen neuen Arbeitsbereich erstellen, ohne eine Containerregistrierung zu erstellen. Eine Containerregistrierung wird erstellt, wenn Sie einen Vorgang ausführen, der eine Containerregistrierung erfordert. Beispielsweise Trainieren oder Bereitstellen eines Modells.
>
> Sie können auch auf eine vorhandene Containerregistrierung oder ein Speicherkonto in der Azure Resource Manager-Vorlage verweisen, anstatt eine neue Registrierung zu erstellen. Für die Containerregistrierung, die Sie verwenden, muss jedoch das __Administratorkonto__ aktiviert sein. Informationen zum Aktivieren des Administratorkontos finden Sie unter [Administratorkonto](../container-registry/container-registry-authentication.md#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Weitere Informationen zu Vorlagen finden Sie in den folgenden Artikeln:

* [Erstellen von Azure Resource-Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)
* [Bereitstellen einer Anwendung mit Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/deploy-powershell.md)
* [Microsoft.MachineLearningServices-Ressourcentypen](/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Bereitstellen der Vorlage

Zum Bereitstellen der Vorlage müssen Sie eine Ressourcengruppe erstellen.

Weitere Informationen finden Sie im Abschnitt [Azure-Portal](#use-the-azure-portal), wenn Sie die grafische Benutzeroberfläche verwenden möchten.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Nachdem die Ressourcengruppe erfolgreich erstellt wurde, stellen Sie die Vorlage mit dem folgenden Befehl bereit:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Standardmäßig sind alle als Teil der Vorlage erstellten Ressourcen neu. Sie haben jedoch auch die Möglichkeit, vorhandene Ressourcen zu verwenden. Durch die Bereitstellung zusätzlicher Parameter für die Vorlage können Sie vorhandene Ressourcen verwenden. Wenn Sie z. B. ein vorhandenes Speicherkonto verwenden möchten, legen Sie den **storageAccountOption**-Wert auf **existing** fest, und geben Sie den Namen Ihres Speicherkontos im Parameter **storageAccountName** an.

> [!IMPORTANT]
> Wenn Sie ein vorhandenes Azure Storage-Konto verwenden möchten, darf es sich nicht um ein Premium-Konto (Premium_LRS oder Premium_GRS) handeln. Es darf auch keinen hierarchischen Namespace aufweisen (mit Azure Data Lake Storage Gen2 verwendet). Weder Storage Premium noch hierarchische Namespaces werden mit dem Standardspeicherkonto des Arbeitsbereichs unterstützt. Weder Storage Premium noch hierarchische Namespaces werden mit dem _Standardspeicherkonto_ des Arbeitsbereichs unterstützt. Sie können Storage Premium noch hierarchische Namespaces mit _nicht standardmäßigen_ Speicherkonten verwenden.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Bereitstellen eines verschlüsselten Arbeitsbereichs

Die folgende Beispielvorlage zeigt, wie Sie einen Arbeitsbereich mit drei Einstellungen erstellen:

* Es werden Einstellungen für hohe Vertraulichkeit für den Arbeitsbereich aktiviert. Dadurch wird eine neue Cosmos DB-Instanz erstellt.
* Die Verschlüsselung wird für den Arbeitsbereich aktiviert.
* Es wird eine vorhandene Azure Key Vault-Instanz verwendet, um kundenseitig verwaltete Schlüssel abzurufen. Kundenseitig verwaltete Schlüssel werden zum Erstellen einer neuen Cosmos DB-Instanz für den Arbeitsbereich verwendet.

    [!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

> [!IMPORTANT]
> Nachdem ein Arbeitsbereich erstellt wurde, können Sie die Einstellungen für vertrauliche Daten, Verschlüsselung, Key Vault-ID oder Schlüsselbezeichner nicht mehr ändern. Um diese Werte zu ändern, müssen Sie einen neuen Arbeitsbereich erstellen und dabei neue Werte verwenden.

Weitere Informationen finden Sie unter [Verschlüsselung ruhender Daten](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]
> Es gibt einige bestimmte Anforderungen, die Ihr Abonnement erfüllen muss, bevor Sie diese Vorlage verwenden können:
> * Sie müssen über einen vorhandenen Azure Key Vault verfügen, der einen Verschlüsselungsschlüssel enthält.
> * Der Azure Key Vault muss sich in derselben Region befinden, in der Sie den Azure Machine Learning-Arbeitsbereich erstellen möchten.
> * Sie müssen die ID der Azure Key Vault-Instanz und den URI des Verschlüsselungsschlüssels angeben.

__Zum Abrufen der Werte__ für die Parameter `cmk_keyvault` (Key Vault-ID) und `resource_cmk_uri` (Schlüssel-URI), die für diese Vorlage erforderlich sind, verwenden Sie die folgenden Schritte:    

1. Verwenden Sie den folgenden Befehl, um die Key Vault-ID abzurufen:  

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)   

    ```azurecli 
    az keyvault show --name <keyvault-name> --query 'id' --output tsv   
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'    
    ``` 
    --- 

    Der Befehl gibt einen Wert zurück, der `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` ähnelt.  

1. Um den Wert für den URI für den kundenseitig verwalteten Schlüssel abzurufen, verwenden Sie folgenden Befehl:    

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)   

    ```azurecli 
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv  
    ``` 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell) 

    ```azurepowershell  
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>' 
    ``` 
    --- 

    Der Befehl gibt einen Wert zurück, der `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` ähnelt. 

> [!IMPORTANT]  
> Nachdem ein Arbeitsbereich erstellt wurde, können Sie die Einstellungen für vertrauliche Daten, Verschlüsselung, Key Vault-ID oder Schlüsselbezeichner nicht mehr ändern. Um diese Werte zu ändern, müssen Sie einen neuen Arbeitsbereich erstellen und dabei neue Werte verwenden.

Legen Sie die folgenden Parameter fest, um die Verwendung kundenseitig verwalteter Schlüssel beim Bereitstellen der Vorlage zu aktivieren:

* **encryption_status** auf **Aktiviert**.
* **cmk_keyvault** auf den `cmk_keyvault`-Wert, den Sie in den vorherigen Schritten erhalten haben.
* **resource_cmk_uri** auf den `resource_cmk_uri`-Wert, den Sie in den vorherigen Schritten erhalten haben.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

Wenn ein vom Kunden verwalteter Schlüssel verwendet wird, erstellt Azure Machine Learning eine sekundäre Ressourcengruppe, die die Cosmos DB-Instanz enthält. Weitere Informationen finden Sie unter [Verschlüsselung ruhender Daten](concept-data-encryption.md#encryption-at-rest).

Als zusätzliche Konfiguration für Ihre Daten können Sie den **confidential_data**-Parameter auf **true** festlegen. Daraus resultiert Folgendes:

* Die Verschlüsselung des lokalen Scratch-Datenträgers in Ihren Azure Machine Learning-Computeclustern wird gestartet, sofern Sie in Ihrem Abonnement keine vorherigen Cluster erstellt haben. Wenn Sie zuvor einen Cluster im Abonnement erstellt haben, öffnen Sie ein Supportticket, um die Verschlüsselung des für Ihre Computecluster aktivierten Scratch-Datenträgers zu aktivieren.
* Ihre lokalen Scratch-Datenträger werden zwischen den Ausführungen bereinigt.
* Unter Verwendung Ihres Schlüsseltresors werden Anmeldeinformationen für Speicherkonto, Containerregistrierung und SSH-Konto von der Ausführungsebene an Ihre Computecluster übergeben.
* Die IP-Filterung wird aktiviert, um sicherzustellen, dass die zugrunde liegenden Batch-Pools nicht von anderen externen Diensten als Azure Machine Learning Service aufgerufen werden können.

    > [!IMPORTANT]
    > Nachdem ein Arbeitsbereich erstellt wurde, können Sie die Einstellungen für vertrauliche Daten, Verschlüsselung, Key Vault-ID oder Schlüsselbezeichner nicht mehr ändern. Um diese Werte zu ändern, müssen Sie einen neuen Arbeitsbereich erstellen und dabei neue Werte verwenden.

  Weitere Informationen finden Sie unter [Verschlüsselung ruhender Daten](concept-data-encryption.md#encryption-at-rest).

## <a name="deploy-workspace-behind-a-virtual-network"></a>Bereitstellen des Arbeitsbereichs hinter einem virtuellen Netzwerk

Wenn Sie den `vnetOption`-Parameterwert entweder auf `new` oder `existing`festlegen, können Sie die Ressourcen erstellen, die von einem Arbeitsbereich hinter einem virtuellen Netzwerk verwendet wird.

> [!IMPORTANT]
> Bei der Containerregistrierung wird nur die SKU „Premium“ unterstützt.

> [!IMPORTANT]
> Application Insights unterstützt keine Bereitstellung hinter einem virtuellen Netzwerk.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Bereitstellen des Arbeitsbereichs nur hinter einem privatem Endpunkt

Wenn sich ihre zugeordneten Ressourcen nicht hinter einem virtuellen Netzwerk befinden, können Sie den Parameter **privateEndpointType** auf `AutoAproval` oder `ManualApproval` festlegen, um den Arbeitsbereich hinter einem privaten Endpunkt bereitzustellen. Dies kann für neue und vorhandene Arbeitsbereiche erfolgen. Wenn Sie einen vorhandenen Arbeitsbereich aktualisieren, geben Sie die Vorlagenparameter anhand der Informationen aus dem vorhandenen Arbeitsbereich ein.

> [!IMPORTANT]
> Die Verwendung eines Azure Machine Learning-Arbeitsbereichs mit Private Link ist in den Azure Government- und Azure China 21Vianet-Regionen nicht verfügbar.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Verwenden eines neuen virtuellen Netzwerks

Legen Sie zum Bereitstellen einer Ressource hinter einem neuen virtuellen Netzwerk die **vnetOption** zusammen mit den Einstellungen des virtuellen Netzwerks für die jeweilige Ressource auf **neu** fest. Die folgende Bereitstellung zeigt, wie Sie einen Arbeitsbereich mit der Speicherkontoressource hinter einem neuen virtuellen Netzwerk bereitstellen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Alternativ können Sie mehrere oder alle abhängigen Ressourcen hinter einem virtuellen Netzwerk bereitstellen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Verwenden eines vorhandenen virtuellen Netzwerks und vorhandener Ressourcen

Wenn Sie einen Arbeitsbereich mit vorhandenen zugeordneten Ressourcen bereitstellen möchten, müssen Sie den **vnetOption**-Parameter zusammen mit Subnetzparametern auf **vorhanden** festlegen. Sie müssen jedoch im virtuellen Netzwerk für alle Ressourcen **vor** der Bereitstellung Dienstendpunkte erstellen. Wie bei Bereitstellungen neuer virtueller Netzwerke können sich eine oder alle Ihre Ressourcen hinter einem virtuellen Netzwerk befinden.

> [!IMPORTANT]
> Das Subnetz sollte den `Microsoft.Storage`-Dienstendpunkt aufweisen

> [!IMPORTANT]
> Subnetze erlauben keine Erstellung privater Endpunkte. Deaktivieren Sie den privaten Endpunkt, um das Subnetz zu aktivieren.

1. Aktivieren Sie Dienstendpunkte für die Ressourcen.

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Bereitstellen des Arbeitsbereichs

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Befolgen Sie die Schritte in [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template). Wählen Sie auf dem Bildschirm __Vorlage auswählen__ in der Dropdownliste die Vorlage **201-machine-learning-advanced** aus.
1. Wählen Sie __Vorlage auswählen__, um die Vorlage zu verwenden. Geben Sie abhängig von Ihrem Bereitstellungsszenario die folgenden erforderlichen Informationen und sonstige Parameter an.

   * Abonnement: Wählen Sie aus, welches Azure-Abonnement für diese Ressourcen verwendet werden soll.
   * Ressourcengruppe: Wählen Sie eine Ressourcengruppe für die Aufnahme der Dienste aus, oder erstellen Sie eine.
   * Region: Wählen Sie die Azure-Region aus, in der die Ressourcen erstellt werden.
   * Arbeitsbereichsname: Der für den Azure Machine Learning-Arbeitsbereich, der erstellt wird, zu verwendende Name. Der Arbeitsbereichsname muss zwischen 3 und 33 Zeichen umfassen. Er darf nur alphanumerische Zeichen und Bindestriche („-“) enthalten.
   * Standort: Wählen Sie den Ort aus, an dem die Ressourcen erstellt werden.
1. Klicken Sie auf __Überprüfen + erstellen__.
1. Stimmen Sie auf dem Bildschirm __Überprüfen + erstellen__ den aufgelisteten Geschäftsbedingungen zu, und wählen Sie __Erstellen__ aus.

Weitere Informationen finden Sie unter [Bereitstellen von Ressourcen mithilfe einer benutzerdefinierten Vorlage](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="resource-provider-errors"></a>Fehler der Ressourcenanbieter

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault-Zugriffsrichtlinie und Azure Resource Manager-Vorlagen

Wenn Sie eine Azure Resource Manager-Vorlage mehrmals zum Erstellen des Arbeitsbereichs und der zugehörigen Ressourcen (einschließlich Azure Key Vault) verwenden. Dies ist beispielsweise der Fall, wenn Sie die Vorlage mehrere Male mit den gleichen Parametern in einer Continuous Integration- und Deployment-Pipeline verwenden.

Die meisten Vorgänge zum Erstellen von Ressourcen mit Vorlagen sind idempotent, Key Vault löscht die Zugriffsrichtlinien jedoch jedes Mal, wenn die Vorlage verwendet wird. Das Löschen der Zugriffsrichtlinien unterbricht den Zugriff auf den Schlüsseltresor für vorhandene Arbeitsbereiche, die ihn verwenden. Es kann beispielsweise sein, dass für die Beenden/Erstellen-Funktionalität der Azure Notebooks-VM ein Fehler auftritt.  

Folgende Ansätze werden empfohlen, um dieses Problem zu umgehen:

* Stellen Sie die Vorlage nicht mehrmals mit den gleichen Parametern bereit, oder löschen Sie die vorhandenen Ressourcen, bevor Sie die Vorlage verwenden, um sie neu zu erstellen.

* Untersuchen Sie die Key Vault-Zugriffsrichtlinien, und verwenden Sie sie dann zum Festlegen der `accessPolicies`-Eigenschaft der Vorlage. Zum Anzeigen der Zugriffsrichtlinien verwenden Sie den folgenden Azure CLI-Befehl:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Weitere Informationen zur Verwendung des `accessPolicies`-Abschnitts der Vorlage finden Sie im [AccessPolicyEntry-Objektverweis](/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Überprüfen Sie, ob die Key Vault-Ressource bereits vorhanden ist. Wenn dies der Fall ist, erstellen Sie sie nicht mithilfe der Vorlage neu. Wenn Sie z. B. den vorhandenen Key Vault verwenden möchten, anstatt einen neuen zu erstellen, nehmen Sie die folgenden Änderungen an der Vorlage vor:

    * **Fügen Sie einen Parameter hinzu**, der die ID einer vorhandenen Key Vault-Ressource annimmt:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Entfernen Sie** den Abschnitt, der eine Key Vault-Ressource erstellt:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Entfernen Sie** die Zeile `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` aus dem Abschnitt `dependsOn` des Arbeitsbereichs. **Ändern** Sie zudem ein Eintrag `keyVault` im Abschnitt `properties` des Arbeitsbereichs, um auf den Parameter `keyVaultId` zu verweisen:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Nachdem Sie diese Änderungen vorgenommen haben, können Sie beim Ausführen der Vorlage die ID der vorhandenen Key Vault-Ressource angeben. Die Vorlage verwendet den Schlüsseltresor anschließend erneut, indem die `keyVault`-Eigenschaft des Arbeitsbereichs auf die entsprechende ID festlegt wird.

    Um die ID des Key Vaults abzurufen, können Sie auf die Ausgabe der ursprünglichen Vorlagenausführung verweisen oder die Azure CLI verwenden. Der folgende Befehl ist ein Beispiel für die Verwendung der Azure CLI zum Abrufen der ID der Key Vault Ressource:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

### <a name="virtual-network-not-linked-to-private-dns-zone"></a>Das virtuelle Netzwerk ist nicht mit einer privaten DNS-Zone verknüpft.

Beim Erstellen eines Arbeitsbereichs mit einem privaten Endpunkt erstellt die Vorlage eine private DNS-Zone mit dem Namen __privatelink.api.azureml.ms__. Dieser privaten DNS-Zone wird automatisch eine __VNET-Verknüpfung__ hinzugefügt. Die Verknüpfung wird nur für den ersten Arbeitsbereich und den privaten Endpunkt hinzugefügt, die Sie in einer Ressourcengruppe erstellen. Wenn Sie ein weiteres virtuelles Netzwerk und einen weiteren Arbeitsbereich mit einem privaten Endpunkt in derselben Ressourcengruppe erstellen, wird das zweite virtuelle Netzwerk unter Umständen nicht zur privaten DNS-Zone hinzugefügt.

Verwenden Sie den folgenden Azure CLI-Befehl, um die VNET-Verknüpfungen anzuzeigen, die für die private DNS-Zone bereits vorhanden sind:

```azurecli
az network private-dns link vnet list --zone-name privatelink.api.azureml.ms --resource-group myresourcegroup
```

Führen Sie die folgenden Schritte aus, um das virtuelle Netzwerk hinzuzufügen, das einen anderen Arbeitsbereich und privaten Endpunkt enthält:

1. Verwenden Sie den folgenden Befehl, um die ID des virtuellen Netzwerks für das Netzwerk zu ermitteln, das Sie hinzufügen möchten:

    ```azurecli
    az network vnet show --name myvnet --resource-group myresourcegroup --query id
    ```
    
    Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht: /subscriptions/GUID/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet. Speichern Sie diesen Wert, und verwenden Sie ihn im nächsten Schritt.

2. Verwenden Sie den folgenden Befehl, um der privaten DNS-Zone „privatelink.api.azureml.ms“ eine VNET-Verknüpfung hinzuzufügen. Verwenden Sie für den Parameter`--virtual-network` die Ausgabe des vorherigen Befehls:

    ```azurecli
    az network private-dns link vnet create --name mylinkname --registration-enabled true --resource-group myresourcegroup --virtual-network myvirtualnetworkid --zone-name privatelink.api.azureml.ms
    ```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/templates/deploy-rest.md).
* [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
* [Weitere Vorlagen für Azure Machine Learning finden Sie im Repository mit Azure-Schnellstartvorlagen.](https://github.com/Azure/azure-quickstart-templates)