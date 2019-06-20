---
title: 'Erstellen eines Speicherkontos: Azure Storage'
description: In diesem Artikel erfahren Sie, wie Sie über das Azure-Portal, Azure PowerShell oder die Azure-Befehlszeilenschnittstelle ein Speicherkonto erstellen. Ein Azure-Speicherkonto stellt in Microsoft Azure einen eindeutigen Namespace zum Speichern der Datenobjekte, die Sie in Azure Storage erstellen, sowie zum Zugreifen auf diese Objekte bereit.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65234174"
---
# <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ein Azure Storage-Konto enthält all Ihre Azure Storage-Datenobjekte: Blobs, Dateien, Warteschlangen, Tabellen und Datenträger. Das Speicherkonto stellt einen eindeutigen Namespace für Ihre Azure Storage-Daten bereit, auf den von jedem Ort der Welt aus über HTTP oder HTTPS zugegriffen werden kann. Daten in Ihrem Azure Storage-Konto sind dauerhaft und hochverfügbar, sicher und extrem skalierbar.

In diesem Artikel erfahren Sie, wie Sie über das [Azure-Portal](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) oder eine [Azure Resource Manager-Vorlage](../../azure-resource-manager/resource-group-overview.md) ein Speicherkonto erstellen.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

None (Keine):

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Für diesen Artikel ist mindestens Version 0.7 des Az-Moduls für Azure PowerShell erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um Ihre aktuelle Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können sich bei Azure anmelden und Azure-CLI-Befehle ausführen. Dazu haben Sie zwei Möglichkeiten:

- Sie können CLI-Befehle innerhalb des Azure-Portals in Azure Cloud Shell ausführen.
- Sie können die Befehlszeilenschnittstelle installieren und CLI-Befehle lokal ausführen.

### <a name="use-azure-cloud-shell"></a>Verwenden von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure-Befehlszeilenschnittstelle ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie im Azure-Portal im Menü im rechten oberen Bereich auf die Schaltfläche **Cloud Shell**:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Die Schaltfläche öffnet eine interaktive Shell, mit der Sie die in diesem Artikel beschriebenen Schritte ausführen können:

[![Screenshot des Fensters „Cloud Shell“ im Portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Lokales Installieren der Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle auch lokal installieren und verwenden. Für diesen Artikel müssen Sie mindestens Version 2.0.4 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Vorlage](#tab/template)

None (Keine):

---

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az-login) aus:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Vorlage](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Sie können nun Ihr Speicherkonto erstellen.

Jedes Speicherkonto muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. In diesem Artikel wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

Ein **universelles v2**-Speicherkonto bietet Zugriff auf sämtliche Azure Storage-Dienste: Blobs, Dateien, Warteschlangen, Tabellen und Datenträger. In den hier beschriebenen Schritten wird ein Speicherkonto vom Typ „Universell v2“ erstellt. Die Schritte für die Erstellung einer anderen Art von Speicherkonto sind jedoch ähnlich.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie zunächst mit PowerShell unter Verwendung des Befehls [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue Ressourcengruppe:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Falls Sie nicht wissen, welche Region Sie für den `-Location`-Parameter angeben sollen, können Sie mit dem Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2“ mit georedundantem Speicher mit Lesezugriff (RA-GRS) über den Befehl [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Denken Sie daran, dass der Name Ihres Speicherkontos innerhalb von Azure eindeutig sein muss, ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Um ein Speicherkonto vom Typ „Universell v2“ mit einer anderen Replikationsoption zu erstellen, setzen Sie für den Parameter **SkuName** den gewünschten Wert in der folgenden Tabelle ein.

|Replikationsoption  |SkuName-Parameter  |
|---------|---------|
|Lokal redundanter Speicher (LRS)     |Standard_LRS         |
|Zonenredundanter Speicher (ZRS)     |Standard_ZRS         |
|Georedundanter Speicher (GRS)     |Standard_GRS         |
|Georedundanter Speicher mit Lesezugriff (RA-GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Erstellen Sie zuerst eine neue Ressourcengruppe über die Azure-Befehlszeilenschnittstelle, und verwenden Sie hierzu den Befehl [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Falls Sie nicht wissen, welche Region Sie für den `--location`-Parameter angeben sollen, können Sie mit dem Befehl [az account list-locations](/cli/azure/account#az_account_list) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2“ mit georedundantem Speicher mit Lesezugriff über den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create). Denken Sie daran, dass der Name Ihres Speicherkontos innerhalb von Azure eindeutig sein muss, ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Um ein Speicherkonto vom Typ „Universell v2“ mit einer anderen Replikationsoption zu erstellen, setzen Sie für den Parameter **sku** den gewünschten Wert in der folgenden Tabelle ein.

|Replikationsoption  |sku-Parameter  |
|---------|---------|
|Lokal redundanter Speicher (LRS)     |Standard_LRS         |
|Zonenredundanter Speicher (ZRS)     |Standard_ZRS         |
|Georedundanter Speicher (GRS)     |Standard_GRS         |
|Georedundanter Speicher mit Lesezugriff (RA-GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Vorlage](#tab/template)

Sie können entweder Azure PowerShell oder die Azure CLI verwenden, um eine Resource Manager-Vorlage zum Erstellen eines Speicherkontos bereitzustellen. Die in diesem Artikel verwendete Resource Manager-Vorlage stammt von der Seite mit den [Azure Resource Manager-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Wählen Sie zum Ausführen der Skripts **Testen Sie es.** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Informationen zum Erstellen von Vorlagen finden Sie hier:

- [Dokumentation zu Azure Resource Manager](/azure/azure-resource-manager/)
- [Vorlagenreferenz für Speicherkonten](/azure/templates/microsoft.storage/allversions)
- [Weitere Beispiele für Speicherkontovorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

Weitere Informationen zu verfügbaren Replikationsoptionen finden Sie unter [Azure Storage-Replikation](storage-redundancy.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Ressourcen zu bereinigen, die im Rahmen dieses Artikels erstellt wurden, können Sie die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden sowohl das zugeordnete Speicherkonto als auch alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

So entfernen Sie eine Ressourcengruppe über das Azure-Portal:

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf **Ressourcengruppen**, um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die zu löschende Ressourcengruppe, und klicken Sie mit der rechten Maustaste rechts neben dem Eintrag auf die Schaltfläche **Mehr** ( **...** ).
3. Klicken Sie auf **Ressourcengruppe löschen**, und bestätigen Sie den Vorgang.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az group delete](/cli/azure/group#az_group_delete), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen.

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Vorlage](#tab/template)

Verwenden Sie Azure PowerShell oder die Azure CLI, um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein Speicherkonto vom Typ „Universell v2“ erstellt. Weitere Informationen zum Hoch- und Herunterladen von Blobs in Ihr Speicherkonto bzw. aus diesem finden Sie in den Schnellstarts für Blob Storage.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Arbeiten mit Blobs unter Verwendung des Azure-Portals](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Arbeiten mit Blobs unter Verwendung der PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Arbeiten mit Blobs unter Verwendung der Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Vorlage](#tab/template)

> [!div class="nextstepaction"]
> [Arbeiten mit Blobs unter Verwendung des Azure-Portals](../blobs/storage-quickstart-blobs-portal.md)

---
