---
title: Erstellen einer Azure-Premium-Dateifreigabe
description: In diesem Artikel erfahren Sie, wie Sie eine Azure-Premium-Dateifreigabe erstellen.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 265a1cf0a8a5e1e099a4ec7a9f0d674e0c474dd4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190096"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>So erstellen Sie eine Azure-Premium-Dateifreigabe
Premium-Dateifreigaben (Vorschauversion) werden auf SSD-Speichermedien (Solid-State Disk-Speichermedien) angeboten und eignen sich für E/A-intensive Workloads, einschließlich Hosten von Datenbanken sowie High Performance Computing (HPC). Premium-Dateifreigaben werden in einem speziell dafür vorgesehenen Speicherkontotyp gehostet, der als FileStorage-Konto bezeichnet wird. Premium-Dateifreigaben sind für hochleistungsfähige Anwendungen auf Unternehmensniveau konzipiert, die eine konsistent niedrige Latenz, hohe IOPS-Leistung und hohe Durchsatzraten bieten.

In diesem Artikel wird gezeigt, wie Sie diesen neuen Kontotyp über das [Azure-Portal](https://portal.azure.com/), Azure PowerShell und die Azure-Befehlszeilenschnittstelle (Azure CLI) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Um auf Azure-Ressourcen, einschließlich Azure-Premium-Dateifreigaben zugreifen zu können, benötigen Sie ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Erstellen einer Premium-Dateifreigabe über das Azure-Portal

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

### <a name="create-a-filestorage-preview-storage-account"></a>Erstellen eines FileStorage (Vorschau)-Speicherkontos

Jetzt können Sie Ihr Speicherkonto erstellen.

Jedes Speicherkonto muss zu einer Azure-Ressourcengruppe gehören. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden. In diesem Artikel wird gezeigt, wie Sie eine neue Ressourcengruppe erstellen.

1. Wählen Sie im Azure-Portal im linken Menü **Speicherkonten** aus.

    ![Azure-Portal, Hauptseite „Speicherkonto auswählen“](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Klicken Sie im angezeigten Fenster **Speicherkonten** auf **Hinzufügen**.
1. Wählen Sie das Abonnement aus, in dem das Speicherkonto erstellt werden soll.
1. Wählen Sie unter dem Feld **Ressourcengruppe** die Option **Neu erstellen**. Geben Sie einen Namen für die neue Ressourcengruppe an, wie in der folgenden Abbildung dargestellt.

1. Geben Sie als Nächstes einen Namen für Ihr Speicherkonto ein. Der gewählte Name muss innerhalb von Azure eindeutig sein. Der Name muss ebenfalls zwischen 3 und 24 Zeichen lang sein und darf nur Zahlen und Kleinbuchstaben enthalten.
1. Wählen Sie einen Standort für Ihr Speicherkonto aus, oder verwenden Sie den Standardstandort.
1. Wählen Sie für **Leistung** den Wert **Premium** aus.
1. Wählen Sie **Kontoart** und dann **FileStorage (Vorschau)** aus.
1. Behalten Sie für **Replikation** den Standardwert **Lokal redundanter Speicher (LRS)** bei.

    ![So erstellen ein Speicherkonto für eine Premium-Dateifreigabe](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Wählen Sie **Überprüfen + erstellen**, um die Speicherkontoeinstellungen zu überprüfen und das Konto zu erstellen.
1. Klicken Sie auf **Erstellen**.

Sobald Ihre Speicherkontoressource erstellt wurde, navigieren Sie dorthin.

### <a name="create-a-premium-file-share"></a>Erstellen einer Premium-Dateifreigabe

1. Scrollen Sie im linken Menü für das Speicherkonto zum Abschnitt **Dateidienst**, und wählen Sie **Files (Vorschau)** aus.
1. Wählen Sie **+Dateifreigabe** aus, um eine Premium-Dateifreigabe zu erstellen.
1. Geben Sie einen Namen und das gewünschte Kontingent für Ihre Dateifreigabe ein, und wählen Sie **Erstellen** aus.

> [!NOTE]
> Die bereitgestellten Freigabegrößen werden durch das Kontingent angegeben. Dateifreigaben werden nach der bereitgestellten Größe abgerechnet. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Erstellen einer Premium-Dateifreigabe](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen bereinigen möchten, die im Rahmen dieses Artikels erstellt wurden, können Sie einfach die Ressourcengruppe löschen. Durch das Löschen der Ressourcengruppe werden auch das zugeordnete Speicherkonto sowie alle anderen Ressourcen gelöscht, die der Ressourcengruppe zugeordnet sind.

## <a name="create-a-premium-file-share-using-powershell"></a>Erstellen einer Premium-Dateifreigabe mithilfe von PowerShell

### <a name="create-an-account-using-powershell"></a>Erstellen eines Kontos mithilfe von PowerShell

Installieren Sie zunächst die aktuelle Version des [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)-Moduls.

Aktualisieren Sie anschließend Ihr PowerShell-Modul, melden Sie sich bei Ihrem Azure-Abonnement an, erstellen Sie eine Ressourcengruppe und dann ein Speicherkonto.

### <a name="upgrade-your-powershell-module"></a>Aktualisieren Ihres PowerShell-Moduls

Um aus PowerShell mit einer Premium-Dateifreigabe interagieren zu können, müssen Sie das neueste „Az.Storage“-Modul installieren.

Öffnen Sie zunächst eine PowerShell-Sitzung mit erhöhten Berechtigungen.

Installieren Sie das Modul „Az.Storage“:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Melden Sie sich bei Ihrem Azure-Abonnement an.

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

### <a name="create-a-filestorage-preview-storage-account"></a>Erstellen eines FileStorage (Vorschau)-Speicherkontos

Wenn Sie ein FileStorage (Vorschau)-Speicherkonto mithilfe von PowerShell erstellen möchten, verwenden Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount):

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Erstellen einer Premium-Dateifreigabe

Da Sie jetzt über ein FileStorage-Konto verfügen, können Sie eine Premium-Dateifreigabe erstellen. Verwenden Sie dafür das Cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare).

> [!NOTE]
> Die bereitgestellten Freigabegrößen werden durch das Kontingent angegeben. Dateifreigaben werden nach der bereitgestellten Größe abgerechnet. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Erstellen einer Premium-Dateifreigabe mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI)

