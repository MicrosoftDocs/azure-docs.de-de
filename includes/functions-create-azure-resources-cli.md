---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 29a31948c5bfc9b5fd3a31f39144d186dbfe4885
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027376"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>Erstellen von unterstützenden Azure-Ressourcen für Ihre Funktion

Zum Bereitstellen Ihres Funktionscodes in Azure müssen Sie drei Ressourcen erstellen:

- Eine Ressourcengruppe als logischen Container für verwandte Ressourcen.
- Ein Storage-Konto, unter dem Status- und andere Informationen zu Ihren Projekten verwaltet werden.
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
