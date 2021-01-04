---
title: Speicherkonto erstellen
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie ein Speicherkonto zum Speichern von Blobs, Dateien, Warteschlangen und Tabellen erstellen. Ein Azure Storage-Konto stellt einen eindeutigen Namespace in Microsoft Azure zum Lesen und Schreiben Ihre Daten bereit.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c8d07e53e259fff28e662f0c1ca3934eabf015e8
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357448"
---
# <a name="create-a-storage-account"></a>Speicherkonto erstellen

Ein Azure Storage-Konto enthält all Ihre Azure Storage-Datenobjekte: Blobs, Dateien, Warteschlangen, Tabellen und Datenträger. Das Speicherkonto stellt einen eindeutigen Namespace für Ihre Azure Storage-Daten bereit, auf den von jedem Ort der Welt aus über HTTP oder HTTPS zugegriffen werden kann. Daten in Ihrem Azure Storage-Konto sind dauerhaft und hochverfügbar, sicher und extrem skalierbar.

In diesem Artikel erfahren Sie, wie Sie über das [Azure-Portal](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), die [Azure-Befehlszeilenschnittstelle](/cli/azure) oder eine [Azure Resource Manager-Vorlage](../../azure-resource-manager/management/overview.md) ein Speicherkonto erstellen.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Keine.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie ein Azure Storage-Konto mit PowerShell erstellen möchten, stellen Sie sicher, dass Sie das Azure PowerShell-Modul Az 0.7 oder höher installiert haben. Weitere Informationen finden Sie unter [Einführung in das neue Azure PowerShell Az-Modul](/powershell/azure/new-azureps-module-az).

Führen Sie den folgenden Befehl aus, um Ihre aktuelle Version zu ermitteln:

```powershell
Get-InstalledModule -Name "Az"
```

Informationen zur Installation oder zum Upgrade von Azure PowerShell finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Sie können sich bei Azure anmelden und Azure-CLI-Befehle ausführen. Dazu haben Sie zwei Möglichkeiten:

- Sie können CLI-Befehle innerhalb des Azure-Portals in Azure Cloud Shell ausführen.
- Sie können die Befehlszeilenschnittstelle installieren und CLI-Befehle lokal ausführen.

### <a name="use-azure-cloud-shell"></a>Verwenden von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose Bash-Shell, die Sie direkt im Azure-Portal ausführen können. Die Azure-Befehlszeilenschnittstelle ist vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Klicken Sie im Azure-Portal im Menü im rechten oberen Bereich auf die Schaltfläche **Cloud Shell**:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Die Schaltfläche öffnet eine interaktive Shell, mit der Sie die in diesem Artikel beschriebenen Schritte ausführen können:

