---
title: Erstellen einer Azure-Premium-Dateifreigabe
description: Hier erfahren Sie, wie Sie über das Azure-Portal, im Azure PowerShell-Modul oder mit der Azure CLI eine Azure Premium-Dateifreigabe erstellen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1ec8e4a945f8b8277d05c11bf3673d2e4ab15f9a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626791"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Erstellen einer Azure Premium-Dateifreigabe

Premium-Dateifreigaben werden auf SSD-Speichermedien (Solid-State Disk) angeboten und eignen sich für E/A-intensive Workloads, z. B. Hosten von Datenbanken und High Performance Computing (HPC). Premium-Dateifreigaben werden in einem speziell dafür vorgesehenen Speicherkontotyp gehostet, der als FileStorage-Konto bezeichnet wird. Premium-Dateifreigaben sind für hochleistungsfähige Anwendungen auf Unternehmensniveau konzipiert, die eine konsistent niedrige Latenz, hohe IOPS-Leistung und hohe Durchsatzraten bieten.

In diesem Artikel wird gezeigt, wie Sie diesen neuen Kontotyp über das [Azure-Portal](https://portal.azure.com/), im Azure PowerShell-Modul und mit der Azure CLI erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Falls Sie die Azure CLI verwenden möchten, [installieren Sie die neueste Version](/cli/azure/install-azure-cli?view=azure-cli-latest).
- Wenn Sie das Azure PowerShell-Modul verwenden möchten, [installieren Sie die neueste Version](/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Erstellen eines FileStorage-Speicherkontos

Jedes Speicherkonto muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. Premium-Dateifreigaben erfordern ein FileStorage-Konto.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

Jetzt können Sie Ihr Speicherkonto erstellen.

1. Wählen Sie im Azure-Portal im linken Menü **Speicherkonten** aus.

    ![Azure-Portal, Hauptseite „Speicherkonto auswählen“](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Klicken Sie im angezeigten Fenster **Speicherkonten** auf **Hinzufügen**.
1. Wählen Sie das Abonnement aus, in dem das Speicherkonto erstellt werden soll.
1. Wählen Sie unter dem Feld **Ressourcengruppe** die Option **Neu erstellen**. Geben Sie einen Namen für die neue Ressourcengruppe an, wie in der folgenden Abbildung dargestellt.

1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.
1. Wählen Sie einen Standort für Ihr Speicherkonto aus, oder verwenden Sie den Standardstandort.
1. Wählen Sie für **Leistung** den Wert **Premium** aus.

    Sie müssen **Premium** auswählen, damit **FileStorage** in der Dropdownliste **Kontoart** verfügbar ist.

1. Wählen Sie **Kontoart** und dann **FileStorage** aus.
1. Behalten Sie für **Replikation** den Standardwert **Lokal redundanter Speicher (LRS)** bei.

    ![So erstellen ein Speicherkonto für eine Premium-Dateifreigabe](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.
1. Klicken Sie auf **Erstellen**.

Sobald Ihre Speicherkontoressource erstellt wurde, navigieren Sie dorthin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Verwenden Sie den Befehl `Connect-AzAccount`, und folgen Sie den Anweisungen auf dem Bildschirm, um sich zu authentifizieren.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Wenn Sie eine neue Ressourcengruppe mithilfe von PowerShell erstellen möchten, verwenden Sie den Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Erstellen eines FileStorage-Speicherkontos

Wenn Sie ein FileStorage-Speicherkonto mit PowerShell erstellen möchten, verwenden Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount):

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der CLI anmelden möchten, stellen Sie zuerst sicher, dass Sie über die aktuelle Version verfügen, und melden sich dann erst an:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Wenn Sie eine neue Ressourcengruppe über die Azure-Befehlszeilenschnittstelle erstellen möchten, verwenden Sie den Befehl [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Erstellen eines FileStorage-Speicherkontos

Wenn Sie ein FileStorage-Speicherkonto über die Azure-Befehlszeilenschnittstelle erstellen möchten, verwenden Sie den Befehl [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Abrufen des Speicherkontoschlüssels

Speicherkontoschlüssel steuern den Zugriff auf Ressourcen in einem Speicherkonto. In diesem Artikel verwenden wir den Schlüssel zum Erstellen einer Premium-Dateifreigabe. Die Schlüssel werden automatisch erstellt, wenn Sie ein Speicherkonto erstellen. Sie können die Speicherkontoschlüssel für Ihr Speicherkonto mit dem Befehl [az storage account keys list](/cli/azure/storage/account/keys) abrufen:

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Erstellen einer Premium-Dateifreigabe

Nachdem Sie ein FileStorage-Konto erstellt haben, können Sie jetzt in diesem Konto eine Premium-Dateifreigabe erstellen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Scrollen Sie im linken Menü für das Speicherkonto zum Abschnitt **Dateidienst**, und wählen Sie **Files**.
1. Wählen Sie **Dateifreigabe** aus, um eine Premium-Dateifreigabe zu erstellen.
1. Geben Sie einen Namen und das gewünschte Kontingent für Ihre Dateifreigabe ein, und wählen Sie **Erstellen** aus.

> [!NOTE]
> Die möglichen Freigabegrößen werden durch das Kontingent bestimmt, die Abrechnung erfolgt gemäß der bereitgestellten Größe. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Erstellen einer Premium-Dateifreigabe](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Im Azure PowerShell-Modul verwenden Sie das Cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) zum Erstellen einer Premium-Dateifreigabe.

> [!NOTE]
> Die möglichen Freigabegrößen werden durch das Kontingent bestimmt, die Abrechnung erfolgt gemäß der bereitgestellten Größe. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

In der Azure CLI verwenden Sie den Befehl [az storage share create](/cli/azure/storage/share) zum Erstellen einer Premium-Dateifreigabe.

> [!NOTE]
> Die möglichen Freigabegrößen werden durch das Kontingent bestimmt, die Abrechnung erfolgt gemäß der bereitgestellten Größe. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie die Ressourcen bereinigen möchten, die im Rahmen dieses Artikels erstellt wurden, löschen Sie die Ressourcengruppe. Durch das Löschen der Ressourcengruppe werden auch das zugeordnete Speicherkonto sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie die Ressourcen bereinigen möchten, die im Rahmen dieses Artikels erstellt wurden, löschen Sie die Ressourcengruppe. Durch das Löschen der Ressourcengruppe werden auch das zugeordnete Speicherkonto sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

Verwenden Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie die Ressourcen bereinigen möchten, die im Rahmen dieses Artikels erstellt wurden, löschen Sie die Ressourcengruppe. Durch das Löschen der Ressourcengruppe werden auch das zugeordnete Speicherkonto sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

Verwenden Sie den Befehl [az group delete](/cli/azure/group), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Premium-Dateifreigabe erstellt. Wenn Sie sich über die von diesem Konto gebotene Leistung informieren möchten, lesen Sie den Abschnitt „Leistungsstufe“ im Planungshandbuch.

> [!div class="nextstepaction"]
> [Speicherebenen](storage-files-planning.md#storage-tiers)