Melden Sie sich zum Starten von Azure Cloud Shell beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie sich bei Ihrer lokalen Installation der Befehlszeilenschnittstelle anmelden möchten, führen Sie den folgenden Befehl aus:

```cli
az login
```

### <a name="add-the-preview-storage-cli-extension"></a>Hinzufügen der Vorschau-CLI-Erweiterung für Speicher

Weil Premium-Dateifreigaben eine Previewfunktion (Vorschaufeature) sind, müssen Sie die Vorschauerweiterung zu der Shell hinzufügen. Geben Sie dazu über Cloud Shell oder eine lokale Shell den folgenden Befehl ein: `az extension add --name storage-preview`

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

### <a name="create-a-premium-file-share"></a>Erstellen einer Premium-Dateifreigabe

Da Sie jetzt über ein FileStorage-Konto verfügen, können Sie eine Premium-Dateifreigabe erstellen. Verwenden Sie dafür den Befehl [az storage share create](/cli/azure/storage/share).

> [!NOTE]
> Die bereitgestellten Freigabegrößen werden durch das Kontingent angegeben. Dateifreigaben werden nach der bereitgestellten Größe abgerechnet. Weitere Informationen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Verwenden Sie den Befehl [az group delete](/cli/azure/group), um die Ressourcengruppe und die zugeordneten Ressourcen (einschließlich des neuen Speicherkontos) zu entfernen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Premium-Dateifreigabe erstellt. Wenn Sie sich über die von diesem Konto gebotene Leistung informieren möchten, lesen Sie den Abschnitt „Leistungsstufe“ im Planungshandbuch.

> [!div class="nextstepaction"]
> [Leistungsstufen von Dateifreigaben](storage-files-planning.md#file-share-performance-tiers)