[![Screenshot des Fensters „Cloud Shell“ im Portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Lokales Installieren der Befehlszeilenschnittstelle

Sie können die Azure-Befehlszeilenschnittstelle auch lokal installieren und verwenden. Die Beispiele in diesem Artikel erfordern Version 2.0.4 oder höher der Azure-Befehlszeilenschnittstelle. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

# <a name="template"></a>[Vorlage](#tab/template)

Keine.

---

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den Befehl [az login](/cli/azure/reference-index#az-login) aus:

```azurecli-interactive
az login
```

# <a name="template"></a>[Vorlage](#tab/template)

–

---

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Jedes Speicherkonto muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. In diesem Artikel wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

Ein **universelles v2**-Speicherkonto bietet Zugriff auf sämtliche Azure Storage-Dienste: Blobs, Dateien, Warteschlangen, Tabellen und Datenträger. In den hier beschriebenen Schritten wird ein Speicherkonto vom Typ „Universell v2“ erstellt. Die Schritte für die Erstellung einer anderen Art von Speicherkonto sind jedoch ähnlich. Weitere Informationen zu den Arten von Speicherkonten und anderen Speicherkontoeinstellungen finden Sie unter [Übersicht über Azure Storage-Konten](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Erstellen Sie zunächst mit PowerShell unter Verwendung des Befehls [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue Ressourcengruppe:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Falls Sie nicht wissen, welche Region Sie für den `-Location`-Parameter angeben sollen, können Sie mit dem Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen:

```powershell
Get-AzLocation | select Location
```

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2“ mit georedundantem Speicher mit Lesezugriff (RA-GRS) über den Befehl [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Denken Sie daran, dass der Name Ihres Speicherkontos innerhalb von Azure eindeutig sein muss, ersetzen Sie daher den Platzhalterwert in Klammern durch Ihren eigenen eindeutigen Wert:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Wenn Sie [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) verwenden möchten, schließen Sie `-EnableHierarchicalNamespace $True` in diese Parameterliste ein.

Um ein Speicherkonto vom Typ „Universell v2“ mit einer anderen Replikationsoption zu erstellen, setzen Sie für den Parameter **SkuName** den gewünschten Wert in der folgenden Tabelle ein.

|Replikationsoption  |SkuName-Parameter  |
|---------|---------|
|Lokal redundanter Speicher (LRS)     |Standard_LRS         |
|Zonenredundanter Speicher (ZRS)     |Standard_ZRS         |
|Georedundanter Speicher (GRS)     |Standard_GRS         |
|Georedundanter Speicher mit Lesezugriff (RA-GRS)     |Standard_RAGRS         |
|Geozonenredundanter Speicher (GZRS)    |Standard_GZRS         |
|Geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

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

> [!IMPORTANT]
> Wenn Sie [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) verwenden möchten, schließen Sie `--enable-hierarchical-namespace true` in diese Parameterliste ein.

Um ein Speicherkonto vom Typ „Universell v2“ mit einer anderen Replikationsoption zu erstellen, setzen Sie für den Parameter **sku** den gewünschten Wert in der folgenden Tabelle ein.

|Replikationsoption  |sku-Parameter  |
|---------|---------|
|Lokal redundanter Speicher (LRS)     |Standard_LRS         |
|Zonenredundanter Speicher (ZRS)     |Standard_ZRS         |
|Georedundanter Speicher (GRS)     |Standard_GRS         |
|Georedundanter Speicher mit Lesezugriff (RA-GRS)     |Standard_RAGRS         |
|Geozonenredundanter Speicher (GZRS)    |Standard_GZRS         |
|Geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Vorlage](#tab/template)

Sie können entweder Azure PowerShell oder die Azure CLI verwenden, um eine Resource Manager-Vorlage zum Erstellen eines Speicherkontos bereitzustellen. Die in diesem Artikel verwendete Resource Manager-Vorlage stammt von der Seite mit den [Azure Resource Manager-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Wählen Sie zum Ausführen der Skripts **Testen** aus, um Azure Cloud Shell zu öffnen. Klicken Sie zum Einfügen des Skripts mit der rechten Maustaste auf die Shell, und wählen Sie **Einfügen** aus.

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

> [!NOTE]
> Diese Vorlage dient nur als Beispiel. Es gibt viele Speicherkontoeinstellungen, die in dieser Vorlage nicht konfiguriert sind. Wenn Sie z. B. [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) verwenden möchten, ändern Sie diese Vorlage, indem Sie die `isHnsEnabledad`-Eigenschaft des Objekts `StorageAccountPropertiesCreateParameters` auf `true` festlegen. 

Informationen zum Ändern dieser Vorlage und zum Erstellen neuer Vorlagen finden Sie unter:

- [Dokumentation zu Azure Resource Manager](../../azure-resource-manager/index.yml)
- [Vorlagenreferenz für Speicherkonten](/azure/templates/microsoft.storage/allversions)
- [Weitere Beispiele für Speicherkontovorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

---

## <a name="delete-a-storage-account"></a>Löschen von Speicherkonten

Durch das Löschen eines Speicherkontos wird das gesamte Konto gelöscht, einschließlich aller Daten im Konto. Dieser Vorgang kann nicht rückgängig gemacht werden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto.
1. Klicken Sie auf **Löschen**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Löschen des Speicherkontos den Befehl [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount):

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Löschen des Speicherkontos den Befehl [az storage account delete](/cli/azure/storage/account#az-storage-account-delete):

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

Verwenden Sie zum Löschen des Speicherkontos entweder Azure PowerShell oder die Azure CLI.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Alternativ können Sie die Ressourcengruppe löschen, wodurch das Speicherkonto und alle anderen Ressourcen in dieser Ressourcengruppe gelöscht werden. Weitere Informationen zum Löschen einer Ressourcengruppe finden Sie unter [Löschen von Ressourcengruppe und Ressourcen](../../azure-resource-manager/management/delete-resource-group.md).

> [!WARNING]
> Es ist nicht möglich, ein gelöschtes Speicherkonto wiederherzustellen oder Inhalte abzurufen, die das Konto vor dem Löschen enthielt. Sichern Sie alle Inhalte, die Sie speichern möchten, bevor Sie das Konto löschen. Dies gilt auch für alle Ressourcen im Konto – gelöschte Blobs, Tabellen, Warteschlangen oder Dateien können nicht wiederhergestellt werden.
>
> Wenn Sie versuchen, ein Speicherkonto zu löschen, das einem virtuellen Azure-Computer zugewiesen ist, wird unter Umständen ein Fehler mit dem Hinweis angezeigt, dass das Speicherkonto noch verwendet wird. Hilfe zum Beheben dieses Fehlers finden Sie unter [Beheben von Fehlern beim Löschen von Speicherkonten](../../virtual-machines/troubleshooting/index.yml).

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](storage-account-overview.md)
- [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md)
- [Verschieben eines Azure Storage-Kontos in eine andere Region](storage-account-move.md)
- [Wiederherstellen eines gelöschten Speicherkontos](storage-account-recover.md)