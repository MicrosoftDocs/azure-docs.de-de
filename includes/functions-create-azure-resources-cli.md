---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 99ae35aca485ac928f7c5ef9f98295eed4bc1245
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500063"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen:

- Eine [Ressourcengruppe](../articles/azure-resource-manager/management/overview.md), bei der es sich um einen logischen Container für verwandte Ressourcen handelt.
- Ein [Speicherkonto](../articles/storage/common/storage-account-create.md), das verwendet wird, um den Status und andere Informationen zu Ihren Funktionen zu verwalten.
- Eine Funktions-App, die als Umgebung zum Ausführen Ihres Funktionscodes dient. Eine Funktions-App ist Ihrem lokalen Funktionsprojekt zugeordnet und ermöglicht Ihnen das Gruppieren von Funktionen als logische Einheit, um die Verwaltung, Bereitstellung und Freigabe von Ressourcen zu vereinfachen.

Verwenden Sie die folgenden Befehle, um diese Elemente zu erstellen. Sowohl die Azure CLI als auch PowerShell werden unterstützt.

1. Melden Sie sich bei Azure an, falls dies noch nicht geschehen ist:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Mit dem Befehl [az login](/cli/azure/reference-index#az_login) werden Sie bei Ihrem Azure-Konto angemeldet.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldet Sie bei Ihrem Konto an.

    ---

1. Erstellen Sie eine Ressourcengruppe mit dem Namen `AzureFunctionsQuickstart-rg` in der Region `westeurope`:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Mit dem Befehl [az group create](/cli/azure/group#az_group_create) wird eine Ressourcengruppe erstellt. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Befehl `az account list-locations` zurückgegeben wird.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Der Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) erstellt eine Ressourcengruppe. Im Allgemeinen erstellen Sie Ihre Ressourcengruppe und die Ressourcen in einer Region in Ihrer Nähe, indem Sie eine verfügbare Region verwenden, die vom Cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation) zurückgegeben wird.

    ---

1. Erstellen Sie in Ihrer Ressourcengruppe und Region ein universelles Speicherkonto:

    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Der Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) erstellt ein Speicherkonto. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) erstellt das Speicherkonto.

    ---

    Ersetzen Sie im vorherigen Beispiel `<STORAGE_NAME>` durch einen Namen, der für Sie geeignet und eindeutig in Azure Storage ist. Namen dürfen nur 3 bis 24 Zeichen und ausschließlich Kleinbuchstaben enthalten. Mit `Standard_LRS` wird ein universelles Konto angegeben, das [von Functions unterstützt](../articles/azure-functions/storage-considerations.md#storage-account-requirements) wird.
